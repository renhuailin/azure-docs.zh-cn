---
title: 规划 Azure 维护事件
description: 了解如何为在 Azure SQL 数据库和 Azure SQL 托管实例中执行计划内维护事件做准备。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: mathoma
ms.date: 3/23/2021
ms.openlocfilehash: a697c0a3095963760d6a95159790c02cbab14d71
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121618"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>规划在 Azure SQL 数据库和 Azure SQL 托管实例中的 Azure 维护事件
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

了解如何为在 Azure SQL 数据库和 Azure SQL 托管实例中的数据库上执行计划内维护事件做准备。

## <a name="what-is-a-planned-maintenance-event"></a>什么是计划内维护事件？

为了保持 Azure SQL 数据库和 Azure SQL 托管实例服务的安全、合规性、稳定性和高性能，将持续通过服务组件进行更新。 得益于新式可靠的服务体系结构和创新技术（如[热修补](https://aka.ms/azuresqlhotpatching)），大多数更新在服务可用性方面都是完全透明且不会产生不良影响的。 尽管如此，少数类型的更新会导致短暂的服务中断并需要特殊处理。 

在计划内维护期间，所维护的数据库副本将进入脱机状态，一次脱机一个，目的是要有一个能够响应的主要副本。 对于业务关键型和高级数据库，还应至少有一个次要副本处于联机状态，确保不发生客户端停机。 当主要副本需要进入脱机状态时，将启动重新配置进程。 对于业务关键型和高级数据库，其中会有一个辅助副本变为新的主要副本。 对于常规用途、标准和基本数据库，主要副本将迁移到具有足够可用容量的另一个无状态计算节点。

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>计划内维护事件期间会发生什么

维护事件可能产生单个或多个重新配置，具体取决于维护事件开始时主要副本和次要副本的集合。 平均而言，每个计划内维护事件会出现 1.7 个重新配置。 重新配置通常在 30 秒内完成。 平均 8 秒。 如果应用程序处于已连接状态，则必须重新连接至新的数据库主要副本。 如果在进行连接时数据库正在进行重新配置，且新的主副本尚未处于联机状态，会显示错误 40613（数据库不可用）： *“服务器 '{servername}' 上的数据库 '{databasename}' 当前不可用。请稍后重试连接”错误。* 如果数据库有一个长时间运行的查询，重新配置期间此查询会中断，需要重新启动。

## <a name="how-to-simulate-a-planned-maintenance-event"></a>如何模拟计划内维护事件

在部署到生产环境之前，确保客户端应用程序对于维护事件是可复原的，这有助于降低应用程序故障的风险，并可帮助提升最终用户的应用程序可用性。可在计划内维护事件期间通过 PowerShell、CLI 或 REST API [测试应用程序的故障复原能力](./high-availability-sla.md#testing-application-fault-resiliency)，来测试客户端应用程序的行为。 另请参阅为托管实例[启动手动故障转移](https://aka.ms/mifailover-techblog)。 这将生成与使主要副本脱机的维护事件相同的行为。

## <a name="retry-logic"></a>重试逻辑

连接到云数据库服务的任何客户端生产应用程序均应实现一个可靠的连接[重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。 这将有助于使重新配置对最终用户透明，或至少最大程度地减少负面影响。

### <a name="service-health-alert"></a>服务运行状况警报

如果你希望在出现服务问题或计划内维护活动时收到警报，可将 Azure 门户中的服务运行状况警报与相应的事件类型和操作组配合使用。 有关详细信息，请参阅[接收有关 Azure 服务通知的警报](../../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal)。

## <a name="resource-health"></a>资源运行状况

如果数据库发生登录失败的情况，请在 [Azure 门户](https://portal.azure.com)的[资源运行状况](../../service-health/resource-health-overview.md#get-started)窗口中查看当前状态。 运行状况历史记录部分包含每个事件（如果有）的停机原因。

## <a name="maintenance-window-feature"></a>维护时段功能

维护时段功能允许配置适用于符合条件的 Azure SQL 数据库和 SQL 托管实例的可预测维护时段计划。 有关详细信息，请参阅[维护时段](maintenance-window.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure SQL 数据库和 Azure SQL 托管实例的[资源运行状况](resource-health-to-troubleshoot-connectivity.md)。
- 有关重试逻辑的详细信息，请参阅[针对暂时性错误的重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。
- 使用[维护时段](maintenance-window.md)功能配置维护时段计划。