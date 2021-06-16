---
title: Azure PowerShell 示例 - 重置 Azure 云服务（外延支持）
description: 用于重置 Azure 云服务（外延支持）部署的示例脚本
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e571a6cfae82274c2e4f3d85540c9724edb657a5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705173"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>重置 Azure 云服务（外延支持） 
这些示例涵盖了重置现有 Azure 云服务（外延支持）部署的各种方式。

## <a name="reimage-role-instances-of-cloud-service"></a>重置云服务角色实例的映像
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
此命令会重置 ContosoCS 资源组中 ContosoCS 云服务的 2 个角色实例（ContosoFrontEnd_IN_0 和 ContosoBackEnd_IN_1）的映像。

## <a name="reimage-all-roles-of-cloud-service"></a>重置云服务的所有角色的映像
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
此命令会重置 ContosOrg 资源组中 ContosoCS 云服务的所有角色实例的映像。

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>重置云服务的单个角色实例的映像
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
此命令会重置 ContosOrg 资源组中 ContosoCS 云服务的 ContosoFrontEnd_IN_0 角色实例的映像。

## <a name="rebuild-role-instances-of-cloud-service"></a>重新生成云服务的角色实例
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
此命令会重新生成 ContosoCS 资源组中 ContosoCS 云服务的 2 个角色实例（ContosoFrontEnd_IN_0 和 ContosoBackEnd_IN_1）。

## <a name="rebuild-all-roles-of-cloud-service"></a>重新生成云服务的所有角色
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
此命令会重新生成 ContosOrg 资源组中 ContosoCS 云服务的所有角色实例。

## <a name="restart-role-instances-of-cloud-service"></a>重启云服务的角色实例
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
此命令会重启 ContosoCS 资源组中 ContosoCS 云服务的 2 个角色实例（ContosoFrontEnd_IN_0 和 ContosoBackEnd_IN_1）。

## <a name="restart-all-roles-of-cloud-service"></a>重启云服务的所有角色
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
此命令会重启 ContosOrg 资源组中 ContosoCS 云服务的所有角色实例。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 云服务（外延支持）的详细信息，请参阅 [Azure 云服务（外延支持）概述](overview.md)。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)
