---
title: 管理 Azure 存储帐户的默认访问层
titleSuffix: Azure Storage
description: 了解如何更改常规用途 v2 或 Blob 存储帐户的默认访问层
author: tamram
ms.author: tamram
ms.date: 09/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a9cb4a119447188202036cca4ed4d8580329d0cc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272559"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>管理 Azure 存储帐户的默认访问层

每个 Azure 存储帐户都有默认访问层：热访问层或冷访问层。 在创建存储帐户时分配访问层。 默认访问层为热访问层。

可以通过设置存储帐户中的“访问层”属性来更改默认帐户层。 更改帐户层适用于存储在帐户中的所有对象，前提是该帐户没有设置一个显式层。 将帐户层从热切换为冷只对常规用途 v2 帐户中没有设置层的所有 Blob 产生写入操作次数（以 10,000 次为单位）收费，而从冷切换为热则会对 Blob 存储和常规用途 v2 帐户中的所有 Blob 产生读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费。

对于已在对象级别设置该层的 Blob，不会应用帐户层。 存档层仅适用于对象级别。 可以随时在访问层之间进行切换。

在按照以下步骤创建存储帐户后，可以更改默认访问层。

## <a name="change-the-default-account-access-tier-of-a-general-purpose-v2-or-blob-storage-account"></a>更改常规用途 v2 或 Blob 存储帐户的默认帐户访问层

以下方案使用 Azure 门户或 PowerShell：

# <a name="portal"></a>[门户](#tab/portal)

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择“所有资源”。

1. 选择存储帐户。

1. 在“设置”中，选择“配置”以查看和更改帐户配置 。

1. 根据需求选择合适的访问层：将“访问层”设置为“冷”或“热”。

1. 单击顶部的“保存”。

![在 Azure 门户中更改默认帐户层](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下 PowerShell 脚本可用于更改帐户层。 必须使用资源组名称初始化 `$rgName` 变量。 必须使用存储帐户名称初始化 `$accountName` 变量。

```powershell
# Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

# Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

以下 Azure CLI 脚本可用于更改帐户层。 必须使用资源组名称初始化 `$rgName` 变量。 必须使用你的存储帐户名初始化 `$accountName` 变量。

```azurecli
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
az storage account update --resource-group $rgName --name $accountName --access-tier Hot
```

---

## <a name="next-steps"></a>后续步骤

- [如何管理 Azure 存储帐户中 Blob 的层](../blobs/manage-access-tier.md)
- [确定高级性能是否会使应用受益](../blobs/storage-blob-performance-tiers.md)
- [通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../blobs/monitor-blob-storage.md)
