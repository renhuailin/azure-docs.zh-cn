---
title: 维护时段
description: 了解如何配置 Azure SQL 数据库和托管实例维护时段。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.custom: references_regions
ms.date: 07/22/2021
ms.openlocfilehash: 9f058cfc97821dc9ddcbedeeed1acf9ebb9919d3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751311"
---
# <a name="maintenance-window-preview"></a>维护时段（预览版）
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

使用维护时段功能，可以为 [Azure SQL 数据库](sql-database-paas-overview.md)和 [Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)资源配置维护计划，从而使重大维护事件可预测，并减少工作负载的中断次数。 

> [!Note]
> 维护时段功能仅保护免受升级或计划性维护产生的计划影响。 它不能保护免受所有故障转移原因影响；可能在维护时段之外导致短暂连接中断的异常包括硬件故障、群集负载均衡和数据库重新配置，这些异常是由于数据库服务级别目标变化等事件引起的。 

## <a name="overview"></a>概述

Azure 会定期执行 SQL 数据库和 SQL 托管实例资源的[计划内维护](planned-maintenance.md)。 在 Azure SQL 维护事件期间，数据库完全可用，但可能在 [SQL 数据库](https://azure.microsoft.com/support/legal/sla/azure-sql-database)和 [SQL 托管实例](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)的各自可用性 SLA 中发生短暂的重新配置。

维护时段专用于不能复原到数据库或实例重新配置的生产工作负载，并且无法缓解由计划内维护事件引起的短暂连接中断。 选择你喜欢的维护时段，可最大程度地减少计划内维护的影响，因为它将在你的高峰营业时间之外发生。 复原工作负载和非生产工作负载可能依赖于 Azure SQL 的默认维护策略。

可在创建时或为现有 Azure SQL 资源配置维护时段。 可以使用 Azure 门户、PowerShell、CLI 或 Azure API 对其进行配置。

> [!Important]
> 配置维护时段是一种长期运行的异步操作，类似于更改 Azure SQL 资源的服务层级。 该资源在操作过程中可用，只在操作结束时会发生短暂的重新配置，即使在长期运行的事务中断的情况下，通常最多也仅持续 8 秒。 若要将重新配置的影响降至最低，应在高峰时段之外执行操作。

### <a name="gain-more-predictability-with-maintenance-window"></a>通过维护时段提高可预测性

默认情况下，Azure SQL 维护策略会在当地时间每天早上 8 点到下午 5 点段期间阻止有影响的更新，以避免在正常高峰营业时间内发生任何中断。 当地时间由承载资源的 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/) 的位置确定，并根据当地时区定义遵守夏令时。 

从两个额外的维护时段槽中进行选择，可以将维护更新进一步调整为适用于 Azure SQL 资源的时间：
 
* 工作日时段，本地时间晚上 10 点到早上 6 点，星期一到星期四
* 周末时段，本地时间晚上 10 点到早上 6 点，星期五到星期日

选择维护时段并且服务配置完成后，计划内维护将仅在所选的时段中进行。 维护事件通常是在单个时段内完成，但其中某些事件可能会跨越两个或更多相邻时段。   

> [!Important]
> 在极少数情况下，任何延迟操作都可能会导致严重影响（例如应用关键安全修补程序），可能会暂时覆盖已配置的维护时段。 

### <a name="cost-and-eligibility"></a>成本和资格

