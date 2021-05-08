---
title: Azure Database for PostgreSQL - 超大规模 (Citus) - 计划性维护 - Azure 门户
description: 了解如何从 Azure 门户为 Azure Database for PostgreSQL 超大规模 (Citus) 配置计划性维护设置。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8e22de5288380490490c91846322e418f98dfcd4
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108317249"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>管理 Azure Database for PostgreSQL 超大规模 (Citus) 的计划性维护设置

可为 Azure 订阅中的每个超大规模 (Citus) 服务器组指定维护选项。 选项包括即将进行的和已完成的维护事件的维护计划和通知设置。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- [Azure Database for PostgreSQL - 超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>指定维护计划选项

1. 在“超大规模(Citus)服务器组”页的“设置”标题下，选择“维护”以打开计划性维护选项 。
2. 默认（由系统管理的）计划是一周中随机的一天，30 分钟的维护时段从服务器组的 [Azure 区域时间](https://go.microsoft.com/fwlink/?linkid=2143646)的晚上 11 点到早上 7 点之间开始。 如果要自定义此计划，请选择“自定义计划”。 然后，可以选择一周中的首选日期和 30 分钟维护时段的开始时间。

## <a name="notifications-about-scheduled-maintenance-events"></a>有关计划维护事件的通知

可以使用 Azure 服务运行状况查看有关超大规模 (Citus) 服务器组上即将进行和已进行的计划性维护的[通知](../service-health/service-notifications.md)。 还可以在 Azure 服务运行状况中[设置](../service-health/resource-health-alert-monitor-guide.md)警报，以接收有关维护事件的通知。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Database for PostgreSQL 超大规模 (Citus) 中的计划性维护](concepts-hyperscale-maintenance.md)
* 了解 [Azure 服务运行状况](../service-health/overview.md)
