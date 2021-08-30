---
title: 使用 Azure Resource Graph (ARG) 查询备份
description: 详细了解如何使用 Azure Resource Graph (ARG) 查询有关 Azure 资源的备份的信息。
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 252c921ce911777315ab043501359b5eb74cf176
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733259"
---
# <a name="query-your-backups-using-azure-resource-graph-arg"></a>使用 Azure Resource Graph (ARG) 查询备份

可以使用 [Azure Resource Graph (ARG)](../governance/resource-graph/overview.md) 来查询有关 Azure 资源的备份的信息，而且这不会产生额外的成本。 ARG 是可以扩展 Azure 资源管理的 Azure 服务。 它旨在提供高效的资源探索，并且能够跨一组给定的订阅进行大规模查询。 下面是使用 ARG 查询备份元数据的主要优势：

- 能够按资源属性进行复杂的筛选、分组和排序，以此大规模查询资源。
- 能够获取有关备份（包括正在进行的备份作业）的实时信息。
- 能够将备份相关的数据与相关 Azure 资源（例如 Azure 虚拟机和存储帐户）的有用信息联接到一起。

## <a name="getting-started"></a>入门

若要开始使用 ARG 查询备份，请执行以下步骤：

1. 在 Azure 门户中搜索“Resource Graph Explorer”。 选择“Resource Graph Explorer”以重定向到 ARG 查询编辑器。
    
    ![搜索 Azure 资源组](./media/query-backups-using-azure-resource-graph/search-resource-graph-explorer.png)

    左侧窗格将显示可供查询的所有表（及其关联的架构）。
    
    - RecoveryServicesResources 表包含大部分的备份相关记录，例如作业详细信息、备份实例详细信息。  等等。
    - Resources 表包含有关所有顶级 Azure 资源（例如恢复服务保管库、Azure 虚拟机、存储帐户等）的信息。

    ![可供查询的表和关联的架构](./media/query-backups-using-azure-resource-graph/tables-and-associated-schemas.png)

1. 若要浏览其中任何一个表中的数据，请在查询编辑器中编写 Kusto 查询，然后单击“运行查询” 。

    可以从 Resource Graph Explorer 以 CSV 格式下载这些查询的输出。 还可以使用 ARG 支持的任何自动化客户端（例如 [PowerShell](../governance/resource-graph/first-query-powershell.md)、[CLI](../governance/resource-graph/first-query-azurecli.md) 或 [SDK](../governance/resource-graph/first-query-python.md)）在自定义自动化中使用这些查询。 还可以使用 ARG 在 Azure 门户中创建[自定义工作簿](../azure-monitor/visualize/workbooks-overview.md)作为数据源。

>[!NOTE] 
>- ARG 提供过去最长 14 天的备份/还原作业用于查询。 如果你要查询历史记录，我们建议使用 Azure Monitor 日志。
>- ARG 允许你查询你对其拥有适当 RBAC 权限的资源。

## <a name="sample-queries"></a>示例查询

下面是针对备份数据运行的一些示例 ARG 查询，可以在自定义仪表板和自动化中使用这些查询。

### <a name="list-all-azure-vms-that-have-been-configured-for-backup"></a>列出所有已配置了备份的 Azure VM

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupInstances',split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend dataSourceType = case(type=~'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupInstances',properties.dataSourceSetInfo.datasourceType,'--')
| extend friendlyName = properties.friendlyName
| extend dsResourceGroup = split(split(properties.dataSourceInfo.resourceID, '/resourceGroups/')[1],'/')[0]
| extend dsSubscription = split(split(properties.dataSourceInfo.resourceID, '/subscriptions/')[1],'/')[0]
| extend lastRestorePoint = properties.lastRecoveryPoint
| extend primaryLocation = properties.dataSourceInfo.resourceLocation
| extend policyName = case(type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.policyName, type =~ 'microsoft.dataprotection/backupVaults/backupInstances', properties.policyInfo.name, '--')
| extend protectionState = properties.currentProtectionState
| where protectionState in~ ('ConfiguringProtection','ProtectionConfigured','ConfiguringProtectionFailed','ProtectionStopped','SoftDeleted','ProtectionError')
| where (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('AzureIaasVM')) //add the relevant subscription ids you wish to query to this line

```

### <a name="list-all-backup-jobs-on-azure-databases-for-postgresql-servers-in-the-last-one-week"></a>列出过去一周在 Azure Databases for PostgreSQL 服务器上执行的所有备份作业

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/backupVaults/backupJobs')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
| extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
| extend backupInstanceName = properties.backupInstanceId
| extend dsResourceGroup = split(split(properties.dataSourceId, '/resourceGroups/')[1],'/')[0]| extend dsSubscription = split(split(properties.dataSourceId, '/subscriptions/')[1],'/')[0]
| extend status = properties.status
| extend dataSourceId = properties.dataSourceId
| extend primaryLocation = properties.dataSourceLocation
| extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'),startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration
| project id, name, friendlyName, resourceGroup, vaultName, dataSourceType, operation, jobStatus, startTime, duration, backupInstanceName, dsResourceGroup, dsSubscription, status, primaryLocation, dataSourceId
| where  (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases')) and (status in~ ('Started','InProgress','Succeeded','Completed','Failed','CompletedWithWarnings')) and (operation in~ ('adhocBackup','backup')) //add the relevant subscription ids you wish to query to this line
| where (startTime >= ago(7d))

```

### <a name="list-all-azure-vms-that-have-not-been-configured-for-backup"></a>列出所有未配置备份的 Azure VM

```kusto
Resources
| where type in~ ('microsoft.compute/virtualmachines','microsoft.classiccompute/virtualmachines') 
| extend resourceId=tolower(id) 
| join kind = leftouter ( RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| where properties.backupManagementType == "AzureIaasVM"
| project resourceId = tolower(tostring(properties.sourceResourceId)), backupItemid = id, isBackedUp = isnotempty(id) ) on resourceId 
| extend isProtected = isnotempty(backupItemid)
| where (isProtected == (0))
| project id,name,resourceGroup,location,tags

```

### <a name="list-all-backup-policies-used-for-azure-vms"></a>列出所有用于 Azure VM 的备份策略

```kusto
RecoveryServicesResources
| where type == 'microsoft.recoveryservices/vaults/backuppolicies'
| extend vaultName = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', properties.backupManagementType,type == 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where datasourceType == 'AzureIaasVM'
```

### <a name="list-all-backup-policies-used-for-azure-databases-for-postgresql-servers"></a>列出所有用于 Azure Databases for PostgreSQL 服务器的备份策略

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/BackupVaults/backupPolicies')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupPolicies', split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'microsoft.recoveryservices/vaults/backupPolicies', split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupPolicies', properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where (datasourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases'))

```

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Resource Graph](../governance/resource-graph/overview.md)
