---
title: Azure Monitor 日志专用群集
description: 每天引入超过 1 TB 监视数据的客户可以使用专用群集，而不是共享群集
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 3b4a98e37c16feeb2ad8203caaeb5bc231761379
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112004216"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor 日志专用群集

Azure Monitor 日志专用群集是一个部署选项，可为 Azure Monitor 日志客户启用高级功能。 具有专用群集的客户可以选择在这些群集上托管的工作区。

需要专用群集的功能包括：

- **[客户管理的密钥](../logs/customer-managed-keys.md)** - 使用由客户提供和控制的密钥对群集数据进行加密。
- **[密码箱](../logs/customer-managed-keys.md#customer-lockbox-preview)** - 客户可以控制 Microsoft 支持工程师的数据访问请求。
- **[双重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** 可以在其中一种加密算法或密钥可能被泄露的情况下提供保护。 在这种情况下，附加的加密层会继续保护你的数据。
- **[多工作区](../logs/cross-workspace-query.md)** - - 如果客户使用多个工作区进行生产，则使用专用群集可能是合理的。 如果所有工作区都在同一群集上，则“跨工作区”查询会运行更快。 使用专用群集还可能更具成本效益，因为分配的承诺层级考虑了所有群集引入并应用于其所有工作区，即使其中一些工作区很小并且没有资格享受承诺层级折扣。

专用群集要求客户使用每天至少 1 TB 的数据引入产能进行提交。 迁移到专用群集很简单。 无数据丢失或服务中断。 

## <a name="management"></a>管理 

专用群集通过表示 Azure Monitor 日志群集的 Azure 资源进行管理。 所有操作都是使用 PowerShell 或 REST API 在该资源上完成的。

创建群集后，可以对其进行配置并将工作区链接到该群集。 当工作区链接到群集时，发送到工作区的新数据都将驻留在群集上。 只有与群集位于同一区域中的工作区才能链接到群集。 可从群集中取消工作区的链接，但有一些限制。 本文将详细介绍这些限制。 

引入到专用群集的数据进行两次加密 — 一次在服务级别使用 Microsoft 管理的密钥或[客户管理的密钥](../logs/customer-managed-keys.md)，一次在基础结构级别使用两种不同的加密算法和两个不同的密钥。 [双重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)可以在其中一种加密算法或密钥可能被泄露的情况下提供保护。 在这种情况下，附加的加密层会继续保护你的数据。 专用群集还允许通过[密码箱](../logs/customer-managed-keys.md#customer-lockbox-preview)控制来保护数据。

群集级别的所有操作都需要群集上的 `Microsoft.OperationalInsights/clusters/write` 操作权限。 可以通过包含 `*/write` 操作的所有者或参与者或包含 `Microsoft.OperationalInsights/*` 操作的 Log Analytics 参与者角色授予此权限。 有关 Log Analytics 权限的更多信息，请参阅[管理对 Azure Monitor 中的日志数据和工作区的访问](./manage-access.md)。 


## <a name="cluster-pricing-model"></a>群集定价模型

Log Analytics 专用群集使用承诺层级定价模型，该模型至少为 1000 GB/天。 高于该层级别的任何使用量都将按该承诺层的每 GB 有效费率计费。  [Azure Monitor 定价页]( https://azure.microsoft.com/pricing/details/monitor/)提供了承诺层级定价信息。  

使用 `Sku` 下的 `Capacity` 参数，通过 Azure 资源管理器以编程方式配置群集承诺层级别。 `Capacity` 以 GB 为单位指定，其值可以是 1000、2000 或 5000 GB/天。

对于群集上的使用情况，有两种计费模式。 配置群集时，可通过 `billingType` 参数指定这些计费模式。 

1. **群集**：在此情况下（其为默认情况），引入数据的计费在群集级别完成。 每个与群集关联的工作区中的引入数据数量将进行聚合，以计算该群集的每日帐单。 

2. **工作区**：群集的承诺层级成本按比例归属于群集中的工作区，按每个工作区的数据引入量计算（在计算了 [Azure 安全中心](../../security-center/index.yml)的每个工作区的每个节点的分配之后）。此定价模型的完整详细信息在[此处]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)说明。 

如果工作区使用旧的每节点定价层，则当其链接到群集时，它将根据群集的承诺层级引入到的数据来计费，而不再是按节点计费。 将继续应用来自 Azure 安全中心的按节点数据分配。

[此处]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)提供了 Log Analytics 专用群集计费的完整详细信息。

## <a name="asynchronous-operations-and-status-check"></a>异步操作和状态检查

某些配置步骤是异步运行的，因为它们无法快速完成。 响应中的状态可能包含以下项之一：“InProgress”、“Updating”、“Deleting”、“Succeeded”或“Failed”，包括错误代码。 使用 REST 时，响应最初返回 HTTP 状态代码 202（已接受）和包含 Azure-AsyncOperation 属性的标头：

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

若要查看异步操作的状态，请向 Azure-AsyncOperation 标头值发送 GET 请求：

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>创建群集

首先创建群集资源以开始创建专用群集。

必须指定以下属性：

- **ClusterName**：用于管理目的。 不会向用户公开此名称。
- **ResourceGroupName**：对于任何 Azure 资源，群集都属于一个资源组。 建议使用中心 IT 资源组，因为群集通常由组织中的许多团队共享。 有关更多设计注意事项，请查看[设计 Azure Monitor 日志部署](../logs/design-logs-deployment.md)
- **位置**：群集位于特定的 Azure 区域中。 只有位于此区域中的工作区才能链接到此群集。
- **SkuCapacity**：创建群集资源时必须指定承诺层级 (sku)。 承诺层级可以设置为 1000、2000 或 5000 GB/天。 有关群集成本的更多信息，请参阅[管理 Log Analytics 群集的成本](./manage-cost-storage.md#log-analytics-dedicated-clusters)。 请注意，承诺层级以前称为产能预留。 

创建群集资源后，可以编辑其他属性，如 sku、keyVaultProperties 或 billingType  。 参阅下面的更多详细信息。

每个区域每个订阅最多可以有 2 个活动群集。 如果删除群集，该群集将仍保留 14 天。 每个区域每个订阅最多可以有 4 个保留群集（活动或最近删除的群集）。

> [!WARNING]
> 创建群集会触发资源分配和预配。 此操作可能需要几个小时才能完成。 建议以异步方式运行。

创建群集的用户帐户必须具有以下标准 Azure 资源创建权限：`Microsoft.Resources/deployments/*` 和群集写入权限 `Microsoft.OperationalInsights/clusters/write`，可通过在其角色分配中添加此特定操作或 `Microsoft.OperationalInsights/*` 或 `*/write` 获得这些权限。

### <a name="create"></a>创建 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*调用* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*响应*

应为 202（已接受）和一个标头。

### <a name="check-cluster-provisioning-status"></a>检查群集预配状态

Log Analytics 群集的预配需要一段时间才能完成。 可以通过多种方式检查预配状态：

- 使用资源组名称运行 Get-AzOperationalInsightsCluster PowerShell 命令，并检查 ProvisioningState 属性。 预配进行时此值是 ProvisioningAccount，预配完成后是 Succeeded 。
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。

- 在群集资源上发送 GET 请求，然后查看 provisioningState 值 。 预配进行时此值是 ProvisioningAccount，预配完成后是 Succeeded 。

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **响应**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

“principalId”GUID 是托管标识服务为群集资源生成的 。

## <a name="link-a-workspace-to-cluster"></a>将工作区链接到群集

当工作区链接到专用群集时，引入到工作区的新数据将路由到新群集，而现有数据仍保留在现有群集上。 如果使用客户管理的密钥 (CMK) 加密专用群集，则只有新数据使用该密钥进行加密。 当系统在后端执行跨群集查询时，系统从用户中抽象出这种差异，用户像往常一样只查询工作区。

一个群集最多可以链接到 100 个工作区。 链接的工作区与群集位于同一区域。 若要保护系统后端并避免数据碎片化，一个工作区每月链接到群集的次数不能超过两次。

若要执行链接操作，需要同时具有对工作区和群集资源的“写入”权限：

- 在工作区中：*Microsoft.OperationalInsights/workspaces/write*
- 在群集资源中：*Microsoft.OperationalInsights/clusters/write*

除了计费方面，链接的工作区还会保留自己的设置，例如数据保留的长度。
工作区和群集可以位于不同的订阅中。 如果使用 Azure Lighthouse 将工作区和群集映射到一个租户，那么工作区和群集可能位于不同的租户中。

与任何群集操作一样，只有在完成 Log Analytics 群集配置之后才能执行工作区链接。

> [!WARNING]
> 将工作区链接到群集需要同步多个后端组件并确保缓存合成。 此操作可能需要两个小时才能完成。 建议以异步方式运行。


**PowerShell**

使用以下 PowerShell 命令链接到群集：

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

使用以下 REST 调用链接到群集：

发送

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*响应*

202（已接受）和标头。

### <a name="check-workspace-link-status"></a>检查工作区链接状态
  
如果使用客户管理的密钥，完成关联操作后，引入的数据会使用托管密钥进行加密存储，这可能需要长达 90 分钟才能完成。 

可以通过两种方式检查工作区关联状态：

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。

- 对工作区执行 Get 操作，并观察功能下的响应中是否存在 clusterResourceId 属性 。

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*调用*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*响应*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>更改群集属性

创建群集资源并对其进行完全预配后，可以使用 PowerShell 或 REST API 在群集级别编辑其他属性。 除了在群集创建过程中可用的属性外，便只能在预配群集后设置其他属性：

- **keyVaultProperties** - 更新 Azure Key Vault 中的密钥。 请参阅[为群集更新密钥标识符详细信息](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details)。 它包含以下参数：KeyVaultUri、KeyName、KeyVersion  。 
- **billingType** - billingType 属性可确定群集资源及其数据的计费归属 ：
  - **群集**（默认）- 群集的成本归属于群集资源。
  - **工作区** - 群集的成本按比例归属于群集中的工作区，如果一天中的引入数据总量低于承诺层级，则会向群集资源的部分使用量进行计费。 请参阅 [Log Analytics 专用群集](./manage-cost-storage.md#log-analytics-dedicated-clusters)以了解有关群集定价模型的更多信息。
  - **标识** - 用于对 Key Vault 进行身份验证的标识。 这可以是系统分配的或用户分配的。

>[!IMPORTANT]
>群集更新不应在同一操作中同时包含标识和密钥标识符详细信息。 如果两者都需要更新，则应该在两个连续操作中进行更新。

> [!NOTE]
> PowerShell 不支持 billingType 属性。

### <a name="get-all-clusters-in-resource-group"></a>获取资源组中的所有群集
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*调用*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*响应*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>获取订阅中的所有群集

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*调用*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*响应*
    
与“资源组的群集”相同，但在订阅范围内。



### <a name="update-commitment-tier-in-cluster"></a>更新群集中的承诺层级

链接工作区的数据量随时间变化时，建议适当地更新承诺层级别。 该层级以 GB 为单位指定，其值可以是 1000、2000 或 5000 GB/天。 请注意，无需提供完整的 REST 请求正文，但应包含 sku。

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*调用*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>更新群集中的 billingType

billingType 属性可确定群集及其数据的计费归属：
- 群集（默认）-- 计费归属于承载群集资源的订阅
- 工作区 -- 计费按比例归属于承载工作区的订阅

**REST**

*调用*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>从群集中取消与工作区的链接

你可以从群集中取消与工作区的链接。 从群集取消工作区的链接后，与此工作区相关联的新数据不会发送到专用群集。 此外，工作区计费不再通过群集完成。 未取消链接的工作区的旧数据可能还保留在群集上。 如果使用客户管理的密钥 (CMK) 加密此数据，则保留 Key Vault 机密。 该系统从 Log Analytics 用户中提取此更改。 用户可以像往常一样查询工作区。 系统根据需要在后端执行跨群集查询，无需获得用户的指示。  

> [!WARNING] 
> 一个月内特定工作区的链接操作限制为两次。 花时间考虑并相应地计划取消链接的操作。

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

使用以下 PowerShell 命令来从群集取消链接工作区：

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>删除群集

可以删除专用群集资源。 删除群集之前，必须取消所有工作区与群集的链接。 你需要对群集资源具有“写入”权限才能执行此操作。 

删除群集资源后，物理群集将进入清除和删除过程。 删除群集将删除存储在群集上的所有数据。 数据可能来自过去链接到群集的工作区。

过去 14 天内删除的群集资源处于软删除状态，因此该群集资源及其数据均可恢复。 由于删除群集资源后所有工作区均与群集资源解除了关联，因此需要在恢复之后重新关联工作区 。 用户无法执行恢复操作，请与 Microsoft 渠道或支持人员联系以获取恢复请求。

删除后 14 天内，群集资源名称被保留，不能被其他资源使用。

> [!WARNING] 
> 每个订阅的群集限制为三个。 活动群集和软删除群集均计入其中。 客户不应创建用于创建和删除群集的循环过程。 它对 Log Analytics 后端系统有重大影响。

**PowerShell**

使用以下 PowerShell 命令来删除群集：

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

使用以下 REST 调用来删除群集：

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常

## <a name="limits-and-constraints"></a>限制和约束

- 每个区域和每个订阅的活动群集的最大数目为 2

- 每个区域和每个订阅的保留群集（活动或最近删除）的最大数目为 4 

- 与群集链接的工作区的最大数目为 1000

- 你可以将工作区链接到群集，然后将其取消链接。 在 30 天内，工作区与特定工作区的链接数限制为 2。

- 目前不支持将群集移动到另一个资源组或订阅。

- 群集更新不应在同一操作中同时包含标识和密钥标识符详细信息。 如果两者都需要更新，则应该在两个连续操作中进行更新。

- 当前不能在中国使用密码箱。 

- 对于受支持区域中自 2020 年 10 月开始创建的群集，系统会自动为其配置[双重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)。 可以通过在群集上发送 GET 请求并观察启用了双重加密的群集的 `isDoubleEncryptionEnabled` 值是否为 `true` 来验证是否为你的群集配置了双重加密。 
  - 如果你创建群集并收到错误“<区域名称> 不支持对群集进行双重加密。”，则你仍可通过在 REST 请求正文中添加 `"properties": {"isDoubleEncryptionEnabled": false}` 以在不使用双重加密的情况下创建群集。
  - 创建群集后，无法更改双重加密设置。

## <a name="troubleshooting"></a>疑难解答

- 如果创建群集时出现冲突错误，原因可能是你在过去 14 天内删除了群集，并且它处于软删除状态。 软删除期间，群集名称保持为预留，并且无法新建同名群集。 永久删除群集时，名称将在软删除期结束后释放。

- 如果在群集处于预配或更新状态时对其进行更新，则更新将失败。

- 部分操作较为耗时，可能需要一段时间才能完成 - 包括群集创建、群集密钥更新和群集删除。 可以通过两种方式检查操作状态：
  - 使用 REST 时，从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
  - 将 GET 请求发送到群集或工作区，然后观察响应。 例如，未链接的工作区在“功能”下没有 clusterResourceId 。

- 将工作区链接到群集时，如果是链接到其他群集，则链接会失败。

- 错误消息
  
  群集创建：
  -  400 -- 群集名称无效。 群集名称可包含字符 a-z、A-Z、0-9，且长度为 3-63。
  -  400 -- 请求的正文为 Null 或格式错误。
  -  400 -- SKU 名称无效。 将 SKU 名称设置为 CapacityReservation。
  -  400 -- 提供了容量，但 SKU 不是 capacityReservation。 将 SKU 名称设置为 CapacityReservation。
  -  400 -- SKU 容量不足。 将“容量”值设置为 1000 或更高（以 100 (GB) 为度）。
  -  400 -- SKU 中的容量不在范围内。 应介于 1000 到最大允许容量之间，最大允许容量可在工作区中的“用量和预估成本”下找到。
  -  400 -- 容量锁定 30 天。 更新后 30 天内允许减少容量。
  -  400 -- 未设置 SKU。 将 SKU 名称设置为 CapacityReservation，将“容量”值设置为 1000 或更高（以 100 (GB) 为增加幅度）。
  -  400 -- 标识为 Null 或为空。 设置具有 systemAssigned 类型的标识。
  -  400 -- KeyVaultProperty 是创建时设置的。 创建群集后更新 KeyVaultProperties。
  -  400 -- 现在无法执行操作。 异步操作处于非成功状态。 群集必须完成其操作，才能执行任意更新操作。

  群集更新
  -  400 -- 群集处于正在删除状态。 正在执行异步操作。 群集必须完成其操作，才能执行任意更新操作。
  -  400 -- KeyVaultProperties 不为空，但格式错误。 请参阅[密钥标识符更新](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details)。
  -  400 -- 无法验证 Key Vault 中的密钥。 可能是由于权限不足或密钥不存在。 验证是否在 Key Vault 中[设置密钥和访问策略](../logs/customer-managed-keys.md#grant-key-vault-permissions)。
  -  400 -- 密钥不可恢复。 Key Vault 必须设置为“软删除”和“清除保护”。 请参阅 [Key Vault 文档](../../key-vault/general/soft-delete-overview.md)
  -  400 -- 现在无法执行操作。 等待异步操作完成，然后重试。
  -  400 -- 群集处于正在删除状态。 等待异步操作完成，然后重试。

  群集获取：
    -  404 -- 找不到群集，群集可能已删除。 如果尝试使用该名称创建群集但发生冲突，则该群集将处于软删除状态，为期 14 天。 可以联系支持人员将其恢复，也可以使用其他名称创建新群集。 

  群集删除
    -  409 -- 处于预配状态时无法删除群集。 等待异步操作完成，然后重试。

  工作区链接：
  -  404 -- 找不到工作区。 指定的工作区不存在或已被删除。
  -  409 -- 正在执行工作区链接或取消链接操作。
  -  400 -- 找不到群集，指定的群集不存在或已被删除。 如果尝试使用该名称创建群集但发生冲突，则该群集将处于软删除状态，为期 14 天。 可以联系支持人员将其恢复。

  工作区取消链接：
  -  404 -- 找不到工作区。 指定的工作区不存在或已被删除。
  -  409 -- 正在执行工作区链接或取消链接操作。

## <a name="next-steps"></a>后续步骤

- 了解 [Log Analytics 专用群集计费](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- 了解 [Log Analytics 工作区的正确设计](../logs/design-logs-deployment.md)