配置和使用维护时段免费适用于所有合格 [套餐类型](https://azure.microsoft.com/support/legal/offer-details/)：即用即付、云解决方案提供商 (CSP)、Microsoft 企业协议或 Microsoft 客户协议。

> [!Note]
> Azure 套餐是用户拥有的 Azure 订阅类型。 例如，具有[即用即付率](https://azure.microsoft.com/offers/ms-azr-0003p/)的订阅、[Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p/)和 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) 都是 Azure 套餐。 每个套餐和计划都有不同的条款和权益。 你的套餐或计划显示在订阅的“概述”上。 有关将订阅切换到其他套餐的详细信息，请参阅[将 Azure 订阅更改为其他套餐](../../cost-management-billing/manage/switch-azure-offer.md)。

## <a name="advance-notifications"></a>提前通知

维护通知可配置为向你发出警报，提前 24 小时提醒你即将发生 Azure SQL Database 的计划内维护事件、维护时以及维护完成时提醒。 有关详细信息，请参阅[提前通知](advance-notifications.md)。

## <a name="availability"></a>可用性

### <a name="supported-service-level-objectives"></a>受支持的服务级别目标

除以下情况外，所有的 SLO 上都可以选择默认维护时段之外的维护时段：
* 超大规模 
* 实例池
* 旧版 Gen4 vCore
* 基本、S0 和 S1 
* DC、Fsv2、M 系列

### <a name="azure-region-support"></a>Azure 区域支持

目前在以下区域可以选择默认维护时段以外的维护时段：

- 澳大利亚东部
- 澳大利亚东南部
- 巴西南部
- 加拿大中部
- 加拿大东部
- 印度中部
- 美国中部
- 美国东部
- 美国东部 2
- 东亚
- 法国南部
- 德国中西部
- Japan East
- 韩国中部*
- 美国中北部
- 北欧
- 美国中南部
- 东南亚
- 英国南部
- 英国西部
- 美国中西部
- 西欧
- 美国西部
- 美国西部 2

*仅适用于 Azure SQL 托管实例

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL 数据库的网关维护

若要从维护时段获得最大优势，请确保客户端应用程序使用的是重定向连接策略。 重定向是推荐的连接策略，其中客户端直接与托管数据库的节点建立连接，从而降低延迟并改进吞吐量。  

* 在 Azure SQL 数据库中，使用代理连接策略的任何连接都可能会受到所选维护时段和网关节点维护时段的影响。 但是，使用推荐的重定向连接策略的客户端连接不受网关节点维护重新配置的影响。 

* 在 Azure SQL 托管实例中，网关节点托管在[虚拟群集中](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)，并与托管实例具有相同的维护时段，但仍建议使用重定向连接策略，以最大程度地减少维护事件期间中断的次数。

有关 Azure SQL 数据库中客户端连接策略的详细信息，请参阅 [Azure SQL 数据库连接策略](../database/connectivity-architecture.md#connection-policy)。 

有关 Azure SQL 托管实例中客户端连接策略的详细信息，请参阅 [Azure SQL 托管实例连接类型](../../azure-sql/managed-instance/connection-types-overview.md)。

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL 托管实例的注意事项

Azure SQL 托管实例由一组服务组件构成，这些组件托管在一组专用的、在客户的虚拟网络子网中运行的独立虚拟机上。 这些虚拟机构成了[虚拟群集](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture)，可以托管多个托管实例。 一个子网实例上配置的维护时段可能会影响子网中的虚拟群集数量、虚拟群集中实例的分配以及虚拟群集管理操作。 这可能需要考虑几个影响。

### <a name="maintenance-window-configuration-is-long-running-operation"></a>维护时段配置是长期运行的操作 
虚拟群集中托管的所有实例共享维护时段。 默认情况下，所有托管实例都在托管在具有默认维护时段的虚拟群集中。 在托管实例创建期间或之后为其指定另一个维护时段，则意味着必须将其放置在具有相应维护时段的虚拟群集中。 如果子网中没有此类虚拟群集，则必须先创建一个新群集来容纳该实例。 在现有虚拟群集中容纳其他实例可能需要重设群集大小。 这两个操作都涉及到为托管实例配置维护时段的持续时间。
在托管实例上配置维护时段的预期持续时间可使用[实例管理操作的估计持续时间](../managed-instance/management-operations-overview.md#duration)进行计算。

> [!Important]
> 在维护操作结束时，会发生短暂的重新配置，即使在长期运行的事务中断的情况下，通常最多也仅持续 8 秒。 若要将重新配置的影响降至最低，应将操作安排在高峰时段之外。

### <a name="ip-address-space-requirements"></a>IP 地址空间要求
子网中的每个新虚拟群集都根据[虚拟群集 IP 地址分配](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size)来要求额外的 IP 地址。 更改现有托管实例的维护时段还需要[临时额外的 IP 容量](../managed-instance/vnet-subnet-determine-size.md#update-scenarios)，就像为相应的服务层级缩放 vCore 方案一样。

### <a name="ip-address-change"></a>IP 地址更改
配置和更改维护时段会导致更改实例的 IP 地址（在子网的 IP 地址范围内）。

> [!Important]
>  请确保 IP 地址更改后 NSG 和防火墙规则不会阻止数据流量。 

### <a name="serialization-of-virtual-cluster-management-operations"></a>虚拟群集管理操作序列化
服务升级和虚拟群集大小调整（添加新计算节点或删除不需要的计算节点）等影响虚拟群集的操作均已序列化。 换句话说，在前一个虚拟群集管理操作完成之前，无法开始新虚拟群集管理操作。 如果正在执行的服务升级或维护操作尚未完成维护时段就结束了，则在此期间提交的其他所有虚拟群集管理操作都将搁置，直至下一个维护时段开放并完成服务升级或维护操作。 虚拟群集维护操作时间超出单个时段的情况并不常见，但如果维护操作很复杂，就可能会发生这种情况。
虚拟群集管理操作序列化是常规行为，也适用于默认维护策略。 使用配置的维护时段计划时，两个相邻时段之间的时间段可能是几天。 如果维护操作跨越两个时段，则提交的操作也会搁置几天。 这种情况非常罕见，但在此期间可能会阻止创建新实例或调整现有实例的大小（如果需要更多计算节点）。

## <a name="next-steps"></a>后续步骤

* [提前通知](advance-notifications.md)
* [配置维护时段](maintenance-window-configure.md)

## <a name="learn-more"></a>了解更多信息

* [维护时段常见问题解答](maintenance-window-faq.yml)
* [Azure SQL 数据库](sql-database-paas-overview.md) 
* [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)
* [在 Azure SQL 数据库和 Azure SQL 托管实例中计划 Azure 维护事件](planned-maintenance.md)
