---
title: 将 Azure 资源管理器锁应用于存储帐户
titleSuffix: Azure Storage
description: 了解如何将 Azure 资源管理器锁应用于存储帐户。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799779"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>将 Azure 资源管理器锁应用于存储帐户

Microsoft 建议使用 Azure 资源管理器锁锁定所有存储帐户，以防意外或恶意删除存储帐户。 有两种类型的 Azure 资源管理器资源锁：

- CannotDelete 锁可防止用户删除存储帐户，但允许读取和修改其配置。
- ReadOnly 锁可防止用户删除存储帐户或修改其配置，但允许读取配置。

有关 Azure 资源管理器锁的详细信息，请参阅[锁定资源以防止更改](../../azure-resource-manager/management/lock-resources.md)。

> [!CAUTION]
> 锁定存储帐户不会阻止对其中容器或 blob 执行的删除或覆盖操作。 有关如何保护 blob 数据的详细信息，请参阅[数据保护概述](../blobs/data-protection-overview.md)。

## <a name="configure-an-azure-resource-manager-lock"></a>配置 Azure 资源管理器锁

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要通过 Azure 门户配置存储帐户的锁，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”部分下，选择“锁”。
1. 选择 **添加** 。
1. 提供资源锁的名称，然后指定锁的类型。 如果需要，请添加有关锁的说明。

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="显示如何使用 CannotDelete 锁锁定存储帐户的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 配置存储帐户的锁，首先请确保已安装 [Az PowerShell 模块](https://www.powershellgallery.com/packages/Az)。 接下来，调用 [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 命令并指定要创建的锁的类型，如以下示例所示：

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 配置存储帐户的锁，请调用 [az lock create](/cli/azure/lock#az_lock_create) 命令并指定要创建的锁的类型，如以下示例所示：

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>当 ReadOnly 锁有效时授权数据操作

将 ReadOnly 锁应用于存储帐户时，将阻止该存储帐户的[列出密钥](/rest/api/storagerp/storageaccounts/listkeys)操作。 “列出密钥”操作是 HTTPS POST 操作，并且在为该帐户配置了 ReadOnly 锁时，所有 POST 操作都会被阻止。 “列出密钥”操作返回帐户访问密钥，然后可以使用这些密钥来读取和写入存储帐户中的任何数据。

如果在将锁应用于存储帐户时客户端拥有帐户访问密钥，则该客户端可以继续使用这些密钥来访问数据。 但是，无权访问密钥的客户端需要使用 Azure Active Directory (Azure AD) 凭据来访问存储帐户中的 blob 或队列数据。

如果 Azure 门户的用户以前已使用帐户访问密钥访问了门户中的 blob 或队列数据，则在应用了 ReadOnly 锁时可能会影响这些用户。 应用锁后，门户用户需要使用 Azure AD 凭据来访问门户中的 blob 或队列数据。 若要执行此操作，用户必须至少为其分配两个 RBAC 角色：至少 Azure 资源管理器读者角色，以及 Azure 存储数据访问角色之一。 有关详细信息，请参阅以下文章之一：

- [选择如何在 Azure 门户中授予对 blob 数据的访问权限](../blobs/authorize-data-operations-portal.md)
- [选择如何在 Azure 门户中授予对队列数据的访问权限](../queues/authorize-data-operations-portal.md)

以前使用帐户密钥访问 Azure 文件中的数据或表服务的客户端可能变得无法访问。 最佳做法是，如果必须将 ReadOnly 锁应用于存储帐户，则将 Azure 文件和表服务工作负载移至未使用 ReadOnly 锁锁定的存储帐户。

## <a name="next-steps"></a>后续步骤

- [数据保护概述](../blobs/data-protection-overview.md)
- [锁定资源以防止更改](../../azure-resource-manager/management/lock-resources.md)
