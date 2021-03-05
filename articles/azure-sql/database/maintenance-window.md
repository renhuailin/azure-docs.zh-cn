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
ms.date: 03/04/2021
ms.openlocfilehash: cf3404f364a7beee67cfa7dc523b9fd4b7b9985a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201305"
---
# <a name="maintenance-window-preview"></a>维护时段 (预览) 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

维护时段功能允许你为 [AZURE Sql 数据库](sql-database-paas-overview.md) 和 [azure sql 托管实例](../managed-instance/sql-managed-instance-paas-overview.md) 资源配置维护计划，使有影响力维护事件可预测且更少中断工作负荷。 

> [!Note]
> 维护时段功能不能防止意外事件（如硬件故障），这可能会导致短暂的连接中断。

## <a name="overview"></a>概述

Azure 会定期执行 SQL 数据库和 SQL 托管实例资源的 [计划内维护](planned-maintenance.md) 。 在 Azure SQL 维护事件期间，数据库完全可用，但在 [Sql 数据库](https://azure.microsoft.com/support/legal/sla/sql-database) 和 [sql 托管实例](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)的相应可用性 sla 中可能会发生短故障转移，因为在某些情况下需要重新配置资源。

维护时段适用于不能复原数据库或实例故障转移的生产工作负荷，并且无法吸收由计划内维护事件引起的短暂连接中断。 通过选择首选维护时段，你可以最大程度地减少计划内维护的影响，因为它将在你的高峰工作时间之外发生。 复原工作负荷和非生产工作负荷可能依赖于 Azure SQL 的默认维护策略。

可在创建或现有 Azure SQL 资源时配置维护时段。 可以使用 Azure 门户、PowerShell、CLI 或 Azure API 对其进行配置。

> [!Important]
> 配置维护时段是一种长时间运行的异步操作，类似于更改 Azure SQL 资源的服务层。 该资源在操作过程中可用，但在操作结束时会发生短暂的故障转移，并且即使在中断长时间运行的事务的情况下，也通常会持续到8秒。 若要将故障转移的影响降至最低，应在高峰时段执行操作。

### <a name="gain-more-predictability-with-maintenance-window"></a>通过维护时段提高可预测性

默认情况下，Azure SQL 维护策略会阻止在一段时间内早晨8点到下午5点的有影响力更新，以避免在正常高峰工作时间内发生任何中断。 本地时间由承载资源的 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/) 确定。 换句话说， _默认维护时段_ 允许在每日下午5到早晨8点之间维护。 通过从两个额外的维护时段槽中进行选择，可以将维护更新进一步调整为适用于 Azure SQL 资源的时间：
 
* 工作日窗口，10PM 到早晨本地时间星期一–星期四
* 周末窗口，10PM 到早晨本地时间星期五-星期日

做出维护时段选择并且服务配置完成后，计划内维护将仅在所选的窗口中进行。   

> [!Important]
> 在极少数情况下，任何延迟操作都可能会导致严重影响，例如，应用关键安全修补程序，可能会暂时覆盖已配置的维护时段。 

### <a name="cost-and-eligibility"></a>成本和资格

