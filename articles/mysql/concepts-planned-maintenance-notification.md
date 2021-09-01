---
title: 计划内维护通知 - Azure Database for MySQL（单一服务器）
description: 本文介绍了 Azure Database for MySQL（单一服务器）中的计划内维护通知功能
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 5e40d255ed250b11542c0a2065ee46915798ac1b
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653573"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL（单一服务器）中的计划内维护通知

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解如何针对 Azure Database for MySQL 的计划内维护事件做准备。

## <a name="what-is-a-planned-maintenance"></a>什么是计划内维护？

Azure Database for MySQL 服务执行基础硬件、OS 和数据库引擎的自动修补。 补丁包括新的服务功能、安全性和软件更新。 对于 MySQL 引擎，次要版本的升级自动进行，并作为修补周期的一部分包含在内。 无需任何用户操作或配置设置即可进行修补。 此补丁经过广泛测试，并使用安全部署实践进行推广。

计划内维护是一个维护时段，这些服务更新会在该时段内部署到给定 Azure 区域中的服务器。 在计划内维护期间，会创建通知事件，通知客户何时在承载其服务器的 Azure 区域中部署服务更新。 两次计划内维护之间的最短持续时间为 30 天。 你会提前 72 小时收到下一个维护时段的通知。

## <a name="planned-maintenance---duration-and-customer-impact"></a>计划内维护 - 持续时间和客户影响

给定 Azure 区域的计划内维护通常会运行 15 小时。 此时段包括必要时执行回滚计划的缓冲时间。 在计划内维护期间，可能会发生数据库服务器重启或故障转移，这可能会导致最终用户的数据库服务器暂时不可用。 Azure Database for MySQL 服务器在容器中运行，因此数据库服务器重启通常很快，预计一般会在 60-120 秒内完成。 工程团队会认真监视包括每个服务器重启在内的整个计划内维护事件。 服务器故障转移时间取决于数据库恢复时间，如果在故障转移时服务器上有大量的事务活动，这可能会导致数据库需要更长的时间才能联机。 若要避免重启时间延长，建议在计划内维护事件期间避免任何长时间运行的事务（大容量加载）。

总之，虽然计划内维护事件运行 15 小时，但单个服务器的影响通常只持续 60 秒，具体取决于服务器上的事务活动。 一个通知会在计划内维护开始前的 72 个日历小时内发送，另一个通知将在给定区域正在维护时发送。

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>如何收到有关计划内维护的通知？

你可以利用计划内维护通知功能接收有关即将进行的计划内维护事件的警报。 你会在事件发生前的 72 个日历小时收到即将进行维护的通知，在给定区域正在维护时收到另一个通知。

### <a name="planned-maintenance-notification"></a>计划内维护通知

> [!IMPORTANT]
> 计划内维护通知目前在除美国中西部以外的所有区域中都以预览版形式提供

“计划内维护通知”允许你接收有关 Azure Database for MySQL 即将进行的计划内维护事件的警报。 这些通知与[服务运行状况](../service-health/overview.md)计划内维护集成，允许你在同一位置查看你的订阅的所有计划内维护。 它还有助于将通知扩展到不同资源组的适当受众，因为你可能有不同的联系人负责不同的资源。 你会在事件发生前的 72 个日历小时收到即将进行维护的通知。

我们将尽一切努力为所有事件提供 **计划内维护通知** 72 小时通知。 但是，对于关键或安全修补程序，通知可能会在事件快要发生时更晚一点发送，或者会被忽略。

你可以在 Azure 门户上检查计划内维护通知，也可以配置警报以接收通知。 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>从 Azure 门户检查计划内维护通知

1. 在 [Azure 门户](https://portal.azure.com)中，选择“服务运行状况”。
2. 选择“计划内维护”选项卡
3. 选择要为其检查计划内维护通知的订阅、区域和服务。   
   
### <a name="to-receive-planned-maintenance-notification"></a>接收计划内维护通知

1. 在[门户](https://portal.azure.com)中，选择“服务运行状况”。
2. 在“警报”部分中，选择“运行状况警报”。
3. 选择“+ 添加服务运行状况警报”，并填写字段。
4. 填写所需的字段。 
5. 选择“事件类型”，然后选择“计划内维护”或“全选”
6. 在“操作组”中，定义接收警报的方式（获取电子邮件、触发逻辑应用等）。  
7. 确保“创建后启用规则”设置为“是”。
8. 选择“创建警报规则”以完成警报

有关如何创建服务运行状况警报的详细步骤，请参阅 [创建有关服务通知的活动日志警报](../service-health/alerts-activity-log-service-notifications-portal.md)。

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>能否取消或推迟计划内维护？

维护是使服务器保持安全、稳定和最新状态所必需的。 计划内维护事件无法取消或推迟。 将通知发送到给定的 Azure 区域后，不能对该区域中的任何单个服务器进行修补计划更改。 补丁针对整个区域同时推出。 Azure Database for MySQL - 单服务器服务适用于不需要对服务进行精细控制或自定义的云原生应用程序。 如果希望能为服务器计划维护，则建议考虑使用[灵活服务器](./flexible-server/overview.md)。

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>是否所有 Azure 区域都同时修补？

否，所有 Azure 区域都按部署时段修补。 在给定的 Azure 区域中，部署时段通常是下午 5 点到第二天的上午 8 点（当地时间）。 地域配对的 Azure 区域会在不同的日期进行修补。 为了实现数据库服务器的高可用性和业务连续性，我们建议使用[跨区域只读副本](./concepts-read-replicas.md#cross-region-replication)。

## <a name="retry-logic"></a>重试逻辑

暂时性错误也称为暂时性故障，是一种可以自行解决的错误。 在维护期间可能会发生[暂时性错误](./concepts-connectivity.md#transient-errors)。 系统在 60 秒以内可自动解决其中的大部分事件。 应使用[重试逻辑](./concepts-connectivity.md#handling-transient-errors)来处理暂时性错误。


## <a name="next-steps"></a>后续步骤

- 如果有任何关于使用 Azure Database for MySQL 的问题或建议，请发送电子邮件至 Azure Database for MySQL 团队 (AskAzureDBforMySQL@service.microsoft.com)
- 有关如何基于指标创建警报的指南，请参阅[如何设置警报](howto-alert-on-metric.md)。
- [排查 Azure Database for MySQL（单一服务器）的连接问题](howto-troubleshoot-common-connection-issues.md)
- [处理暂时性错误并有效地连接到 Azure Database for MySQL（单一服务器）](concepts-connectivity.md)
