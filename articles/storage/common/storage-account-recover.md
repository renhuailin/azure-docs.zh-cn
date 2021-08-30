---
title: 恢复删除的存储帐户
titleSuffix: Azure Storage
description: 了解如何在 Azure 门户中恢复已删除的存储帐户。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3a26a181dc8fd25ad93ebba9aaa496f3421bbffc
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354576"
---
# <a name="recover-a-deleted-storage-account"></a>恢复删除的存储帐户

在某些情况下，可以从 Azure 门户中恢复已删除的存储帐户。 若要恢复存储帐户，必须满足以下条件：

- 存储帐户在过去 14 天内删除。
- 存储帐户是使用 Azure 资源管理器部署模型创建的。
- 删除原始帐户后，尚未创建同名的新存储帐户。
- 对于要恢复存储帐户的用户，必须为其分配提供 Microsoft.Storage/storageAccounts/write 权限的 Azure RBAC 角色。 有关提供此权限的内置 Azure RBAC 角色的信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。 

在尝试恢复已删除的存储帐户之前，请确保该帐户的资源组存在。 如果资源组已删除，则必须重新创建它。 无法恢复资源组。 有关详细信息，请参阅[管理资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md)。

如果已删除的存储帐户将客户管理的密钥与 Azure 密钥保管库一起使用，并且密钥保管库也已删除，则必须先还原密钥保管库，然后再还原存储帐户。 有关详细信息，请参阅 [Azure 密钥保管库恢复概述](../../key-vault/general/key-vault-recovery.md)。

> [!IMPORTANT]
> 不保证恢复已删除的存储帐户。 我们将尽力尝试恢复。 Microsoft 建议锁定资源以防止意外删除帐户。 有关资源锁定的详细信息，请参阅[锁定资源以防止更改](../../azure-resource-manager/management/lock-resources.md)。
>
> 避免意外删除帐户的另一种最佳做法是限制有权通过基于角色的访问控制 (Azure RBAC) 删除帐户的用户数量。 有关详细信息，请参阅 [Azure RBAC 的最佳做法](../../role-based-access-control/best-practices.md)。

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>从 Azure 门户还原已删除的帐户

若要从另一个存储帐户中恢复已删除的存储帐户，请按照以下步骤执行操作：

1. 导航到 Azure 门户中现有存储帐户的概述页面。
1. 在“支持 + 故障排除”部分中，选择“恢复已删除的帐户”。
1. 从下拉列表中选择要恢复的帐户，如下图所示。 如果要恢复的存储帐户不在下拉列表中，则无法恢复。

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="显示如何在 Azure 门户中恢复存储帐户的屏幕截图":::

1. 选择“恢复”按钮以还原帐户。 门户会显示恢复正在进行的通知。

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>通过支持票证恢复已删除的帐户

1. 在 Azure 门户中，导航到“帮助和支持”。
1. 选择“新建支持请求”。
1. 在“基本信息”选项卡上的“问题类型”字段中，选择“技术”。
1. 在“订阅”字段中，选择包含已删除存储帐户的订阅。
1. 在“服务”字段中，选择“存储帐户管理”。
1. 在“资源”字段中，选择任意存储帐户资源。 已删除的存储帐户将不会出现在列表中。
1. 添加问题的简短摘要。
1. 在“问题类型”字段中，选择“删除和恢复”。
1. 在“问题子类型”字段中，选择“恢复已删除的存储帐户”。 下图显示了要填写的“基本信息”选项卡的示例：

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="显示如何通过支持票证 -“基本信息”选项卡恢复存储帐户的屏幕截图":::

1. 接下来，导航到“解决方案”选项卡，并选择“客户控制的存储帐户恢复”，如下图所示：

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="显示如何通过支持票证 -“解决方案”选项卡恢复存储帐户的屏幕截图":::

1. 从下拉列表中选择要恢复的帐户，如下图所示。 如果要恢复的存储帐户不在下拉列表中，则无法恢复。

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="显示如何通过支持票证恢复存储帐户的屏幕截图":::

1. 选择“恢复”按钮以还原帐户。 门户会显示恢复正在进行的通知。

## <a name="next-steps"></a>后续步骤

- [存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-account-create.md)
- [升级到常规用途 v2 存储帐户](storage-account-upgrade.md)
- [将 Azure 存储帐户移到另一个区域](storage-account-move.md)
