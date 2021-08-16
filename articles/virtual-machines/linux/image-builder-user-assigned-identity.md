---
title: 创建虚拟机映像并使用用户分配的托管标识来访问 Azure 存储中的文件
description: 使用 Azure 映像生成器创建虚拟机映像，该映像可以使用用户分配的托管标识来访问 Azure 存储中存储的文件。
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 94bde3351f74c3388e137cc738f2b970654416bd
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439595"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>创建映像并使用用户分配的托管标识来访问 Azure 存储中的文件 

Azure 映像生成器支持使用脚本或从多个位置（例如 GitHub 和 Azure 存储等）复制文件。要使用这些文件，必须可以从外部通过 Azure 映像生成器来访问这些文件，但你可以使用 SAS 令牌保护 Azure 存储 blob。

本文演示如何使用 Azure VM 映像生成器来创建自定义映像，其中服务将使用[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)来访问 Azure 存储中用于映像定制的文件，你无需将这些文件设为可公共访问，也不必设置 SAS 令牌。

在下面的示例中，你将创建两个资源组，一个用于自定义映像，另一个将托管包含脚本文件的 Azure 存储帐户。 这模拟了一个真实场景，即，你可能在映像生成器之外的不同存储帐户内具有生成项目或映像文件。 你将创建用户分配的标识，然后授予对该脚本文件的读取权限，但不会设置对该文件的任何公共访问权限。 然后将使用 Shell 定制器从存储帐户下载并运行该脚本。


## <a name="register-the-features"></a>注册功能
若要使用 Azure 映像生成器，需要注册该功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查注册。


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

如果未显示“已注册”，请运行以下命令：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="create-a-resource-group"></a>创建资源组

我们将重复使用某些信息，因此我们将创建一些变量来存储这些信息。


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

为你的订阅 ID 创建变量。 可使用 `az account show | grep id` 获取。

```console
subscriptionID=<Your subscription ID>
```

同时为映像和脚本存储创建资源组。

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

创建用户分配的标识，并在资源组上设置权限。

映像生成器使用提供的[用户标识](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)将映像注入到资源组中。 在本示例中，你将创建一个 Azure 角色定义，其中包含分发映像时要执行的精细操作。 然后将此角色定义分配给用户标识。

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

创建存储，从 GitHub 将示例脚本复制到该存储中。

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

授予映像生成器在映像资源组中创建资源的权限。 `--assignee` 值是用户标识 ID。

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>修改示例

下载示例 .json 文件，并使用先前创建的变量对其进行配置。

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>创建映像

将映像配置提交到 Azure 映像生成器服务。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

启动映像生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

等待生成完成。 这可能需要大约 15 分钟。

## <a name="create-a-vm"></a>创建 VM

从映像创建 VM。 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

创建 VM 后，启动与 VM 的 SSH 会话。

```console
ssh aibuser@<publicIp>
```

建立 SSH 连接后，应会立即看到映像已使用当天的一个消息进行了自定义！

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>清理

完成后，如果不再需要资源，可以将其删除。

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>后续步骤

如果在使用 Azure 映像生成器时遇到任何问题，请参阅[故障排除](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)。
