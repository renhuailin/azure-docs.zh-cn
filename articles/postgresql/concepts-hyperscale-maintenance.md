---
title: 计划性维护 - Azure Database for PostgreSQL 超大规模 (Citus)
description: 本文介绍了 Azure Database for PostgreSQL 超大规模 (Citus) 中的计划性维护功能。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027444"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 超大规模 (Citus) 中的计划性维护

Azure Database for PostgreSQL 超大规模 (Citus) 执行定期维护，以确保托管数据库安全、稳定并处于最新状态。  在维护期间，服务器组中的所有节点都会获取新功能、更新和修补程序。

超大规模 (Citus) 的计划性维护的主要功能包括：

* 更新会同时对服务器组中的所有节点应用
* 关于即将进行的维护的通知会提前 5 天发布到 Azure 服务运行状况
* 通常，服务器组的成功维护事件间隔至少为 30 天

## <a name="notification-about-upcoming-maintenance"></a>关于即将进行的维护的通知

关于即将进行的计划性维护的通知可以通过以下方式发布到 Azure 服务运行状况：

* 通过电子邮件发送到特定地址
* 通过电子邮件发送到 Azure 资源管理器角色
* 以短信 (SMS) 形式发送到移动设备
* 作为通知推送到 Azure 应用
* 作为语音消息传递

> [!IMPORTANT]
> 通常，服务器组的成功计划性维护事件间隔至少为 30 天。
>
> 但如果发生关键紧急更新（如严重漏洞），通知时间范围可能会短于五天。 即使在过去 30 天内成功地执行了计划性维护，关键更新也可能会应用于服务器。

如果维护失败或被取消，系统将创建通知。
系统将根据当前的计划设置再次尝试进行维护，并在下一次维护事件发生前提前 5 天通知你。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 Azure 服务运行状况[获取有关即将进行的维护的通知](../service-health/service-notifications.md)
* 了解如何[设置有关即将发生的计划性维护事件的警报](../service-health/resource-health-alert-monitor-guide.md)
