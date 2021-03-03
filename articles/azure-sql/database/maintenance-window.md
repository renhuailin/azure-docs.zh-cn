---
title: 维护时段
description: 了解如何配置 Azure SQL 数据库和托管实例维护时段。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 4006cedf5f24ab2fc08e41b58f8acf90c404f668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679124"
---
# <a name="maintenance-window-preview"></a>维护时段 (预览) 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

维护时段功能允许配置适用于 [AZURE Sql 数据库](sql-database-paas-overview.md) 和 [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)的可预测维护时段计划。 

有关维护事件的详细信息，请参阅 [在 AZURE Sql 数据库和 AZURE sql 中规划 azure 维护事件托管实例](planned-maintenance.md)。

## <a name="overview"></a>概述

Azure 定期在 Azure SQL 数据库和 SQL 托管实例资源上执行计划内维护更新，这通常包括对底层硬件（包括基础操作系统 (OS) 和 SQL 引擎）的更新。 在维护更新过程中，资源完全可用且可访问，但某些维护更新需要进行故障转移，因为 Azure 会在平均) 的持续时间内，将实例脱机 (8 秒。  计划内维护更新平均每35天发生一次，这意味着客户预计每个 Azure SQL 数据库或 SQL 托管实例每月大约进行一个计划内维护事件，并且仅在客户选择的维护时段插槽期间执行。   

维护时段适用于对默认时段内的计划内维护事件可能导致的潜在连接中断敏感的业务工作负荷。  

可以使用 Azure 门户、PowerShell、CLI 或 Azure API 配置维护时段。 它可在创建时配置，也可用于现有 SQL 数据库和 SQL 托管实例。

### <a name="gain-more-predictability-with-maintenance-window"></a>通过维护时段提高可预测性

默认情况下，所有 Azure SQL 数据库和托管实例数据库仅在下午5：早晨8点本地时间更新，以避免高峰工作时间中断。 本地时间由承载资源的 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/) 确定。 您可以通过从两个额外的维护时段槽中进行选择，将维护更新进一步调整到适合您的数据库的时间：

* **默认** 窗口，从下午到早晨8点的本地时间星期一-星期日 
* 工作日窗口，10PM 到早晨 local time 星期一–星期四： **要求客户选择加入** 
* 周末窗口，10PM 到早晨本地时间星期五-星期日： **要求客户选择加入**  

做出维护窗口选择后，所有计划内的维护更新将仅在您选择的窗口中发生。   

> [!Note]
> 除了计划内维护更新外，在极少数情况下，计划外维护事件可能导致不可用。 

### <a name="cost"></a>成本

对于以下订阅 [产品/服务类型](https://azure.microsoft.com/support/legal/offer-details/)，选择维护时段是免费的：即用即付、云解决方案提供商 (CSP) 、microsoft Enterprise 或 Microsoft 客户协议。

> [!Note]
> Azure 套餐是用户拥有的 Azure 订阅类型。 例如，使用即用即 [付费率](https://azure.microsoft.com/offers/ms-azr-0003p/)、 [Azure 开放许可](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)和 [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) 的订阅均为 Azure 产品/服务。 每个产品/服务或计划都有不同的术语和权益。 你的产品/服务或计划显示在订阅概述上。 有关将订阅切换到其他产品/服务的详细信息，请参阅 [将 Azure 订阅更改为其他产品/服务](/azure/cost-management-billing/manage/switch-azure-offer)。

## <a name="advance-notifications"></a>提前通知

可以将维护通知配置为提前提醒客户即将发生的计划内维护事件（在维护时）和维护时段完成。 有关详细信息，请参阅 [提前通知](advance-notifications.md)。

## <a name="availability"></a>可用性

### <a name="supported-service-level-objectives"></a>支持的服务级别目标

选择除默认值以外的维护时段在所有 Slo 上可用，但以下情况 **除外**：
* 超大规模 
* 旧 Gen4 vCore
* 基本、S0 和 S1 
* DC、Fsv2、M 系列

### <a name="azure-region-support"></a>Azure 区域支持

选择除默认值以外的维护时段当前在以下区域中可用：

- 澳大利亚东部
- 澳大利亚东南部
- 巴西南部
- 加拿大中部
- 美国中部
- 美国东部
- 美国东部 2
- Japan East
- 美国中北部
- 北欧
- SouthCentral
- 东南亚
- 英国南部
- 西欧
- 美国西部
- 美国西部 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL Database 的网关维护

若要从维护时段获得最大优势，请确保客户端应用程序正在使用重定向连接策略。 重定向是推荐的连接策略，其中客户端直接建立与托管数据库的节点的连接，从而降低延迟并提高吞吐量。  

* 在 Azure SQL 数据库中，使用代理连接策略的任何连接都可能会受到所选维护时段和网关节点维护时段的影响。 但是，使用建议的重定向连接策略的客户端连接不受网关节点维护故障转移的影响。 

* 在 Azure SQL 托管实例中，网关节点位于 [虚拟群集内](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 并且与托管实例具有相同的维护时段，因此使用代理连接策略不可能会向其他维护时段公开连接。

有关 Azure SQL 数据库中的客户端连接策略的详细信息，请参阅 [AZURE Sql 数据库连接策略](../database/connectivity-architecture.md#connection-policy)。 

有关 Azure SQL 托管实例中的客户端连接策略的详细信息，请参阅 [AZURE sql 托管实例连接类型](../../azure-sql/managed-instance/connection-types-overview.md)。


## <a name="next-steps"></a>后续步骤

* [提前通知](advance-notifications.md)
* [配置维护时段](maintenance-window-configure.md)

## <a name="learn-more"></a>了解详细信息

* [维护时段常见问题解答](maintenance-window-faq.yml)
* [Azure SQL 数据库](sql-database-paas-overview.md) 
* [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)
* [规划在 Azure SQL 数据库和 Azure SQL 托管实例中的 Azure 维护事件](planned-maintenance.md)




