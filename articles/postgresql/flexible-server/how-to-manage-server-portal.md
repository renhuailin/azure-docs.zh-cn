---
title: 管理服务器 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 了解如何通过 Azure 门户管理 Azure Database for PostgreSQL 灵活服务器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91961402"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户管理 Azure Database for PostgreSQL 灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文介绍如何管理 Azure Database for PostgreSQL 灵活服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

## <a name="sign-in"></a>登录

登录 [Azure 门户](https://portal.azure.com)。 请在 Azure 门户中转到灵活服务器资源。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

创建服务器后，可以根据需要在不同[定价层](https://azure.microsoft.com/pricing/details/postgresql/)之间进行缩放。 也可以通过增加或减少 vCore 来扩展或缩减计算和内存。

> [!NOTE]
> 存储无法纵向缩减到较小值。

1. 在 Azure 门户中选择服务器。 选择位于“设置”部分的“计算 + 存储” 。
2. 可以更改计算层、vCore 和存储，使用更高的计算层纵向扩展服务器或通过将存储或 vCore 增加到所需的值在同一层中纵向扩展服务器  。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="缩放存储灵活服务器":::

> [!Important]
> - 存储无法纵向缩减。
> - 缩放 vCore 会导致服务器重启。

3. 选择“确定”以保存更改。 

## <a name="reset-admin-password"></a>重置管理员密码

可以通过 Azure 门户更改管理员角色的密码。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“重置密码”   。
2. 输入新密码并确认。 文本框会就密码复杂性要求提示你。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="重置灵活服务器的密码":::

3. 选择“保存”，以保存新密码。

## <a name="delete-a-server"></a>删除服务器

不再需要服务器时，可以将其删除。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“删除”。  
2. 在输入框中键入服务器名称，以确认你想要删除该服务器。

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="删除灵活服务器":::

   > [!IMPORTANT]
   > 删除服务器的操作不可逆。

  > [!div class="mx-imgBorder"]
  > ![删除灵活服务器](./media/howto-manage-server-portal/delete-server.png)  

3. 选择“删除”。

## <a name="next-steps"></a>后续步骤

- [了解备份和还原概念](concepts-backup-restore.md)
- [优化和监视服务器](concepts-monitoring.md)
