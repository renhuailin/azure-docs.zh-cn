---
title: 管理服务器 - Azure 门户 - Azure Database for MySQL
description: 了解如何在 Azure 门户中管理 Azure Database for MySQL 服务器。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 1673b533e70ee4c15210cc9e8be042af162b7a42
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653056"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>使用 Azure 门户管理 Azure Database for MySQL 服务器

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍如何管理 Azure Database for MySQL 服务器。 管理任务包括计算和存储缩放、管理员密码重置，以及查看服务器详细信息。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
>

## <a name="sign-in"></a>登录

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-server"></a>创建服务器

访问[快速入门](quickstart-create-mysql-server-database-using-azure-portal.md)，了解如何创建并开始使用 Azure Database for MySQL 服务器。

## <a name="scale-compute-and-storage"></a>缩放计算和存储

创建服务器后，可以根据需要在“常规用途”层和“内存优化”层之间缩放。 也可通过增减 vCore 数来缩放计算和内存。 存储可以纵向扩展（但不能纵向缩减）。

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>在“常规用途”和“内存优化”层之间缩放

可以从“常规用途”层缩放到“内存优化”层，反之亦然。 不支持在创建服务器后在“基本”层和其他层之间来回更改。

1. 在 Azure 门户中选择服务器。 选择位于“设置”部分的“定价层”。  

2. 选择“常规用途”或“内存优化”，具体取决于你要缩放到哪一层。  

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中选择“基本”、“常规用途”或“内存优化”层":::

   > [!NOTE]
   > 更改层级导致服务器重启。

3. 选择“确定”以保存更改。 

### <a name="scale-vcores-up-or-down"></a>纵向扩展或缩减 vCore

1. 在 Azure 门户中选择服务器。 选择位于“设置”部分的“定价层”。  

2. 通过将滑块移动到所需的值来更改“vCore”  设置。

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中选择 vCore 选项":::

    > [!NOTE]
    > 缩放 vCore 会导致服务器重启。

3. 选择“确定”以保存更改。 

### <a name="scale-storage-up"></a>纵向扩展存储

1. 在 Azure 门户中选择服务器。 选择位于“设置”部分的“定价层”。  

2. 通过将滑块向上移动到所需的值来更改“存储”  设置。

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中选择存储规模":::

   > [!NOTE]
   > 存储无法纵向缩减。

3. 选择“确定”以保存更改。 

## <a name="update-admin-password"></a>更新管理员密码

可以通过 Azure 门户更改管理员角色的密码。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“重置密码”   。

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中重置密码":::

2. 输入新密码并确认。 文本框会就密码复杂性要求提示你。

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中重置密码并进行保存":::

3. 选择“确定”以保存新密码。 
 

> [!IMPORTANT]
> 重置服务器管理员密码会将服务器管理员权限自动重置为默认值。 如果不小心撤消了一个或多个服务器管理员权限，请考虑重置服务器管理员密码。
   
> [!NOTE]
> 服务器管理员用户默认拥有以下权限：SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER

## <a name="delete-a-server"></a>删除服务器

不再需要服务器时，可以将其删除。

1. 在 Azure 门户中选择服务器。 在“概览”窗口中，选择“删除”。  

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中删除服务器":::

2. 在输入框中键入服务器名称，确认这是要删除的服务器。

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Azure 门户的屏幕截图，该门户用于在 Azure Database for MySQL 中确认服务器删除操作":::

   > [!NOTE]
   > 删除服务器的操作不可逆。

3. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

- 了解[备份和服务器还原](howto-restore-server-portal.md)
- 了解 [Azure Database for MySQL 中的优化和监视选项](concepts-monitoring.md)
