---
title: 使用 Azure 映像生成器从现有映像版本创建新的 VM 映像版本
description: 使用 Linux 中的 Azure 映像生成器从现有映像版本创建新的 VM 映像版本。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 85f41327476ae96af8dcc28eaaa3e49ef3ce80e5
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113596526"
---
# <a name="create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>使用 Linux 中的 Azure 映像生成器从现有映像版本创建新的 VM 映像版本

本文介绍如何使用[共享映像库](../shared-image-galleries.md)中的现有映像版本，对其进行更新，并将其作为新的映像版本发布到库。

我们将使用一个示例 .json 模板来配置映像。 要使用的 .json 文件为：[helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)。 


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


## <a name="set-variables-and-permissions"></a>设置变量和访问权限

如果使用了[创建映像并将其分发到共享映像库](image-builder-gallery.md)来创建共享映像库，则已创建所需的部分变量。 如果没有，请设置要用于此示例的一些变量。


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

为你的订阅 ID 创建变量。 可使用 `az account show | grep id` 获取。

```console
subscriptionID=<Subscription ID>
```

获取要更新的映像版本。

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>创建用户分配的标识，并在资源组上设置权限
已在上一示例中设置了用户身份，因此只需获取其资源 ID，然后将其追加到模板即可。

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

如果你已经拥有自己的共享映像库，但没有按前面的示例进行操作，则需要为映像生成器分配对资源组的访问权限，便于它访问该映像库。 请查看[创建映像并将其分发到共享映像库](image-builder-gallery.md)示例中的步骤。


## <a name="modify-helloimage-example"></a>修改 helloImage 示例
通过打开此处的 .json 文件 [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) 以及[映像生成器模板参考](image-builder-json.md)，你可以查看要使用的示例。 


下载 .json 示例，并使用变量对其进行配置。 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>创建映像

将映像配置提交到 VM 映像生成器服务。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

启动映像生成。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

等待映像生成和复制完成后，再进行下一步操作。


## <a name="create-the-vm"></a>创建 VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

使用 VM 的公共 IP 地址创建与 VM 的 SSH 连接。

```console
ssh azureuser@<pubIp>
```

建立 SSH 连接后，应会立即看到映像已使用当天的一个消息进行了自定义。

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

键入 `exit` 以关闭 SSH 连接。

你还可以列出库中目前可用的映像版本。

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>后续步骤

若要详细了解本文中使用的 .json 文件的组件，请参阅[映像生成器模板参考](../linux/image-builder-json.md)。
