---
title: 还原 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for MySQL 中执行还原操作。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241950"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器（预览版）的时间点还原


> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍使用备份在灵活服务器中执行时间点恢复的分步过程。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

-   必须具备 Azure Database for MySQL 灵活服务器。

## <a name="restore-to-the-latest-restore-point"></a>还原到最新还原点

按照以下步骤使用最早的现有备份还原灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2.  单击左侧面板中的“概述”。

3.  在“概述”页上单击“还原”。

    [占位符]

4.  随即将显示还原页面，其中包含一个可以在最新还原点和自定义还原点之间进行选择的选项。

5.  选择“最新还原点”。


6.  在“还原到新服务器”字段中提供新的服务器名称。

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="最早还原时间":::

8.  单击“确定”。

9.  随即显示还原操作已启动的通知。

## <a name="restoring-to-a-custom-restore-point"></a>还原到自定义还原点

按照以下步骤使用最早的现有备份还原灵活服务器。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择想要从其中还原备份的灵活服务器。

2.  在“概述”页上单击“还原”。

    [占位符]

3.  随即将显示还原页面，其中包含一个可以在最早还原点和自定义还原点之间进行选择的选项。

4.  选择“自定义还原点”。

5.  选择日期和时间。

6.  在“还原到新服务器”字段中提供新的服务器名称。

6.  在“还原到新服务器”字段中提供新的服务器名称。 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="查看概述":::
 
7.  单击“确定”。

8.  随即显示还原操作已启动的通知。

## <a name="next-steps"></a>后续步骤

占位符
