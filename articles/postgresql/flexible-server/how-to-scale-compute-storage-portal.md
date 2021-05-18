---
title: 缩放操作 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中执行缩放操作。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90934373"
---
# <a name="scale-operations-in-flexible-server"></a>在灵活服务器中执行缩放操作

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文提供了对计算和存储执行缩放操作的步骤。 你将能够在可突发、常规用途和内存优化 SKU 之间更改计算层，包括选择适合运行应用程序的 vCore 数。 还可以纵向扩展存储。 预期的 IOPS 基于计算层、vCore 数和存储容量显示。 还会根据你的选择显示成本估算值。

> [!IMPORTANT]
> 无法纵向缩减存储。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   必须具有 Azure Database for PostgreSQL 灵活服务器。 同样的过程也适用于配置了区域冗余的灵活服务器。
> [!IMPORTANT]
> 如果配置了高可用性，则不能选择可突发 SKU。 在缩放操作期间，首先会将备用服务器调整为所需大小，对主服务器进行故障转移，然后缩放主服务器。 

## <a name="scaling-the-compute-tier-and-size"></a>缩放计算层和大小

请按照以下步骤选择计算层。
 
1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2.  单击“计算 + 存储”。

3.  随即显示一个包含当前设置的页面。
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="“计算 + 存储”视图":::

4.  可在可突发、常规用途和内存优化层之间选择计算类。
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="列出计算层":::


5.  如果你认为使用默认 vCore 和内存大小即可，则可以跳过下一步。

6.  如果想要更改 vCore 数，可以单击“计算大小”下拉列表，然后从列表中单击所需的 vCore 数/内存。
    
    - 可突发计算层：:::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="可突发计算":::

    - 常规用途计算层：:::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="常规用途计算":::

    - 内存优化计算层：:::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="内存优化计算":::

7.  单击“保存”。 
8.  将显示确认消息。 如果想要继续，单击“确定”。 
9.  有关正在进行缩放操作的通知。


## <a name="scaling-storage-size"></a>缩放存储大小

请按以下步骤增加存储大小。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要为其增加存储大小的灵活服务器。
2.  单击“计算 + 存储”。

3.  随即显示一个包含当前设置的页面。
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="单击“计算 + 存储”":::
4.  带有滑动条的字段“存储大小(GiB)”以当前大小显示。

5.  将滑动条滑至所需大小。 系统将显示相应 IOPS 数。 IOPS 取决于计算层和大小。 系统还会显示成本信息。 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="存储纵向扩展":::

6.  如果你认为使用该存储大小即可，请单击“保存”。 
7.  将显示确认消息。 如果想要继续，单击“确定”。 
8.  有关正在进行缩放操作的通知。

## <a name="next-steps"></a>后续步骤

-   了解[业务连续性](./concepts-business-continuity.md)
-   了解[高可用性](./concepts-high-availability.md)
-   了解[备份和恢复](./concepts-backup-restore.md)
