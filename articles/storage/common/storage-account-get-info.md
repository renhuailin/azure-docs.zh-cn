---
title: 获取存储帐户配置信息
titleSuffix: Azure Storage
description: 使用 Azure 门户、PowerShell 或 Azure CLI 检索存储帐户配置属性，包括 Azure 资源管理器资源 ID、帐户位置、帐户类型或复制 SKU。
services: storage
author: tamram
ms.author: tamram
ms.date: 06/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: f3f94dda19f11b1a0aad9a84e7ae624e41c5015c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573645"
---
# <a name="get-storage-account-configuration-information"></a>获取存储帐户配置信息

本文介绍如何使用 Azure 门户、PowerShell 或 Azure CLI 获取 Azure 存储帐户的配置信息和属性。

## <a name="get-the-resource-id-for-a-storage-account"></a>获取存储帐户的资源 ID

每个 Azure 资源管理器资源都有一个对其唯一标识的关联资源 ID。 某些操作需要提供资源 ID。 你可以通过使用 Azure 门户、PowerShell 或 Azure CLI 获取存储帐户的资源 ID。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中显示存储帐户的 Azure 资源管理器的资源 ID，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“概述”页面的“Essentials”分区，选择“JSON 视图”链接。  
1. 存储帐户的资源 ID 显示在页面顶部。

    :::image type="content" source="media/storage-account-get-info/resource-id-portal.png" alt-text="显示如何从门户复制存储帐户的资源 ID 的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 返回存储帐户的 Azure 资源管理器资源 ID，请确保已安装 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 模块。 接下来，调用 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 命令以返回存储帐户并获取其资源 ID：

```azurepowershell
(Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 返回存储帐户的 Azure 资源管理器的资源 ID，调用 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 命令并查询资源 ID：

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query id \
    --output tsv
```

---

还可通过在 REST API 中调用[“帐户 - 获取属性”](/rest/api/storagerp/storage-accounts/get-properties)操作来获取存储帐户的资源 ID。

有关 Azure 资源管理器管理的资源类型的详细信息，请参阅[资源提供程序和资源类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="get-the-account-type-location-or-replication-sku-for-a-storage-account"></a>获取存储帐户的帐户类型、位置或复制 SKU

帐户类型、位置和复制 SKU 是存储帐户上的一些可用属性。 可以使用 Azure 门户、PowerShell 或 Azure CLI 来查看这些值。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中查看存储帐户的帐户类型、位置或复制 SKU，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在概述页面的“Essentials”分区中找到这些属性 

    :::image type="content" source="media/storage-account-get-info/account-configuration-portal.png" alt-text="显示门户中帐户配置的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 查看存储帐户的帐户类型、位置或复制 SKU，请调用 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 命令以返回存储帐户，然后检查属性：

```azurepowershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>
$account.Location
$account.Sku
$account.Kind
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 PowerShell 查看存储帐户的帐户类型、位置或复制 SKU，请调用 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 命令并查询属性：

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query '[location,sku,kind]' \
    --output tsv
```

---

## <a name="next-steps"></a>后续步骤

- [存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-account-create.md)
