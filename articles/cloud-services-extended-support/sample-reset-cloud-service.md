---
title: Azure PowerShell 示例 - 重置 Azure 云服务（外延支持）
description: 用于重置 Azure 云服务（外延支持）部署的示例脚本
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881479"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>重置 Azure 云服务（外延支持） 
这些示例涵盖了重置现有 Azure 云服务（外延支持）部署的各种方式。

## <a name="reimage-role-instances-of-cloud-service"></a>重置云服务角色实例的映像
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
此命令会重置 ContosOrg 资源组中 ContosoCS 云服务的 2 个角色实例（ContosoFrontEnd\_IN\_0 和 ContosoBackEnd\_IN\_1）的映像 。

## <a name="reimage-all-roles-of-cloud-service"></a>重置云服务的所有角色的映像
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>重置云服务的单个角色实例的映像
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>重启云服务的单个角色实例
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>后续步骤

- 有关 Azure 云服务（外延支持）的详细信息，请参阅 [Azure 云服务（外延支持）概述](overview.md)。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)