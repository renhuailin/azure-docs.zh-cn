---
title: Azure Database for MySQL 灵活服务器（预览）- 计划性维护 - Azure 门户
description: 了解如何从 Azure 门户为 Azure Database for MySQL 灵活服务器配置计划性维护设置。
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a4ff27243643a5c0fbb833a05a07e9e117196a6f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653174"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>管理 Azure Database for MySQL 灵活服务器中的计划性维护设置

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

可为 Azure 订阅中的每个灵活服务器指定维护选项。 选项包括即将进行的和已完成的维护事件的维护计划和通知设置。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器处于预览状态。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- [Azure Database for MySQL 灵活服务器](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>指定维护计划选项
 
1. 在“MySQL 服务器”页的“设置”标题下，选择“维护”以打开计划性维护选项 。
2. 默认（由系统管理的）计划是一周中随机的一天，60 分钟的维护时段从本地服务器时间的晚上 11 点到早上 7 点之间开始。 如果要自定义此计划，请选择“自定义计划”。 然后，可以选择一周中的首选日期和 60 分钟维护时段的开始时间。
 
## <a name="notifications-about-scheduled-maintenance-events"></a>有关计划维护事件的通知
 
可以使用 Azure 服务运行状况查看有关灵活服务器上即将进行和已执行的计划维护的[通知](../../service-health/service-notifications.md)。 还可以在 Azure 服务运行状况中[设置](../../service-health/resource-health-alert-monitor-guide.md)警报，以接收有关维护事件的通知。
 
## <a name="next-steps"></a>后续步骤  
 
* 了解 [Azure Database for MySQL 灵活服务器中的计划性维护](concepts-maintenance.md)
* 了解 [Azure 服务运行状况](../../service-health/overview.md)
