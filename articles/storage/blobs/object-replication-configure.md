---
title: 配置对象复制
titleSuffix: Azure Storage
description: 了解如何配置对象复制，以将块 blob 从一个存储帐户中的容器异步复制到另一个存储帐户中的容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b53f861dc361463a44202874820955c9bdb5d0f6
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123472931"
---
# <a name="configure-object-replication-for-block-blobs"></a>为块 blob 配置对象复制

对象复制在源存储帐户和目标帐户之间异步复制块 blob。 配置对象复制时，需要创建复制策略，以指定源存储帐户和目标帐户。 复制策略包括一个或多个规则，用于指定源容器和目标容器，并指明复制源容器中的哪些块 blob。 有关对象复制的详细信息，请参阅[针对块 Blob 的对象复制](object-replication-overview.md)。

本文介绍如何使用 Azure 门户、PowerShell 或 Azure CLI 配置对象复制策略。 还可以使用 Azure 存储资源提供程序客户端库之一来配置对象复制。

## <a name="prerequisites"></a>必备条件

在配置对象复制前，如果还没有源存储帐户和目标存储帐户，请先创建它们。 源帐户和目标帐户可以是常规用途 v2 存储帐户，也可以是高级块 Blob 帐户（预览版）。 有关详细信息，请参阅[创建 Azure 存储帐户](../common/storage-account-create.md)。

若要执行对象复制，需要同时为源帐户和目标帐户启用 blob 版本管理，并为源帐户启用 blob 更改源。 若要详细了解 blob 版本控制，请参阅 [Blob 版本控制](versioning-overview.md)。 若要详细了解更改源，请参阅 [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)。 请记住，启用这些功能可能会产生额外的成本。

若要为存储帐户配置对象复制策略，需要具有存储帐户级别或更高级别的 Azure 资源管理器参与者角色。 有关详细信息，请参阅 Azure 基于角色的访问控制 (Azure RBAC) 文档中的 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 针对高级块 Blob 帐户的对象复制目前处于预览阶段。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="configure-object-replication-with-access-to-both-storage-accounts"></a>将对象复制配置为访问这两个存储帐户

如果同时有权访问源存储帐户和目标存储帐户，则可以在这两个帐户上均配置对象复制策略。 以下示例显示如何使用 Azure 门户、PowerShell 或 Azure CLI 配置对象复制。

### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 Azure 门户中配置对象复制时，只需在源帐户上配置策略。 为源帐户配置策略后，Azure 门户会自动在目标帐户上创建策略。

若要在 Azure 门户中创建复制策略，请按照以下步骤操作：

1. 在 Azure 门户中，转到源存储帐户。
1. 在“Blob 服务”下，选择“对象复制” 。
1. 选择“设置复制规则”。
1. 选择目标订阅和存储帐户。
1. 在“容器对”部分中，选择源帐户中的源容器，以及目标帐户中的目标容器。 每个复制策略最多可以创建 10 个容器对。

    下图展示了一组复制规则。

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="展示了 Azure 门户中复制规则的屏幕截图":::

1. 如果需要，指定一个或多个筛选器，以便只复制与前缀模式匹配的 blob。 例如，如果指定前缀 `b`，则只会复制名称以此字母开头的 blob。 可以将虚拟目录指定为前缀的一部分。 前缀字符串不支持通配符。

    下图展示了限制在复制规则中复制哪些 blob 的筛选器。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="展示了复制规则的筛选器的屏幕截图":::

1. 默认情况下，复制范围设置为只复制新对象。 若要复制容器中的所有对象，或从自定义日期和时间开始复制对象，请选择“更改”链接，然后为容器对配置复制范围。

    下图显示了从指定的日期和时间开始复制对象的自定义复制范围。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="展示了对象复制的自定义复制范围的屏幕截图":::

1. 选择“保存并应用”，以创建复制策略，并开始复制数据。

配置对象复制后，Azure 门户会显示复制策略和规则，如下图所示。

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="显示 Azure 门户中对象复制策略的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建复制策略，请先安装 [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) 或更高版本的 Az.Storage PowerShell 模块。 有关如何安装 Azure PowerShell 的详细信息，请参阅[使用 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-az-ps)。

以下示例显示如何先在目标帐户上创建复制策略，然后再在源帐户上创建策略。 请注意将尖括号中的值替换为你自己的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2021-09-01T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建复制策略，请先安装 Azure CLI 2.11.1 或更高版本。 有关详细信息，请参阅 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)。

接下来，通过调用 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 命令，在源存储帐户和目标存储帐户上启用 blob 版本控制，并在源帐户上启用更改源。 请注意将尖括号中的值替换为你自己的值：

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

在它们各自的存储帐户中创建源容器和目标容器。

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-2 \
    --auth-mode login
```

通过调用 [az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create)，在目标帐户上创建新的复制策略和关联的规则。

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2021-09-01T00:00:00Z' \
    --prefix-match a

```

创建新策略时，Azure 存储会为其设置策略 ID。 若要向策略添加其他规则，请调用 [az storage account or-policy rule add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) 并提供策略 ID。

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

接下来，使用策略 ID 在源帐户上创建策略。

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

## <a name="configure-object-replication-with-access-to-only-the-destination-account"></a>将对象复制配置为仅访问目标帐户

如果你没有访问源存储帐户的权限，则可以在目标帐户上配置对象复制，并向另一个用户提供一个包含策略定义的 JSON 文件，以便在源帐户上创建相同的策略。 例如，如果源帐户与目标帐户位于不同的 Azure AD 租户中，则可以使用此方法配置对象复制。

