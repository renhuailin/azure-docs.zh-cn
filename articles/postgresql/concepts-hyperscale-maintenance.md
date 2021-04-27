---
title: 计划性维护 - Azure Database for PostgreSQL 超大规模 (Citus)
description: 本文介绍了 Azure Database for PostgreSQL 超大规模 (Citus) 中的计划性维护功能。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106779"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 超大规模 (Citus) 中的计划性维护

Azure Database for PostgreSQL 超大规模 (Citus) 执行定期维护，以确保托管数据库安全、稳定并处于最新状态。  在维护期间，服务器组中的所有节点都会获取新功能、更新和修补程序。

超大规模 (Citus) 的计划性维护的主要功能包括：

* 更新会同时对服务器组中的所有节点应用
* 关于即将进行的维护的通知会提前 5 天发布到 Azure 服务运行状况
* 通常，服务器组的成功维护事件间隔至少为 30 天
* 可以分别为每个服务器组定义一周中的首选日期和当天维护时段的开始时间

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>选择维护时段和关于即将进行的维护的通知

你可以计划在一周中特定某天以及当天某个时段范围内进行维护。 或者，你可以让系统自动选择某一天和某个时段。 无论采用哪种方式，系统都会在运行任何维护之前提前 5 天提醒你。 系统还会在维护开始时以及在维护成功完成时告知你。

关于即将进行的计划性维护的通知可以通过以下方式发布到 Azure 服务运行状况：

* 通过电子邮件发送到特定地址
* 通过电子邮件发送到 Azure 资源管理器角色
* 以短信 (SMS) 形式发送到移动设备
* 作为通知推送到 Azure 应用
* 作为语音消息传递

为维护计划指定首选项时，可以选择一周中的某一天，然后选择一个时间范围。 如果未指定，系统将选择服务器组区域时间中的晚上 11 点到早上 7 点之间的时间。 可为 Azure 订阅中的每个超大规模 (Citus) 服务器组定义不同的计划。

> [!IMPORTANT]
> 通常，服务器组的成功计划性维护事件间隔至少为 30 天。
>
> 但如果发生关键紧急更新（如严重漏洞），通知时间范围可能会短于五天。 即使在过去 30 天内成功地执行了计划性维护，关键更新也可能会应用于服务器。

你可以随时更新计划设置。 如果已存在为超大规模 (Citus) 服务器组制定的维护计划并且你更新了该计划，现有事件将仍按原计划继续进行。 成功完成现有事件后，设置更改才会生效。

如果维护失败或被取消，系统将创建通知。
系统将根据当前的计划设置再次尝试进行维护，并在下一次维护事件发生前提前 5 天通知你。

## <a name="next-steps"></a>后续步骤

* 了解如何[更改维护计划](howto-hyperscale-maintenance.md)
* 了解如何使用 Azure 服务运行状况[获取有关即将进行的维护的通知](../service-health/service-notifications.md)
* 了解如何[设置有关即将发生的计划性维护事件的警报](../service-health/resource-health-alert-monitor-guide.md)
