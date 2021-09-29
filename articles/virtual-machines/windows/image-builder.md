---
title: 使用 Azure 映像生成器创建 Windows VM
description: 使用 Azure 映像生成器创建 Windows VM。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/23/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 83deba3d6fe63f5a63bda04556b911168527b065
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553054"
---
# <a name="create-a-windows-vm-with-azure-image-builder"></a>使用 Azure 映像生成器创建 Windows VM

**适用于：** :heavy_check_mark: Windows VM 

本文介绍如何使用 Azure VM 映像生成器创建自定义的 Windows 映像。 本文中的示例使用[自定义程序](../linux/image-builder-json.md#properties-customize)来自定义映像：
- PowerShell (ScriptUri) - 下载并运行 [PowerShell 脚本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重启 - 重启 VM。
- PowerShell（内联）- 运行特定的命令。 本示例将使用 `mkdir c:\\buildActions` 在 VM 上创建一个目录。
- 文件 - 将 GitHub 中的文件复制到 VM。 本示例将 [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 复制到 VM 上的 `c:\buildArtifacts\index.html` 中。
- buildTimeoutInMinutes - 增大生成时间，使长时间运行的生成能够完成。默认值为 240 分钟，但你可以增大生成时间，使长时间运行的生成能够完成。
- vmProfile - 指定 vmSize 和 Network 属性
- osDiskSizeGB - 可以增大映像的大小
- identity - 提供在生成过程中要使用的 Azure 映像生成器标识


也可以指定 `buildTimeoutInMinutes`。 默认值为 240 分钟，但你可以增大生成时间，使长时间运行的生成能够完成。 允许的最小值为 6 分钟；更短的值会导致错误。

我们将使用一个示例 .json 模板来配置映像。 我们将使用的 .json 文件位于：[helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)。 



> [!NOTE]
> 对于 Windows 用户，可使用 Bash 在 [Azure Cloud Shell](https://shell.azure.com) 上运行以下 Azure CLI 示例。


## <a name="register-the-features"></a>注册功能

若要使用 Azure 映像生成器，则需要注册此功能。

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


## <a name="set-variables"></a>设置变量

我们将重复使用某些信息，因此我们将创建一些变量来存储这些信息。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

为你的订阅 ID 创建变量。

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```
## <a name="create-a-resource-group"></a>创建资源组
此资源组用于存储映像配置模板项目和映像。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>创建用户分配的标识，并在资源组上设置权限
映像生成器使用提供的 [user-identity](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) 将映像注入到资源组中。 在本示例中，你将创建一个 Azure 角色定义，其中包含分发映像时要执行的精细操作。 然后将此角色定义分配给用户标识。

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>创建用户分配的托管标识并授予权限 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>下载映像配置模板示例

我们已经创建了一个参数化映像配置模板供你试用。 请下载示例 .json 文件，并使用先前设置的变量对其进行配置。

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

可以使用 `vi` 等文本编辑器在终端中修改此示例。

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> 对于源映像，始终必须[指定一个版本](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)，而不能使用 `latest`。
> 如果添加或更改映像所分发到的资源组，则确保必须对资源组[设置权限](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group)。
 
## <a name="create-the-image"></a>创建映像

将映像配置提交到 VM 映像生成器服务

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完成后，此命令会在控制台中返回成功消息，并在 `$imageResourceGroup` 中创建 `Image Builder Configuration Template`。 如果启用了“显示隐藏的类型”，则可以在 Azure 门户上的资源组中看到此资源。

在后台，映像生成器还会在你的订阅中创建一个暂存资源组。 此资源组用于映像生成过程。 它采用以下格式：`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 切勿直接删除暂存资源组。 请先删除映像模板项目，这样就会删除暂存资源组。

如果在提交映像配置模板期间服务报告失败：
-  请查看这些[故障排除](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors)步骤。 
- 在重试提交之前，需使用以下代码片段删除模板。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

## <a name="start-the-image-build"></a>启动映像生成
使用 [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) 启动映像生成过程。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等待生成完成。 这可能需要大约 15 分钟。

如果遇到任何错误，请查看这些[故障排除](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors)步骤。


## <a name="create-the-vm"></a>创建 VM

使用生成的映像创建 VM。 请将 \<password> 替换为你自己的、在 VM 上使用的 `aibuser` 的密码。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>验证自定义

使用创建 VM 时设置的用户名和密码创建与 VM 的远程桌面连接。 在 VM 中，打开命令提示符并键入以下内容：

```console
dir c:\
```

在自定义映像期间，应会看到创建了以下两个目录：
- buildActions
- buildArtifacts

## <a name="clean-up"></a>清理

完成后，请删除资源。

### <a name="delete-the-image-builder-template"></a>删除映像生成器模板

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>删除角色分配、角色定义和用户标识 (user-identity)。
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>删除映像资源组

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>后续步骤

若要详细了解本文中使用的 .json 文件的组件，请参阅[映像生成器模板参考](../linux/image-builder-json.md)。
