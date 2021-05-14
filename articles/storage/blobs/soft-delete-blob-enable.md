---
title: 为 blob 启用软删除
titleSuffix: Azure Storage
description: 启用 blob 的软删除，防止意外删除或覆盖 blob 数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a1698a56ad7e92a59b664ce8f8bca2355fb44fb1
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108074702"
---
# <a name="enable-soft-delete-for-blobs"></a>为 blob 启用软删除

Blob 软删除会在系统中将已删除的数据保留指定的一段时间，可以在意外删除或覆盖单个 Blob 及其版本、快照和元数据时提供保护。 在保留期内，可以将 Blob 还原到它在删除时的状态。 在指定的保留期已过后，Blob 将永久被删除。 有关 Blob 软删除的详细信息，请参阅 [Blob 的软删除](soft-delete-blob-overview.md)。

Blob 软删除是针对 Blob 数据的综合性数据保护策略的一部分。 若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

## <a name="enable-blob-soft-delete"></a>启用 blob 软删除

对于新存储帐户，默认情况下 blob 软删除处于禁用状态。 你随时都可使用 Azure 门户、PowerShell 或 Azure CLI 为存储帐户启用或禁用软删除。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户为存储帐户启用 blob 软删除，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到存储帐户。
1. 在“Blob 服务”下，找到“数据保护”选项 。
1. 在“恢复”部分中，选择“启用 blob 软删除” 。
1. 指定 1 到 365 天的保持期。 Microsoft 建议使用 7 天的最短保持期。
1. 保存所做更改。

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="显示如何在 Azure 门户中启用软删除的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 启用 blob 软删除，请调用 [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) 命令，并指定保持期（以天为单位）。

下面的示例启用了 blob 软删除并将保持期设置为 7 天。 请记得将括号中的占位符值替换为你自己的值：

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

若要检查 blob 软删除的当前设置，请调用 [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) 命令：

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

若要使用 Azure CLI 启用 blob 软删除，请调用 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 命令，并指定保持期（以天为单位）。

下面的示例启用了 blob 软删除并将保持期设置为 7 天。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

若要检查 blob 软删除的当前设置，请调用 [az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) 命令：

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>后续步骤

- [blob 的软删除](soft-delete-blob-overview.md)
- [管理和还原软删除的 Blob](soft-delete-blob-manage.md)
