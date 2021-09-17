---
title: 配置生命周期管理策略
titleSuffix: Azure Storage
description: 配置生命周期管理策略，以便于数据生命周期内自动在热存储层、冷存储层和存档存储层之间移动数据。
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7264e83fd830eb513a1ac74fdaaace2de1193ed
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515806"
---
# <a name="configure-a-lifecycle-management-policy"></a>配置生命周期管理策略

Azure 存储生命周期管理可提供基于规则的策略，用于将 blob 数据转移到最适合的访问层，或在数据生命周期结束时使数据过期。 生命周期策略作用于基础 blob，并且可以选择作用于 blob 的版本还是快照。 有关生命周期管理策略的详细信息，请参阅[通过自动管理数据生命周期来优化成本](lifecycle-management-overview.md)。

生命周期管理策略由一个或多个规则组成，这些规则定义了一组根据所满足的条件要执行的操作。 对于基础 blob，可以选择检查以下两个条件之一：

- 自上次修改 blob 以来经过的天数。
- 自上次访问 blob 以来经过的天数。 若要在操作中使用此条件，必须先[启用访问时间跟踪（可选）](#optionally-enable-access-time-tracking)。

当所选条件为 true 时，管理策略将执行指定操作。 例如，如果已经定义一项操作（将 30 天内未经修改的 blob 从热层移至冷层），则生命周期管理策略将会在自上次对该 blob 执行写入操作过去 30 天后移动此 blob。

对于 blob 快照或版本，所要检查的条件是自创建快照或版本以来经过的天数。

## <a name="optionally-enable-access-time-tracking"></a>启用访问时间跟踪（可选）

在配置生命周期管理策略之前，可以选择启用 blob 访问时间跟踪。 启用访问时间跟踪时，生命周期管理策略可能会根据上次通过读取或写入操作访问 blob 的时间加入一项操作。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要通过 Azure 门户启用上次访问时间跟踪，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“数据管理”部分，选择“启用访问跟踪”。

    :::image type="content" source="media/lifecycle-management-policy-configure/last-access-tracking-enable.png" alt-text="显示如何在 Azure 门户中启用上次访问跟踪的屏幕截图":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要通过 PowerShell 启用上次访问时间跟踪，请调用 [Enable-AzStorageBlobLastAccessTimeTracking](/powershell/module/az.storage/enable-azstoragebloblastaccesstimetracking) 命令，如以下示例中所示。 请务必将尖括号中的占位符值替换为你自己的值：

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Enable-AzStorageBlobLastAccessTimeTracking  -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -PassThru
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 启用上次访问时间跟踪，请调用 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 命令，如以下示例中所示。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --enable-last-access-tracking true
```

# <a name="template"></a>[模板](#tab/template)

若要使用 Azure 资源管理器模板为新存储帐户或现有存储帐户启用上次访问时间跟踪，请在模板定义中加入 lastAccessTimeTrackingPolicy 对象。 有关详细信息，请参阅 [Microsoft.Storage/storageAccounts/blobServices 2021-02-01 - Bicep & ARM 模板引用](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/blobservices?tabs=json)。 在 2019-06-01 及更高版本中，Azure 存储资源提供程序 REST API 可提供 lastAccessTimeTrackingPolicy 对象。

---

使用 daysAfterLastAccessTimeGreaterThan 属性来指定应当在自上次访问经过多少天后，对 Blob 执行操作。

## <a name="create-or-manage-a-policy"></a>创建或管理策略

可以使用 Azure 门户、PowerShell、Azure CLI 或 Azure 资源管理器模板来添加、编辑或删除生命周期管理策略。

# <a name="portal"></a>[Portal](#tab/azure-portal)

可以在 Azure 门户中通过两种方式添加策略。

- [列表视图](#list-view)
- [代码视图](#code-view)

#### <a name="list-view"></a>列表视图

1. 在 Azure 门户中导航到存储帐户。
1. 在“数据管理”下，选择“生命周期管理”以查看或更改生命周期管理策略。
1. 选择“列表视图”选项卡。

1. 选择“添加规则”并在“详细信息”表单上为你的规则命名。 你还可以设置“规则范围”、“Blob 类型”和“Blob 子类型”值。 下面的示例设置筛选 Blob 的范围。 这将导致添加“筛选器集”选项卡。

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-details.png" alt-text="Azure 门户中的“生命周期管理”>“添加规则”>“详细信息”页":::

1. 选择“基础 Blob”以设置规则的条件。 在以下示例中，如果 Blob 有 30 天未修改，它们将转移到冷存储。

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-base-blobs.png" alt-text="Azure 门户中的“生命周期管理”>“基础 Blob”页":::

   只有在启用访问时间跟踪后，才可以使用“上次访问”选项。 若要了解如何启用访问跟踪，请参阅[启用访问时间跟踪（可选）](#optionally-enable-access-time-tracking)。

1. 如果你在“详细信息”页上选择了“使用筛选器限制 Blob” ，请选择“筛选器集”来添加可选筛选器。 下方示例筛选的是 sample-container 容器中名称以 log 开头的 Blob。

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-filter-set.png" alt-text="Azure 门户中的生命周期管理筛选器集页":::

1. 选择“添加”以添加新策略。

#### <a name="code-view"></a>代码视图

1. 在 Azure 门户中导航到存储帐户。
1. 在“数据管理”下，选择“生命周期管理”以查看或更改生命周期管理策略。
1. 选择“代码视图”选项卡。在此选项卡上，你可以定义 JSON 生命周期管理策略。

下方 JSON 示例定义了以下生命周期策略：将名称以 log 开头的块 Blob 移动到冷层，但前提是该 Blob 已有超过 30 天时间未经修改。

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "sample-container/log"
             ]
           }
         }
       }
     ]
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要通过 PowerShell 添加生命周期管理策略，请使用以下命令：

- 调用 [AzStorageAccountManagementPolicyAction](/powershell/module/az.storage/add-azstorageaccountmanagementpolicyaction) 命令，以定义构成规则的操作。
- 调用 [AzStorageAccountManagementPolicyFilter](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyfilter) 命令，以指定规则的一个或多个筛选器。
- 调用 [AzStorageAccountManagementPolicyRule](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyrule) 命令，以创建包含操作和可选筛选器的策略规则。
- 调用 [AzStorageAccountManagementPolicy](/powershell/module/az.storage/set-azstorageaccountmanagementpolicy) 命令，以在存储帐户中创建策略。

下方示例演示如何使用上述每一个命令来创建生命周期策略。 请务必将尖括号中的占位符值替换为你自己的值：

```powershell
# Initialize the following variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Create a new action object.
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete `
    -daysAfterModificationGreaterThan 180
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToArchive `
    -daysAfterModificationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToCool `
    -daysAfterModificationGreaterThan 30
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -SnapshotAction Delete `
    -daysAfterCreationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BlobVersionAction TierToArchive `
    -daysAfterCreationGreaterThan 90

# Create a new filter object.
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd `
    -BlobType blockBlob

# Create a new rule object.
$rule1 = New-AzStorageAccountManagementPolicyRule -Name sample-rule `
    -Action $action `
    -Filter $filter

# Create the policy.
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Rule $rule1
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 添加生命周期管理策略，请将该策略写入 JSON 文件，然后调用 [az storage account management-policy create](/cli/azure/storage/account/management-policy#az_storage_account_management_policy_create) 命令创建该策略。

下方示例演示如何使用上述每一个命令来创建生命周期策略。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli
az storage account management-policy create \
    --account-name <storage-account> \
    --policy @policy.json \
    --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

若要使用 Azure 资源管理器模板定义生命周期管理策略，请在模板中加入 Microsoft.Storage/storageAccounts/managementPolicies 对象。 有关详细配置信息，请参阅 [Microsoft.Storage/storageAccounts/managementPolicies 2021-02-01 - Bicep & ARM 模板引用](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/managementpolicies?tabs=json)。 在 2018-11-01 及更高版本中，Azure 存储资源提供程序 REST API 可提供 Microsoft.Storage/storageAccounts/managementPolicies 对象。

---

必须完整读取或写入生命周期管理策略。 不支持部分更新。

> [!NOTE]
> 如果为存储帐户启用了防火墙规则，生命周期管理请求可能会被阻止。 可以通过针对受信任的 Microsoft 服务提供例外来取消阻止这些请求。 有关详细信息，请参阅“[配置防火墙和虚拟网络](../common/storage-network-security.md#exceptions)”中的“例外”部分。

## <a name="see-also"></a>请参阅

- [通过自动管理数据生命周期优化成本](lifecycle-management-overview.md)
- [Azure Blob 存储的访问层 - 热、冷和存档](storage-blob-storage-tiers.md)