> [!NOTE]
> 默认情况下，允许对存储帐户进行跨租户对象复制。 为防止跨租户复制，可以设置 AllowCrossTenantReplication 属性（预览版），禁止对存储帐户进行跨租户对象复制。 有关详细信息，请参阅[防止跨 Azure Active Directory 租户进行对象复制](object-replication-prevent-cross-tenant-policies.md)。

本部分中的示例演示如何在目标帐户上配置对象复制策略，然后获取该策略的 JSON 文件，其他用户可以使用该文件在源帐户上配置策略。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户中的 JSON 文件在目标帐户上配置对象复制，请执行以下步骤：

1. 创建一个本地 JSON 文件，用于在目标帐户上定义复制策略。 将 policyId 字段设置为“默认”，以便 Azure 存储区可以定义策略 ID 。

    若要创建定义复制策略的 JSON 文件，一种简单的方法是先在 Azure 门户中创建两个存储帐户之间的测试复制策略。 然后，可以下载复制规则并根据需要修改 JSON 文件。

1. 在 Azure 门户中导航到目标帐户的“对象复制”设置。
1. 选择“上传复制规则”。
1. 上传 JSON 文件。 Azure 门户会显示将创建的策略和规则，如下图所示。

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="显示如何上传 JSON 文件以定义复制策略的屏幕截图":::

1. 选择“上传”，在目标帐户上创建复制策略。

然后，可以下载包含策略定义的 JSON 文件，可以将该文件提供给其他用户用于配置源帐户。 若要下载此 JSON 文件，请执行以下步骤：

1. 在 Azure 门户中导航到目标帐户的“对象复制”设置。
1. 选择要下载的策略旁边的“更多”按钮，然后选择“下载规则”，如下图所示 。

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="显示如何将复制规则下载为 JSON 文件的屏幕截图":::

1. 将 JSON 文件保存到本地计算机，以便与其他用户共享以在源帐户上配置策略。

下载的 JSON 文件包含“Azure 存储”为目标帐户上的策略创建的策略 ID。 必须使用相同的策略 ID 在源帐户上配置对象复制。

请记住，为了为目标帐户创建复制策略而通过 Azure 门户上传 JSON 文件时，不会在源帐户中自动创建相同的策略。 在 Azure 存储开始复制对象之前，另一个用户必须在源帐户上创建策略。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要从 PowerShell 下载包含目标帐户的复制策略定义的 JSON 文件，请调用 [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) 命令以返回策略。 然后，将策略转换为 JSON，并将其另存为本地文件，如以下示例中所示。 请注意将尖括号和文件路径中的值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

若要通过 PowerShell 使用 JSON 文件定义源帐户上的复制策略，请检索本地文件，并将 JSON 转换为对象。 然后，调用 [Set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) 命令以在源帐户上配置策略，如以下示例中所示。

运行示例时，请务必将 `-ResourceGroupName` 参数设置为源帐户的资源组，并将 `-StorageAccountName` 参数设置为源帐户的名称。 另外，请注意将尖括号和文件路径中的值替换为你自己的值：

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要将目标帐户的复制策略定义从 Azure CLI 写入 JSON 文件，请调用 [az storage account or-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) 命令并输出到文件。

以下示例将策略定义写入名为 policy.json 的 JSON 文件。 请注意将尖括号和文件路径中的值替换为你自己的值：

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

若要通过 Azure CLI 使用 JSON 文件在源帐户上配置复制策略，请调用 [az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) 命令并引用 policy.json 文件。 请注意将尖括号和文件路径中的值替换为你自己的值：

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>检查 blob 的复制状态

可以使用 Azure 门户、PowerShell 或 Azure CLI 检查源帐户中 blob 的复制状态。 在复制完成或失败之前，不会填充对象复制属性。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中检查源帐户中 blob 的复制状态，请执行以下步骤：

1. 在 Azure 门户中，导航到源帐户。
1. 找到包含源 blob 的容器。
1. 选择要显示其属性的 blob。 如果已成功复制 blob，则“对象复制”部分将显示状态设置为“完成”。 还会列出复制策略 ID 和用于管理此容器的对象复制的规则的 ID。

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="显示源帐户中 blob 的复制状态的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 检查源帐户中 blob 的复制状态，请获取对象复制“ReplicationStatus”属性的值，如以下示例中所示。 请注意将尖括号中的值替换为你自己的值：

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 检查源帐户中 blob 的复制状态，请获取对象复制“status”属性的值，如以下示例中所示：

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

如果源帐户中 blob 的复制状态指示失败，则请调查以下可能的原因：

- 请确保已在目标帐户上配置对象复制策略。
- 请验证目标容器是否仍然存在。
- 如果在写入操作中已使用客户提供的密钥对源 blob 进行了加密，则对象复制将失败。 有关客户提供的密钥的详细信息，请参阅[在对 Blob 存储的请求中提供加密密钥](encryption-customer-provided-keys.md)。

## <a name="remove-a-replication-policy"></a>删除复制策略

若要删除复制策略及其关联的规则，请使用 Azure 门户、PowerShell 或 CLI。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中删除复制策略，请按照以下步骤操作：

1. 在 Azure 门户中，转到源存储帐户。
1. 在“设置”下，选择“对象复制”。
1. 单击策略名称旁边的“更多”按钮。
1. 选择“删除规则”。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要删除复制策略，请从源帐户和目标帐户中都删除复制策略。 删除策略还会删除与之关联的任何规则。

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除复制策略，请从源帐户和目标帐户中都删除复制策略。 删除策略还会删除与之关联的任何规则。

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>后续步骤

- [块 blob 的对象复制](object-replication-overview.md)
- [防止跨 Azure Active Directory 租户进行对象复制](object-replication-prevent-cross-tenant-policies.md)
- [启用和管理 blob 版本控制](versioning-enable.md)
- [处理 Azure Blob 存储中的更改源](storage-blob-change-feed-how-to.md)
