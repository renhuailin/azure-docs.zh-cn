---
title: 映像生成器 - 创建 Windows 虚拟桌面映像
description: 在 PowerShell 中使用 Azure 映像生成器创建 Windows 虚拟桌面的 Azure VM 映像。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 05/12/2021
ms.topic: article
ms.service: virtual-machines
ms.collection: windows
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fdda25ecc9d111d626b7c9836757c31d4972c25f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692442"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>使用 Azure VM 映像生成器和 PowerShell 创建 Windows 虚拟桌面映像

适用于：:heavy_check_mark: Windows VM 

本文介绍如何使用以下自定义创建 Windows 虚拟桌面映像：

* 安装 [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1)。
* 从社区存储库运行 [Windows 虚拟桌面优化脚本](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool)。
* 安装 [Microsoft Teams](../../virtual-desktop/teams-on-avd.md)。
* [重启](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* 运行 [Windows 更新](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer)

我们将向你展示如何使用 Azure VM 映像生成器自动完成此操作，并将映像分发到[共享映像库](../shared-image-galleries.md)，你可以在此库中将映像复制到其他区域、控制规模以及在组织内部和外部分享映像。


为了简化映像生成器配置的部署，本示例使用嵌套了图像生成器模板的 Azure 资源管理器模板。 这可以提供一些其他优点，例如变量和参数输入。 还可以从命令行传递参数。

本文旨在作为复制和粘贴练习。

> [!NOTE]
> 用于安装应用的脚本位于 [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD) 上。 它们仅用于说明和测试，不用于生产工作负荷。 

## <a name="tips-for-building-windows-images"></a>Windows 映像生成技巧 

- VM 大小 - 默认 VM 大小为 `Standard_D1_v2`，不适用于 Windows。 请使用 `Standard_D2_v2` 或更大大小。
- 此示例使用 [PowerShell 定制器脚本](../linux/image-builder-json.md)。 你需要使用这些设置，否则生成将停止响应。

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    例如：

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- 注释代码 - AIB 生成日志 (customization.log) 非常详细，如果你使用“write-host”注释脚本，这些操作将发送到日志，并使故障排除变得更简单。

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- 退出代码 - AIB 要求所有脚本返回 0 退出代码，任何非零退出代码都将导致 AIB 自定义失败并停止生成。 如果脚本复杂，请添加检测并发出退出代码，这些代码将显示在 customization.log 中。

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- 测试：请提前在独立 VM 上测试你的代码，确保没有用户提示，你使用的权限正确，等等。

- 网络 - `Set-NetAdapterAdvancedProperty`。 已在优化脚本中设定此设置，但 AIB 生成失败，因为其断开了网络连接，此条目被注释掉。正在调查此问题。

## <a name="prerequisites"></a>先决条件

必须安装最新的 Azure PowerShell Cmdlet，请参阅 [Azure PowerShell 概述](/powershell/azure/overview)，了解安装详细信息。

```PowerShell
# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not show as registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>设置环境和变量

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>权限、用户标识和角色 


 创建用户标识。

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

为标识分配权限以分发映像。 此命令将下载模板，并使用前面指定的参数更新模板。

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> 如果看到此错误：“New-AzRoleDefinition: 已超出角色定义限制。 无法创建更多的角色定义。” 请参阅 [Azure RBAC 故障排除](../../role-based-access-control/troubleshooting.md)。


## <a name="create-the-shared-image-gallery"></a>创建共享映像库 

如果还没有共享映像库，需要创建一个。

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>配置映像模板

在此示例中，我们的模板已准备就绪，可以下载并使用之前指定的参数进行更新，它将安装 FsLogix、操作系统优化、Microsoft Teams，最后将运行 Windows 更新。

如果打开模板，则可以在源属性中看到正在使用的映像，在此示例中，它使用的是 Win 10 多会话映像。 

### <a name="windows-10-images"></a>Windows 10 映像
应注意两种密钥类型：多会话和单会话。

多会话映像适用于共用使用。 Azure 中的映像详细信息示例如下：

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

单会话映像适用于单独使用。 Azure 中的映像详细信息示例如下：

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

还可以更改可用的 Win10 映像：

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>下载模板和配置

现在，需要下载模板，并对其进行配置以供使用。

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

可以随意查看[模板](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)，所有代码都是可查看的。


## <a name="submit-the-template"></a>提交模板

模板必须提交给服务，此操作会下载任何依赖项目（如脚本），验证，检查权限，并将其存储在带有“IT_”前缀的暂存资源组中。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>生成映像
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> 命令不会等待映像生成器服务完成映像生成，你可以在下面查询状态。

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>创建 VM
生成完成后，可以从映像生成 VM，使用 [New-AzVM (Az.Compute)](/powershell/module/az.compute/new-azvm#examples) 中的示例。

## <a name="clean-up"></a>清理

首先删除资源组模板，而不是只删除整个资源组，否则 AIB 使用的暂存资源组 (IT_) 将无法清除。

删除映像模板。

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

删除角色分配。

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

删除该资源组。

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

你可以在 [GitHub](https://github.com/azure/azvmimagebuilder/tree/master/quickquickstarts) 上试用更多示例。

