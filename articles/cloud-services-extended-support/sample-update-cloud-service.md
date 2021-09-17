---
title: Azure PowerShell 示例 - 更新 Azure 云服务（外延支持）
description: 用于更新 Azure 云服务（外延支持）部署的示例脚本
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22d02ef7c395ae6f7cdbbf739ed5107436bd169c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751098"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>更新 Azure 云服务（外延支持）

这些示例涵盖了更新现有 Azure 云服务（外延支持）部署的各种方式。

## <a name="add-an-extension-to-existing-cloud-service"></a>将扩展添加到现有云服务
下面的命令集将 RDP 扩展添加到名为 ContosoCS 的现有云服务，该服务属于名为 ContosOrg 的资源组。
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing cloud service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>从云服务中删除所有扩展
下面的命令集将所有扩展从名为 ContosoCS 的现有云服务中删除，该服务属于名为 ContosOrg 的资源组。
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfile.Extension = @()
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>从云服务中删除远程桌面扩展
下面的命令集将 RDP 扩展从名为 ContosoCS 的现有云服务中删除，该服务属于名为 ContosOrg 的资源组。
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Name -ne "RDPExtension" }
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>横向扩展/横向缩减实例
下面的命令集显示如何为名为 ContosoCS 的云服务（属于名为 ContosOrg 的资源组）横向扩展和横向缩减角色实例计数。
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfile.Role | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfile.Role | Where-Object {$_.Name -eq "ContosoFrontend"}
$role.SkuCapacity -= 1

# Update cloud service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>后续步骤
有关 Azure 云服务（外延支持）的详细信息，请参阅 [Azure 云服务（外延支持）概述](overview.md)。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)
