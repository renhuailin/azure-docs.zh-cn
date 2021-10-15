---
title: 管理只读副本 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 了解如何使用 Azure 门户在 Azure Database for MySQL 灵活服务器中设置和管理只读副本。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2021
ms.openlocfilehash: 711b35ec11d42ae16a4b4db08d17e85b5217d41f
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387501"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>如何使用 Azure 门户在 Azure Database for MySQL 灵活服务器中创建和管理只读副本

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器中的只读副本现处于预览状态。

本文介绍如何使用 Azure 门户在 Azure Database for MySQL 灵活服务器中创建和管理只读副本。

> [!Note]
>
> * 启用高可用性的服务器不支持副本。 
> 
> * 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 灵活服务器。 请确保源服务器位于其中一个定价层中。
> * 如果在主服务器上启用了 GTID (`gtid_mode` = ON)，则新创建的副本也会启用 GTID 并使用基于 GTID 的复制。 若要了解详细信息，请参阅[全局事务标识符 (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

## <a name="prerequisites"></a>先决条件

- 将用作源服务器的 [Azure Database for MySQL 服务器灵活服务器](quickstart-create-server-portal.md)。

## <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
>如果为没有现有副本的源服务器创建副本，源服务器将首先重启，以便为复制做好准备。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下步骤创建只读副本服务器：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择要用作源的现有 Azure Database for MySQL 灵活服务器。 此操作将打开“概述”页。

3. 从菜单中的“设置”下，选择“复制”。

4. 选择“添加副本”。

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL - 复制":::

5. 输入副本服务器的名称。 如果你的区域支持可用性区域，可以选择你选择的可用性区域。

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL - 副本名称":::

6. 选择“确定”以确认创建该副本。

> [!NOTE]
> 只读副本是使用与源服务器相同的服务器配置创建的。 副本服务器配置在创建后可以更改。 始终会在与源服务器相同的资源组、位置和订阅中创建副本服务器。 如果要将副本服务器创建到不同的资源组或不同的订阅，可以在创建后[移动副本服务器](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 建议副本服务器的配置应始终采用与源服务器相同或更大的值，以确保副本能够与源服务器保持一致。

一旦创建副本服务器，可以从“复制”边栏选项卡中进行查看。

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL - 列出副本":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器

> [!IMPORTANT]
>停止复制到服务器操作不可逆。 一旦源服务器和副本服务器之间的复制停止，将无法撤消它。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

若要从 Azure 门户停止源服务器和副本服务器之间的复制，请使用以下步骤：

1. 在 Azure 门户中，选择源 Azure Database for MySQL 灵活服务器。 

2. 从菜单中的“设置”下，选择“复制”。

3. 选择要停止复制的副本服务器。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - 停止复制选择服务器":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. 选择“停止复制”。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL - 停止复制":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. 通过单击“确定”，确认要停止复制。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - 停止复制确认":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>删除副本服务器

若要从 Azure 门户删除只读副本服务器，请使用以下步骤：

1. 在 Azure 门户中，选择源 Azure Database for MySQL 灵活服务器。

2. 从菜单中的“设置”下，选择“复制”。

3. 选择要删除的副本服务器。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - 删除副本选择服务器":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. 选择“删除副本”

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - 删除副本":::

5. 键入副本的名称，然后单击“删除”以确认删除副本。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - 删除副本确认":::

## <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
>删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要从 Azure 门户删除源服务器，请使用以下步骤：

1. 在 Azure 门户中，选择源 Azure Database for MySQL 灵活服务器。

2. 从“概览”中，选择“删除”。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL - 删除源":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. 键入源服务器的名称，然后单击“删除”以确认删除源服务器。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL - 删除源确认":::

## <a name="monitor-replication"></a>监视复制

1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的副本 Azure Database for MySQL 灵活服务器。

2. 在边栏的“监视”部分，选择“指标”：

3. 从可用指标的下拉列表中选择“复制延迟(秒)”。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="选择复制延迟时间":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. 选择要查看的时间范围。 下图选择 30 分钟的时间范围。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="选择时间范围":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. 查看所选时间范围的复制延迟时间。 下图显示过去的 30 分钟。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="选择时间范围“30 分钟”":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 详细了解[只读副本](concepts-read-replicas.md)