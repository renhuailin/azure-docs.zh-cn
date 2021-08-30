---
title: 还原 - 超大规模 (Citus) - Azure Database for PostgreSQL - Azure 门户
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 超大规模 (Citus) 中执行还原操作。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/09/2021
ms.openlocfilehash: fc52bac390763418d21cb001e5ae224acbaaa906
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595518"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>超大规模 (Citus) 服务器组的时间点还原

本文逐步介绍如何对使用备份的超大规模 (Citus) 服务器组执行[时间点恢复](concepts-hyperscale-backup.md#restore)。 可以还原到最早的备份或保持期内的自定义还原点。

## <a name="restoring-to-the-earliest-restore-point"></a>还原到最早的还原点

按照以下步骤将超大规模 (Citus) 服务器组还原到现有的最早备份。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要还原的服务器组。

2.  单击左侧面板中的“概述”，然后单击“还原” 。

    > [!IMPORTANT]
    > 如果服务器组还没有“还原”按钮，请建立 Azure 支持请求以还原服务器组。

3.  还原页将要求你在“最早”和“自定义”还原点之间选择，并且该页将显示最早的日期 。

4.  选择“最早还原点”。

5.  在“还原到新服务器”字段中提供新的服务器组名称。 显示其他字段（订阅、资源组和位置），但不可编辑。

6.  单击“确定”。

7.  随即显示还原操作已启动的通知。

最后，请遵循[还原后任务](#post-restore-tasks)。

## <a name="restoring-to-a-custom-restore-point"></a>还原到自定义还原点

按照以下步骤将超大规模 (Citus) 服务器组还原到你选择的日期和时间。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择要还原的服务器组。

2.  单击左侧面板中的“概述”，然后单击“还原” 

    > [!IMPORTANT]
    > 如果服务器组还没有“还原”按钮，请创建还原该服务器组的 Azure 支持请求。

3.  还原页将要求你在“最早”和“自定义”还原点之间选择，并且该页将显示最早的日期 。

4.  选择“自定义还原点”。

5.  选择“还原点 (UTC)”的日期和时间，并在“还原到新服务器”字段中提供新的服务器组名称 。 显示其他字段（订阅、资源组和位置），但不可编辑。
 
6.  单击“确定”。

7.  随即显示还原操作已启动的通知。

最后，请遵循[还原后任务](#post-restore-tasks)。

## <a name="post-restore-tasks"></a>还原后任务

还原后，应执行以下任务，然后用户和应用程序才能重新运行：

* 如果需要使用新服务器来替换原始服务器，则请将客户端和客户端应用程序重定向到新服务器
* 对于要进行连接的用户，请确保设置适当的服务器级防火墙。 这些规则不会从原始服务器组进行复制。
* 请根据需要调整 PostgreSQL 服务器参数。 参数不会从原始服务器组进行复制。
* 确保设置适当的登录名和数据库级权限。
* 视情况配置警报。

## <a name="next-steps"></a>后续步骤

* 详细了解超大规模 (Citus) 中的[备份和还原](concepts-hyperscale-backup.md)。
* 在超大规模 (Citus) 服务器组上设置 [建议的警报](./howto-hyperscale-alert-on-metric.md#suggested-alerts)。
