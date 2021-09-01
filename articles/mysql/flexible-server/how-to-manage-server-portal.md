---
title: 管理服务器 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 了解如何通过 Azure 门户管理 Azure Database for MySQL 灵活服务器。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 5500a197b7cb2566b3ea78b6c2acba9403a7a8ae
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652729"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>使用 Azure 门户管理 Azure Database for MySQL 灵活服务器（预览版）

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍如何管理 Azure Database for MySQL 灵活服务器（预览版）。 管理任务包括计算和存储缩放、重置服务器管理员密码和删除服务器。

## <a name="sign-in"></a>登录

登录 [Azure 门户](https://portal.azure.com)。 请在 Azure 门户中转到灵活服务器资源。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

创建服务器后，可以根据需要在不同[定价层](https://azure.microsoft.com/pricing/details/mysql/)之间进行缩放。 也可以通过增加或减少 vCore 来扩展或缩减计算和内存。

1. 在 Azure 门户中选择服务器。 选择位于“设置”部分的“计算 + 存储” 。

2. 可以更改计算层、vCore 和存储，使用更高的计算层纵向扩展服务器或通过将存储或 vCore 增加到所需的值在同一层中纵向扩展服务器  。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="缩放存储灵活服务器":::

   
> [!IMPORTANT]
   > - 存储无法纵向缩减。
   > - 缩放 vCore 会导致服务器重启。

3. 选择“确定”以保存更改。 

## <a name="reset-admin-password"></a>重置管理员密码

可以通过 Azure 门户更改管理员角色的密码。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“重置密码” 。

2. 输入新密码并确认。 文本框会就密码复杂性要求提示你。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="重置灵活服务器的密码":::

3. 选择“保存”，以保存新密码。

## <a name="delete-a-server"></a>删除服务器

不再需要服务器时，可以将其删除。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“删除”。  

2. 在输入框中键入服务器名称，以确认你想要删除该服务器。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="删除灵活服务器":::

   > [!NOTE]
   > 删除服务器的操作不可逆。

3. 选择“删除”。 

## <a name="next-steps"></a>后续步骤
- [了解如何启动或停止服务器](how-to-stop-start-server-portal.md)
- [了解如何还原服务器](how-to-restore-server-portal.md)
- [排查连接问题](how-to-troubleshoot-common-connection-issues.md)

