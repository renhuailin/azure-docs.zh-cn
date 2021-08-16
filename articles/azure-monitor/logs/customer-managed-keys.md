---
title: Azure Monitor 客户管理的密钥
description: 使用 Azure Key Vault 密钥配置客户管理的密钥以加密 Log Analytics 工作区中的数据的信息和步骤。
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: fc66f79e09021a10c2dde3cc973cd608baeedc32
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061607"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 客户管理的密钥 

使用 Microsoft 管理的密钥对 Azure Monitor 中的数据进行加密。 可以使用自己的加密密钥来保护工作区中的数据和保存的查询。 指定客户管理的密钥时，该密钥将用于保护和控制对数据的访问，在配置后，将使用 Azure Key Vault 密钥对发送到工作区的所有数据进行加密。 使用客户托管密钥可以更灵活地管理访问控制。

建议在配置之前，查看下方的[限制和约束](#limitationsandconstraints)。

## <a name="customer-managed-key-overview"></a>客户管理的密钥概述

[静态加密](../../security/fundamentals/encryption-atrest.md)是组织中常见的隐私和安全要求。 你可以让 Azure 完全管理静态加密，同时可以使用各种选项严格管理加密和加密密钥。

Azure Monitor 确保使用 Microsoft 管理的密钥 (MMK) 静态加密所有数据和保存的查询。 Azure Monitor 还可以使用你自己的密钥进行加密（该密钥存储在 [Azure Key Vault](../../key-vault/general/overview.md) 中），这会赋予你控制权，允许你随时撤销对你的数据的访问权限。 Azure Monitor 进行加密的操作与[Azure 存储加密](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)的操作相同。

客户管理的密钥在[专用的群集](./logs-dedicated-clusters.md)上提供，可提供更高的保护级别和控制。 引入到专用群集的数据进行两次加密 - 一次在服务级别使用 Microsoft 管理的密钥或客户管理的密钥，一次在基础结构级别使用两种不同的加密算法和两个不同的密钥。 [双重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)可以在其中一种加密算法或密钥可能被泄露的情况下提供保护。 在这种情况下，附加的加密层会继续保护你的数据。 专用群集还允许通过[密码箱](#customer-lockbox-preview)控制来保护数据。

过去 14 天内引入的数据也保存在热缓存（受 SSD 支持）中，以实现高效的查询引擎操作。 此数据保持使用 Microsoft 密钥进行加密，而不管客户管理的密钥的配置如何，但你对 SSD 数据的控制将遵循[密钥吊销](#key-revocation)规定。 我们正致力于在 2021 年的上半年使用客户管理的密钥加密 SSD 数据。

Log Analytics 专用群集使用产能预留[定价模型](./logs-dedicated-clusters.md#cluster-pricing-model)，起始价格为 1000 GB/天。

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>客户管理的密钥在 Azure Monitor 中的操作方式

Azure Monitor 使用托管标识授予对 Azure Key Vault 的访问权限。 在群集级别支持 Log Analytics 群集的标识。 为了允许在多个工作区上提供客户管理的密钥保护，一个新的 Log Analytics 群集资源将用作 Key Vault 和 Log Analytics 工作区之间的中间标识连接。 群集的存储使用与群集资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证。 

客户管理的密钥配置完成后，与专用群集链接的工作区中引入的新数据都将使用密钥进行加密。 可以随时取消工作区与群集的链接。 然后，新数据会被引入到 Log Analytics 存储并使用 Microsoft 密钥进行加密，而你可以无缝查询新旧数据。

> [!IMPORTANT]
> 客户管理的密钥功能是区域性的。 Azure Key Vault、群集和链接的 Log Analytics 工作区必须位于同一区域，但可以位于不同订阅。

![客户管理的密钥概述](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics 群集资源具有拥有 Key Vault 权限的托管标识，此标识可传播到基础专用 Log Analytics 群集存储
3. 专用 Log Analytics 群集
4. 链接到群集资源的工作区 

### <a name="encryption-keys-operation"></a>加密密钥操作

存储数据加密涉及 3 种类型的密钥：

- **KEK** - 密钥加密密钥（你的客户管理的密钥）
- **AEK** - 帐户加密密钥
- **DEK** - 数据加密密钥

下列规则适用：

- Log Analytics 群集存储帐户为每个存储帐户生成唯一的加密密钥，称为 AEK。
- AEK 用于派生 DEK 密钥，后者用于对写入磁盘的每个数据块进行加密。
- 在 Key Vault 中配置密钥并在群集中引用它时，Azure 存储会将请求发送到 Azure Key Vault 以包装和解包 AEK，从而执行数据加密和解密操作。
- KEK 永不离开 Key Vault。
- Azure 存储使用与群集资源关联的托管标识通过 Azure Active Directory 对 Azure Key Vault 进行身份验证和访问。

### <a name="customer-managed-key-provisioning-steps"></a>客户管理的密钥的预配步骤

1. 创建 Azure Key Vault 和存储密钥
1. 创建群集
1. 向 Key Vault 授予权限
1. 为群集更新密钥标识符详细信息
1. 链接 Log Analytics 工作区

Azure 门户中当前不支持客户管理的密钥的配置，可以通过 [PowerShell](/powershell/module/az.operationalinsights/)、[CLI](/cli/azure/monitor/log-analytics) 或 [REST](/rest/api/loganalytics/) 请求执行预配。

### <a name="asynchronous-operations-and-status-check"></a>异步操作和状态检查

某些配置步骤是异步运行的，因为它们无法快速完成。 响应中的 `status` 可能是以下项之一：“InProgress”、“Updating”、“Deleting”、“Succeeded”或“Failed”，包括错误代码。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

空值

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="rest"></a>[REST](#tab/rest)

使用 REST 时，响应最初返回 HTTP 状态代码 202（接受）和包含 Azure-AsyncOperation 属性的标头：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

若要查看异步操作的状态，请向 Azure-AsyncOperation 标头中的终结点发送 GET 请求：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>存储加密密钥 (KEK)

在计划群集的区域中创建 Azure 密钥保管库，或使用现有 Azure 密钥保管库，然后生成或导入用于日志加密的密钥。 必须将 Azure Key Vault 配置为可恢复，以保护密钥以及对 Azure Monitor 中的数据的访问权限。 可以验证是否应启用 Key Vault 中“软删除”和“清除保护”属性下的此配置 。

![软删除和清除保护设置](media/customer-managed-keys/soft-purge-protection.png)

可以通过 CLI 和 PowerShell 在 Key Vault 中更新这些设置：

- [软删除](../../key-vault/general/soft-delete-overview.md)
- [清除保护](../../key-vault/general/soft-delete-overview.md#purge-protection)可防止在软删除后强行删除机密/保管库

## <a name="create-cluster"></a>创建群集

群集支持两种[托管标识类型](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)：系统分配的标识和用户分配的标识，而单一标识可根据自己的场景在群集中进行自定义。 
- 当标识 `type` 设置为“SystemAssigned”时，系统分配的托管标识会更加简单，并在群集创建过程中自动生成。 此标识稍后可用于授予对 Key Vault 的存储访问权限，以便进行包装和展开操作。 
  
  群集中系统分配的托管标识的标识设置
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- 如果要在创建群集时配置客户管理的密钥，则应事先在 Key Vault 中授予密钥和用户分配的标识，然后使用以下设置创建群集：标识 `type` 为“UserAssigned”，`UserAssignedIdentities` 具有标识的资源 ID。

  群集中用户分配的托管标识的标识设置
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> 如果 Key Vault 位于专用链接 (vNet) 中，则不能使用用户分配的托管标识。 在这种情况下，可以使用系统分配的托管标识。

请遵循[“专用群集”一文](./logs-dedicated-clusters.md#creating-a-cluster)中说明的过程。 

## <a name="grant-key-vault-permissions"></a>授予 Key Vault 权限

在 Key Vault 中创建访问策略来授予对你的群集的权限。 基础 Azure Monitor 存储使用这些权限。 在 Azure 门户中打开 Key Vault，单击“访问策略”，然后单击“+ 添加访问策略”以使用以下设置创建策略 ：

- 密钥权限：选择“获取”、“包装密钥”和“解包密钥”  。
- 选择主体：根据群集中使用的标识类型（系统或用户分配的托管标识），要么输入系统分配的托管标识的群集名称或群集主体 ID，要么输入用户分配的托管标识名称。

![授予 Key Vault 权限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要“获取”权限，才能验证是否已将 Key Vault 配置为可恢复以保护密钥以及对 Azure Monitor 数据的访问。

## <a name="update-cluster-with-key-identifier-details"></a>为群集更新密钥标识符详细信息

群集的所有操作都需要 `Microsoft.OperationalInsights/clusters/write` 操作权限。 可以通过包含 `*/write` 操作的所有者或参与者或包含 `Microsoft.OperationalInsights/*` 操作的 Log Analytics 参与者角色授予此权限。

此步骤使用要用于数据加密的密钥和版本更新 Azure Monitor 存储。 更新后，新密钥将用于包装和解包到存储密钥 (AEK)。

>[!IMPORTANT]
>- 密钥轮换可以是自动操作，也可以要求显式密钥更新，请参阅[密钥轮换](#key-rotation)，确定适合自己的方法后再更新群集中的密钥标识符详细信息。
>- 群集更新不应在同一操作中同时包含标识和密钥标识符详细信息。 如果两者都需要更新，则应在两次连续操作中进行更新。

![授予 Key Vault 权限](media/customer-managed-keys/key-identifier-8bit.png)

为群集中的 KeyVaultProperties 更新密钥标识符详细信息。

该操作是异步操作，可能需要一段时间才能完成。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**响应**

完成密钥的传播需要几分钟。 可以通过两种方式检查更新状态：
1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 在群集上发送 GET 请求，然后查看 KeyVaultProperties 属性。 你最近更新的密钥应返回到响应中。

密钥更新完成后，对 GET 请求的响应应如下所示：202（接受）和标头
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

---

## <a name="link-workspace-to-cluster"></a>将工作区链接到群集

> [!IMPORTANT]
> 完成 Log Analytics 群集的预配后才应执行此步骤。 如果在预配前链接工作区并引入数据，则引入的数据将被删除，并且无法恢复。

需要具有对工作区和群集的“写入”权限才能执行此操作，其中包括 `Microsoft.OperationalInsights/workspaces/write` 和 `Microsoft.OperationalInsights/clusters/write`。

请遵循[“专用群集”一文](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)中说明的过程。

## <a name="key-revocation"></a>密钥吊销

> [!IMPORTANT]
> - 若要撤销对数据的访问，建议禁用密钥，或删除 Key Vault 中的访问策略。
> - 如果将群集的 `identity` `type` 设置为 `None`，还可撤销数据访问权，但不建议使用此方法，因为如果不联系支持人员就无法将其还原。

群集存储在一小时或更短时间内将始终遵循关键权限的更改，并且存储将变得不可用。 与群集链接的工作区中引入的任何新数据都将被删除，并且无法恢复，数据将变得不可访问，针对这些工作区的查询将会失败。 只要不删除群集和工作区，之前引入的数据就会保留在存储中。 不可访问的数据由数据保留策略管理，并在保留期截止时被清除。 过去 14 天内引入的数据也保存在热缓存（SSD 提供支持）中，以实现高效的查询引擎操作。 它将在进行密钥吊销操作后被删除，并且变得不可访问。

群集的存储会定期检查 Key Vault 以尝试解包加密密钥，数据引入和查询将在受到访问后的 30 分钟内恢复。

## <a name="key-rotation"></a>密钥轮换

密钥轮换具有两种模式： 
- 自动轮换 - 当你使用 ```"keyVaultProperties"``` 更新群集但省略 ```"keyVersion"``` 属性或将其设置为 ```""``` 时，存储将自动使用最新版本。
- 显式密钥版本更新 - 更新群集并在 ```"keyVersion"``` 属性中提供密钥版本时，任何新的密钥版本都需要在群集中显式更新 ```"keyVaultProperties"```，请参阅[为群集更新密钥标识符详细信息](#update-cluster-with-key-identifier-details)。 如果在 Key Vault 中生成了新的密钥版本但未在群集中更新它，Log Analytics 群集存储将继续使用之前的密钥。 如果在更新群集中的新密钥之前禁用或删除旧密钥，则你将进入[密钥吊销](#key-revocation)状态。

进行密钥轮换操作后，所有数据都将保持可访问，因为数据始终使用帐户加密密钥 (AEK) 进行加密，而 AEK 目前使用 Key Vault 中的新密钥加密密钥 (KEK) 版本进行加密。

## <a name="customer-managed-key-for-saved-queries-and-log-alerts"></a>用于已存查询和日志警报的客户管理的密钥

Log Analytics 中使用的查询语言可以实现丰富的表达，并且可以在添加到查询的注释中或查询语法中包含敏感信息。 某些组织要求将此类信息按照客户管理的密钥的策略进行保护，因此你需要保存使用密钥加密的查询。 使用 Azure Monitor 可以在连接到工作区时将采用密钥加密的已存搜索查询和日志警报查询存储到你自己的存储帐户。 

> [!NOTE]
> 根据所使用的方案，可将 Log Analytics 查询保存到各种存储。 在以下方案中，仍使用 Microsoft 密钥 (MMK) 对查询加密，而不考虑客户管理的密钥的配置：Azure Monitor 中的工作簿、Azure 仪表板、Azure 逻辑应用、Azure Notebooks 和自动化 runbook。

自带存储 (BYOS) 并将其链接到工作区时，该服务会将已存搜索查询和日志警报查询上传到存储帐户。 这意味着，可以使用加密 Log Analytics 群集中数据的密钥或其他密钥来控制存储帐户和[静态加密策略](../../storage/common/customer-managed-keys-overview.md)。 但需支付与该存储帐户相关的费用。 

**设置用于查询的客户管理的密钥之前需要注意的事项**
* 需拥有对工作区和存储帐户的“写入”权限
* 确保在 Log Analytics 工作区所在区域创建存储帐户
* 存储中的保存搜索视为服务项目，并且其格式可能会发生变化
* 从工作区删除现有的保存搜索。 复制配置之前需要的所有保存搜索。 可以使用 [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) 查看 *已存搜索*
* 不支持查询历史记录，因此无法查看已运行的查询
* 可以出于保存查询的目的将单个存储帐户链接到工作区，且该帐户可同时用于已存搜索查询和日志警报查询
* 不支持“固定到仪表板”
* 触发的日志警报不包含搜索结果或警报查询。 你可以使用[警报维度](../alerts/alerts-unified-log.md#split-by-alert-dimensions)获取已触发警报中的上下文。

**为已存搜索查询配置 BYOS**

将“查询”的存储帐户链接到工作区 - 已存搜索查询保存在存储帐户中 。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

完成配置后，所有新的已存搜索查询将保存在存储中。

**为日志警报查询配置 BYOS**

将警报的存储帐户链接到工作区 - 日志警报查询保存在存储帐户中。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

完成配置后，所有新的日志警报查询将保存在存储中。

## <a name="customer-lockbox-preview"></a>客户密码箱（预览版）

通过密码箱，可以在支持请求期间批准或拒绝 Microsoft 工程师的数据访问请求。

在 Azure Monitor 中，对 Log Analytics 专用群集所链接的工作区中的数据也可以实现上述操作。 密码箱适用于 Log Analytics 专用群集中存储的数据，在群集中这些数据以隔离形式存在于受密码箱保护的订阅下的群集存储帐户中。  

了解有关 [Microsoft Azure 客户密码箱](../../security/fundamentals/customer-lockbox-overview.md)详细信息

## <a name="customer-managed-key-operations"></a>客户管理的密钥的操作

客户管理的密钥在专用群集上提供，并且[专用群集文章](./logs-dedicated-clusters.md#change-cluster-properties)中引用了这些操作

- 获取资源组中的所有群集  
- 获取订阅中的所有群集
- 更新群集中的容量预留
- 更新群集中的 billingType
- 从群集中取消与工作区的链接
- 删除群集

## <a name="limitations-and-constraints"></a>限制和约束

- 每个区域和每个订阅的群集的最大数目为 2

- 可以链接到群集的最大工作区数为 1000

- 你可以将工作区链接到群集，然后将其取消链接。 在 30 天内，工作区与特定工作区的链接数限制为 2。

- 客户管理的密钥的加密应用于在配置后新引入的数据。 在配置前引入的数据仍将使用 Microsoft 密钥进行加密。 你可以无缝查询在配置客户管理的密钥前后引入的数据。

- Azure Key Vault 必须配置为可恢复。 默认情况下，这些属性不会启用，并且应使用 CLI 或 PowerShell 对其进行配置：<br>
  - [软删除](../../key-vault/general/soft-delete-overview.md)
  - 应打开[清除保护](../../key-vault/general/soft-delete-overview.md#purge-protection)防止在软删除后强行删除机密/保管库。

- 目前不支持将群集移动到另一个资源组或订阅。

- Azure Key Vault、群集和工作区必须位于同一区域和同一 Azure Active Directory (Azure AD) 租户，但可以位于不同订阅。

- 群集更新不应在同一操作中同时包含标识和密钥标识符详细信息。 如果两者都需要更新，则应在两次连续操作中进行更新。

- 当前不能在中国使用密码箱。 

- 对于受支持区域中自 2020 年 10 月开始创建的群集，系统会自动为其配置[双重加密](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)。 可以通过在群集上发送 GET 请求并观察启用了双重加密的群集的 `isDoubleEncryptionEnabled` 值是否为 `true` 来验证是否为你的群集配置了双重加密。 
  - 如果你创建群集并收到错误“<区域名称> 不支持对群集进行双重加密。”，则你仍可通过在 REST 请求正文中添加 `"properties": {"isDoubleEncryptionEnabled": false}` 以在不使用双重加密的情况下创建群集。
  - 创建群集后，无法更改双重加密设置。

  - 如果将群集的 `identity` `type` 设置为 `None`，还可撤销数据访问权，但不建议使用此方法，因为如果不联系支持人员就无法将其还原。 撤销数据访问权的推荐方法是[吊销密钥](#key-revocation)。

  - 如果密钥保管库位于专用链接 (vNet) 中，则不能将客户管理的密钥与用户分配的托管标识一起使用。 在这种情况下，可以使用系统分配的托管标识。

## <a name="troubleshooting"></a>疑难解答

- Key Vault 可用性的行为
  - 在正常操作中，存储会缓存 AEK 一小段时间，并返回 Key Vault 定期进行解包。
    
  - 暂时性连接错误 -- 存储通过允许密钥在缓存中保留一小段时间来处理暂时性错误（超时、连接失败、DNS 问题），这可以克服可用性方面的任何小问题。 查询和引入功能将继续运行而不会中断。
    
  - 实时网站 -- 如果在约 30 分钟内未进行访问，则会导致无法使用存储帐户。 查询功能不可用，引入的数据会使用 Microsoft 密钥缓存几个小时，以避免数据丢失。 恢复对 Key Vault 的访问后，查询将变为可用，临时缓存的数据会引入到数据存储并使用客户管理的密钥进行加密。

  - Key Vault 访问速率 -- Azure Monitor 存储为实现包装和解包操作而访问 Key Vault 的频率介于 6 到 60 秒之间。

- 如果在群集处于预配或更新状态时对其进行更新，则更新将失败。

- 如果创建群集时出现冲突错误，原因可能是你在过去 14 天内删除了群集，并且它处于软删除期间。 软删除期间，群集名称保持为预留，并且无法新建同名群集。 永久删除群集时，名称将在软删除期结束后释放。

- 将工作区链接到群集时，如果是链接到其他群集，则链接会失败。

- 如果创建群集并立即指定 KeyVaultProperties，则操作可能会失败，因为将系统标识分配给群集后才能定义访问策略。

- 如果使用 KeyVaultProperties 更新现有的群集，并且 Key Vault 中缺少“Get”密钥访问策略，则该操作将失败。

- 如果无法部署群集，请验证 Azure Key Vault、群集和链接的 Log Analytics 工作区是否位于同一区域。 可以位于不同的订阅。

- 如果在 Key Vault 中更新密钥版本，但未更新群集中的新密钥标识符详细信息，则 Log Analytics 群集将继续使用之前的密钥，并且数据将变得不可访问。 更新群集中的新密钥标识符详细信息以恢复数据引入和数据查询功能。

- 部分操作较为耗时，可能需要一段时间才能完成 - 包括群集创建、群集密钥更新和群集删除。 可以通过两种方式检查操作状态：
  1. 使用 REST 时，从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
  2. 将 GET 请求发送到群集或工作区，然后观察响应。 例如，未链接的工作区在“功能”下没有 clusterResourceId 。

- 错误消息
  
  **群集创建**
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

  **群集更新**
  -  400 -- 群集处于正在删除状态。 正在执行异步操作。 群集必须完成其操作，才能执行任意更新操作。
  -  400 -- KeyVaultProperties 不为空，但格式错误。 请参阅[密钥标识符更新](#update-cluster-with-key-identifier-details)。
  -  400 -- 无法验证 Key Vault 中的密钥。 可能是由于权限不足或密钥不存在。 验证是否在 Key Vault 中[设置密钥和访问策略](#grant-key-vault-permissions)。
  -  400 -- 密钥不可恢复。 Key Vault 必须设置为“软删除”和“清除保护”。 请参阅 [Key Vault 文档](../../key-vault/general/soft-delete-overview.md)
  -  400 -- 现在无法执行操作。 等待异步操作完成，然后重试。
  -  400 -- 群集处于正在删除状态。 等待异步操作完成，然后重试。

  **群集获取**
    -  404 -- 找不到群集，群集可能已删除。 如果尝试使用该名称创建群集但发生冲突，则该群集将处于软删除状态，为期 14 天。 可以联系支持人员将其恢复，也可以使用其他名称创建新群集。 

  **群集删除**
    -  409 -- 处于预配状态时无法删除群集。 等待异步操作完成，然后重试。

  **工作区链接**
  -  404 -- 找不到工作区。 指定的工作区不存在或已被删除。
  -  409 -- 正在执行工作区链接或取消链接操作。
  -  400 -- 找不到群集，指定的群集不存在或已被删除。 如果尝试使用该名称创建群集但发生冲突，则该群集将处于软删除状态，为期 14 天。 可以联系支持人员将其恢复。

  **工作区取消链接**
  -  404 -- 找不到工作区。 指定的工作区不存在或已被删除。
  -  409 -- 正在执行工作区链接或取消链接操作。
## <a name="next-steps"></a>后续步骤

- 了解 [Log Analytics 专用群集计费](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- 了解 [Log Analytics 工作区的正确设计](./design-logs-deployment.md)