配置和使用维护时段免费适用于所有合格的 [产品/服务类型](https://azure.microsoft.com/support/legal/offer-details/)：即用即付、云解决方案提供商 (CSP) 、microsoft 企业协议或 Microsoft 客户协议。

> [!Note]
> Azure 套餐是用户拥有的 Azure 订阅类型。 例如，使用即用即 [付费率](https://azure.microsoft.com/offers/ms-azr-0003p/)、 [Azure 开放许可](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)和 [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) 的订阅均为 Azure 产品/服务。 每个产品/服务或计划都有不同的术语和权益。 你的产品/服务或计划显示在订阅概述上。 有关将订阅切换到其他产品/服务的详细信息，请参阅 [将 Azure 订阅更改为其他产品/服务](/azure/cost-management-billing/manage/switch-azure-offer)。

## <a name="advance-notifications"></a>提前通知

维护通知可配置为提前向你发出警报，提醒你提前计划 Azure SQL Database 的计划内维护事件（在维护时）以及维护完成时间。 有关详细信息，请参阅 [提前通知](advance-notifications.md)。

## <a name="availability"></a>可用性

### <a name="supported-service-level-objectives"></a>支持的服务级别目标

选择除默认值以外的维护时段在所有 Slo 上可用，但以下情况 **除外**：
* 超大规模 
* 实例池
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

* 在 Azure SQL 托管实例中，网关节点托管在 [虚拟群集中](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) ，并与托管实例具有相同的维护时段，但仍建议使用重定向连接策略，以最大程度地减少维护事件期间中断的次数。

有关 Azure SQL 数据库中的客户端连接策略的详细信息，请参阅 [AZURE Sql 数据库连接策略](../database/connectivity-architecture.md#connection-policy)。 

有关 Azure SQL 托管实例中的客户端连接策略的详细信息，请参阅 [AZURE sql 托管实例连接类型](../../azure-sql/managed-instance/connection-types-overview.md)。

## <a name="considering-specifics-of-azure-sql-managed-instance"></a>了解 Azure SQL 托管实例的具体信息

Azure SQL 托管实例包含在客户的虚拟网络子网中运行的一组专用虚拟机上托管的服务组件。 这些虚拟机构成了 [虚拟群集 (s) ](https://docs.microsoft.com/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture) ，可以托管多个托管实例。 在一个子网的实例上配置的维护时段可能会影响子网中的虚拟群集数量以及虚拟群集中实例的分配。 这可能需要考虑几个影响。

### <a name="maintenance-window-configuration-is-long-running-operation"></a>维护时段配置是长时间运行的操作 
虚拟群集中托管的所有实例共享维护时段。 默认情况下，所有托管实例都在具有默认维护时段的虚拟群集中托管。 在其创建期间为托管实例指定另一个维护时段，或在之后指定该时段，这意味着必须将其放置在具有相应维护时段的虚拟群集中。 如果子网中没有此类虚拟群集，则必须先创建一个新群集来容纳该实例。 在现有虚拟群集中容纳其他实例可能需要调整群集大小。 这两个操作都涉及到为托管实例配置维护时段的持续时间。
在托管实例上配置维护时段的预期持续时间可使用 [实例管理操作的估计持续时间](https://docs.microsoft.com/azure/azure-sql/managed-instance/management-operations-overview#duration)计算。

> [!Important]
> 在操作结束时，会发生短暂的故障转移，并且即使在中断长时间运行的事务时，也通常会持续多达8秒。 若要将故障转移的影响降至最低，应在高峰时段执行操作。

### <a name="ip-address-space-requirements"></a>IP 地址空间要求
子网中的每个新虚拟群集都需要根据 [虚拟群集 IP 地址分配](https://docs.microsoft.com/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size)来额外的 ip 地址。 更改现有托管实例的维护时段还需要 [临时额外的 IP 容量](https://docs.microsoft.com/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios) ，如缩放 vcore 方案（适用于相应的服务层）。

### <a name="ip-address-change"></a>IP 地址更改
配置和更改维护时段会导致更改实例的 IP 地址，在子网的 IP 地址范围内。

> [!Important]
>  请确保在 IP 地址更改后 NSG 和防火墙规则不会阻止数据流量。 

## <a name="next-steps"></a>后续步骤

* [提前通知](advance-notifications.md)
* [配置维护时段](maintenance-window-configure.md)

## <a name="learn-more"></a>了解详细信息

* [维护时段常见问题解答](maintenance-window-faq.yml)
* [Azure SQL 数据库](sql-database-paas-overview.md) 
* [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)
* [规划在 Azure SQL 数据库和 Azure SQL 托管实例中的 Azure 维护事件](planned-maintenance.md)




