---
title: Azure Monitor 日志专用群集
description: 每天引入超过 1 TB 监视数据的客户可以使用专用群集，而不是共享群集
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: a68501bd1189993b4dd0c2acdecaa7434fa51dcc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488028"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor 日志专用群集

Azure Monitor 日志专用群集是一种部署选项，可实现 Azure Monitor 记录客户的高级功能。 具有专用群集的客户可以选择在这些群集上托管的工作区。

需要专用群集的功能包括：

- **[客户管理的密钥](../platform/customer-managed-keys.md)** -使用客户提供和控制的密钥来加密群集数据。
- **[密码箱](../platform/customer-managed-keys.md#customer-lockbox-preview)** -客户可以控制 Microsoft 支持工程师对数据的访问请求。
- **[双加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** 可防止其中一个加密算法或密钥泄露的情况。 在这种情况下，额外的加密层将继续保护你的数据。
- **[多工作区](../log-query/cross-workspace-query.md)** -如果客户使用多个工作区进行生产，则使用专用群集可能有意义。 如果所有工作区都在同一群集上，则跨工作区查询将运行更快。 使用专用群集的方式也可能更具成本效益，因为分配的容量预留层将考虑所有群集引入，并将其应用到所有的工作区，即使其中一些空间较小且不符合容量保留折扣。

专用群集要求客户使用每天至少 1 TB 的数据引入产能进行提交。 迁移到专用群集很简单。 无数据丢失或服务中断。 

> [!IMPORTANT]
> 专用群集已获得批准并完全支持用于生产部署。 但是，由于临时产能限制，我们要求你预先注册才能使用该功能。 在 Microsoft 中使用联系人提供订阅 ID。

## <a name="management"></a>管理 

专用群集通过表示 Azure Monitor 日志群集的 Azure 资源进行管理。 所有操作都是使用 PowerShell 或 REST API 在该资源上完成的。

创建群集后，可以对其进行配置并将工作区链接到该群集。 当工作区链接到群集时，发送到工作区的新数据都将驻留在群集上。 只有与群集位于同一区域中的工作区才能链接到群集。 可从群集中取消工作区的链接，但有一些限制。 本文将详细介绍这些限制。 

引入到专用群集的数据将被加密两次-一次是在使用 Microsoft 托管密钥或 [客户托管密钥](../platform/customer-managed-keys.md)的服务级别，一次使用两种不同的加密算法和两个不同的密钥。 [双加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) 可防止其中一个加密算法或密钥泄露的情况。 在这种情况下，额外的加密层将继续保护你的数据。 专用群集还允许通过 [密码箱](../platform/customer-managed-keys.md#customer-lockbox-preview) 控制来保护数据。

群集级别的所有操作都需要群集上的 `Microsoft.OperationalInsights/clusters/write` 操作权限。 可以通过包含 `*/write` 操作的所有者或参与者或包含 `Microsoft.OperationalInsights/*` 操作的 Log Analytics 参与者角色授予此权限。 有关 Log Analytics 权限的更多信息，请参阅[管理对 Azure Monitor 中的日志数据和工作区的访问](../platform/manage-access.md)。 


## <a name="cluster-pricing-model"></a>群集定价模型

Log Analytics 专用群集使用产能预留定价模型，该模型至少为 1000 GB/天。 将按即用即付费率对超出预留级别的任何使用量进行计费。  有关产能预留的定价信息，请参阅 [Azure Monitor 定价页]( https://azure.microsoft.com/pricing/details/monitor/)。  

群集产能预留级别将使用 `Sku` 下的 `Capacity` 参数以编程方式通过 Azure 资源管理器进行配置。 `Capacity` 指定 GB 为单位，并且值可以为 1000 GB/天或更大，增量为 100 GB/天。

对于群集上的使用情况，有两种计费模式。 配置群集时，可通过 `billingType` 参数指定这些计费模式。 

1. **群集**：在此情况下（其为默认情况），引入数据的计费在群集级别完成。 将聚合与群集关联的每个工作区中的引入数据数量，以计算群集的每日账单。 

2. **工作区**：群集的产能预留成本按比例分配给群集中的工作区（在考虑了为每个工作区从 [Azure 安全中心](../../security-center/index.yml)进行每节点分配之后。）

请注意，如果工作区使用旧的每节点定价层，则当其链接到群集时，它将根据群集的产能预留引入到的数据来计费，而不再是按节点计费。 将继续应用来自 Azure 安全中心的每节点数据分配。

有关 Log Analytics 专用群集的计费的详细信息，请参阅[此处]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)。


## <a name="creating-a-cluster"></a>创建群集

首先创建群集资源以开始创建专用群集。

必须指定以下属性：

- **ClusterName**：用于管理目的。 不会向用户公开此名称。
- **ResourceGroupName**：对于任何 Azure 资源，群集都属于一个资源组。 建议使用中心 IT 资源组，因为群集通常由组织中的许多团队共享。 有关更多设计注意事项，请查看[设计 Azure Monitor 日志部署](../platform/design-logs-deployment.md)
- **位置**：群集位于特定的 Azure 区域中。 只有位于此区域中的工作区才能链接到此群集。
- **SkuCapacity**：创建群集资源时，必须指定产能预留级别 (SKU) 。 产能预留级别可以在每天 1000 GB 到 3000 GB 之间。 如果需要，你可以在以后按 100 进行增减。 如果你需要高于每天 3000 GB 的产能预留级别，请通过 LAIngestionRate@microsoft.com 与我们联系。 有关群集成本的更多信息，请参阅[管理 Log Analytics 群集的成本](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

创建群集资源后，可以编辑其他属性，如 sku、keyVaultProperties 或 billingType  。 参阅下面的更多详细信息。

> [!WARNING]
> 创建群集会触发资源分配和预配。 此操作可能需要一个小时才能完成。 建议以异步方式运行。

创建群集的用户帐户必须具有标准 Azure 资源创建权限：`Microsoft.Resources/deployments/*` 和群集写入权限 `(Microsoft.OperationalInsights/clusters/write)`。

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

应为 200（正常）和一个标头。

### <a name="check-provisioning-status"></a>检查预配状态

Log Analytics 群集的预配需要一段时间才能完成。 可以通过多种方式检查预配状态：

- 使用资源组名称运行 Get-AzOperationalInsightsCluster PowerShell 命令，并检查 ProvisioningState 属性。 预配进行时此值是 ProvisioningAccount，预配完成后是 Succeeded 。
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。

- 在群集资源上发送 GET 请求，然后查看 provisioningState 值 。 预配进行时此值是 ProvisioningAccount，预配完成后是 Succeeded 。

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

## <a name="change-cluster-properties"></a>更改群集属性

创建群集资源并对其进行完全预配后，可以使用 PowerShell 或 REST API 在群集级别编辑其他属性。 除了在群集创建过程中可用的属性外，便只能在预配群集后设置其他属性：

- **keyVaultProperties**：用于配置 Azure Key Vault，以便使用该保管库预配 [Azure Monitor 客户管理的密钥](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure)。 它包含以下参数：KeyVaultUri、KeyName、KeyVersion  。 
- **billingType** - billingType 属性可确定群集资源及其数据的计费归属 ：
  - **群集**（默认）- 群集的产能预留成本归因于群集资源。
  - **工作区** - 群集的产能预留成本按比例分配给群集中的工作区，如果当天引入的总数据在产能预留之下，则会对群集资源的一些使用进行收费。 请参阅 [Log Analytics 专用群集](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)以了解有关群集定价模型的更多信息。 

> [!NOTE]
> PowerShell 不支持 billingType 属性。
> 群集属性更新可能是异步执行的，可能需要一段时间才能完成。

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> 可以使用 PATCH 更新群集资源 sku、keyVaultProperties 或 billingType   。

例如： 

*调用*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

“KeyVaultProperties”包含 Key Vault 密钥标识符详细信息。

*响应*

200（正常）和标头

### <a name="check-cluster-update-status"></a>检查群集更新状态

完成密钥标识符的传播需要几分钟。 可以通过两种方式检查更新状态：

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。 

   OR

- 在群集资源上发送 GET 请求，然后查看 KeyVaultProperties 属性 。 你最近更新的密钥标识符详细信息应返回到响应中。

   密钥标识符更新完成后，对群集资源上 GET 请求的响应应如下所示：

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principle-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "keyVaultProperties": {
         "keyVaultUri": "https://key-vault-name.vault.azure.net",
         "kyName": "key-name",
         "keyVersion": "current-version"
         },
        "provisioningState": "Succeeded",
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>将工作区链接到群集

当工作区链接到专用群集时，引入到工作区的新数据将路由到新群集，而现有数据仍保留在现有群集上。 如果使用客户管理的密钥 (CMK) 加密专用群集，则只有新数据使用该密钥进行加密。 当系统在后端执行跨群集查询时，系统从用户中抽象出这种差异，用户像往常一样只查询工作区。

一个群集最多可以链接到 100 个工作区。 链接的工作区与群集位于同一区域。 若要保护系统后端并避免数据碎片化，一个工作区每月链接到群集的次数不能超过两次。

若要执行链接操作，需要同时具有对工作区和群集资源的“写入”权限：

- 在工作区中：*Microsoft.OperationalInsights/workspaces/write*
- 在群集资源中：*Microsoft.OperationalInsights/clusters/write*

除了计费方面，链接的工作区还会保留自己的设置，例如数据保留的长度。
工作区和群集可以位于不同的订阅中。 如果使用 Azure Lighthouse 将这两个用户映射到单个租户，则工作区和群集可能位于不同的租户中。

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*响应*

200 OK 和标头。

### <a name="using-customer-managed-keys-with-linking"></a>使用带链接的客户管理的密钥

如果使用客户管理的密钥，完成关联操作后，引入的数据会使用托管密钥进行加密存储，这可能需要长达 90 分钟才能完成。 

可以通过两种方式检查工作区关联状态：

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。

- 发送 [工作区–获取](/rest/api/loganalytics/workspaces/get) 请求并观察响应。 关联的工作区在“功能”下有一个 clusterResourceId。

发送请求如下所示：

发送

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>取消工作区与专用群集的链接

你可以从群集中取消与工作区的链接。 从群集取消工作区的链接后，与此工作区相关联的新数据不会发送到专用群集。 此外，工作区计费不再通过群集完成。 未取消链接的工作区的旧数据可能还保留在群集上。 如果使用客户管理的密钥 (CMK) 加密此数据，则保留 Key Vault 机密。 该系统从 Log Analytics 用户中提取此更改。 用户可以像往常一样查询工作区。 系统根据需要在后端执行跨群集查询，无需获得用户的指示。  

> [!WARNING] 
> 一个月内每个工作区的链接操作限制为两次。 花时间考虑并相应地计划取消链接的操作。 

## <a name="delete-a-dedicated-cluster"></a>删除专用群集

可以删除专用群集资源。 删除群集之前，必须取消所有工作区与群集的链接。 你需要对群集资源具有“写入”权限才能执行此操作。 

删除群集资源后，物理群集将进入清除和删除过程。 删除群集将删除存储在群集上的所有数据。 数据可能来自过去链接到群集的工作区。

过去 14 天内删除的群集资源处于软删除状态，因此该群集资源及其数据均可恢复。 由于删除群集资源后所有工作区均与群集资源解除了关联，因此需要在恢复之后重新关联工作区 。 用户无法执行恢复操作，请与 Microsoft 渠道或支持人员联系以获取恢复请求。

删除后 14 天内，群集资源名称被保留，不能被其他资源使用。

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



## <a name="next-steps"></a>后续步骤

- 了解 [Log Analytics 专用群集计费](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- 了解 [Log Analytics 工作区的正确设计](../platform/design-logs-deployment.md)
