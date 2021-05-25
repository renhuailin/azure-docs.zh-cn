---
title: 还原 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 中执行还原操作。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "90934221"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>灵活服务器的时间点还原

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文介绍使用备份在灵活服务器中执行时间点恢复的分步过程。 可以执行到保留期内最早的还原点或自定义还原点。

## <a name="pre-requisites"></a>先决条件

若要完成本操作指南，需要：

-   必须具有 Azure Database for PostgreSQL 灵活服务器。 同样的过程也适用于配置了区域冗余的灵活服务器。

## <a name="restoring-to-the-earliest-restore-point"></a>还原到最早的还原点

按照以下步骤使用最早的现有备份还原灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2.  单击左侧面板中的“概述”，然后单击“还原” 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="还原概述":::

3.  随即将显示还原页面，其中包含一个可以在最早还原点和自定义还原点之间进行选择的选项。

4.  选择“最早还原点”，并在“还原到新服务器”字段中提供新的服务器名称 。 随即显示可以还原到的最早时间戳。 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="最早还原时间":::

5.  单击“确定”。

6.  随即显示还原操作已启动的通知。

## <a name="restoring-to-a-custom-restore-point"></a>还原到自定义还原点

按照以下步骤使用最早的现有备份还原灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2.  在“概述”页上单击“还原”。
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="还原概述":::
    
3.  随即将显示还原页面，其中包含一个可以在最早还原点和自定义还原点之间进行选择的选项。

4.  选择“自定义还原点”。

5.  选择日期和时间，并在“还原到新服务器”字段中提供新的服务器名称。 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="自定义还原时间":::
 
6.  单击“确定”。

7.  随即显示还原操作已启动的通知。

## <a name="next-steps"></a>后续步骤

-   了解[业务连续性](./concepts-business-continuity.md)
-   了解[区域冗余高可用性](./concepts-high-availability.md)
-   了解[备份和恢复](./concepts-backup-restore.md)
