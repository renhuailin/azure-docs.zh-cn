---
title: 专用事件中心概述 - Azure 事件中心 | Microsoft Docs
description: 本文概述专用 Azure 事件中心，它提供事件中心的单租户部署。
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1a15206fc35f0d536c7105aa73dfdcfc9967124d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358858"
---
# <a name="overview-of-event-hubs-dedicated"></a>专用事件中心概述

事件中心群集提供单租户部署来满足客户的苛刻流式处理要求。 此单租户套餐提供有保障的 99.99% SLA，只能在专用定价层上使用。 事件中心群集每秒能够引入数百万个事件，且提供有保障的容量和亚秒级的延迟。 在专用群集中创建的命名空间和事件中心不仅包括标准产品/服务的所有功能，而且不存在任何引入限制。 它还包括常见的 [事件中心捕获](event-hubs-capture-overview.md) 功能，无需额外付费。 此功能可让你自动对数据流进行批处理，并将数据流记录到 Azure 存储或 Azure Data Lake。 

群集是按容量单位 (CU) 预配和计费的，并预先分配了 CPU 数量和内存资源量。 可为每个群集购买 1、2、4、8、12、16 或 20 个 CU。 每个 CU 可以引入和流式传输的量取决于多种因素，例如： 

- 制造者和使用者数量
- 有效负载形状
- 出口速率

> [!NOTE]
> 默认情况下，所有事件中心群集都支持 Kafka，并支持可由现有基于 Kafka 的应用程序使用的 Kafka 终结点。 在群集上启用 Kafka 不影响非 Kafka 用例；没有对应的选项，或者不需要在群集上禁用 Kafka。

## <a name="why-dedicated"></a>为何采用专用形式？

专用事件中心为需要企业级容量的客户提供了三个引人注目的优点：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>单租户可以保证容量，以提供更好的性能

专用群集可保证容量达到最大限制。 它可以通过完全持久的存储和亚秒级延迟传输多达千兆字节的流式数据，以适应流量的突发。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>对功能的专有访问权限和非专有访问权限 
专用产品/服务包括捕获无需额外付费的功能，以及对即将推出的功能（如创建自己的密钥 (BYOK) ）的独占访问权限。 该服务还管理负载均衡、操作系统更新、安全修补程序和分区。 因此，您可以花费更少的时间来维护基础结构，以及生成客户端功能的时间更长。  

#### <a name="cost-savings"></a>节约成本
在高入口卷上 ( # B0 100 吞吐量单位) ，与在标准产品中购买可比较数量的吞吐量单位相比，每小时的群集成本要大大减少。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中心专用层配额和限制

事件中心专用层产品/服务按固定的每月价格计费，最少用量为 4 小时。 专用层提供标准计划的所有功能，但具有企业规模的容量和对具有要求的工作负荷的客户的限制。 

| 功能 | 标准 | 专用 |
| --- |:---|:---|
| 带宽 | 20 TU（最多 40 TU） | 20 CU |
| 命名空间 |  1 | 每个 CU 50 |
| 事件中心 |  每个命名空间 10 | 每个命名空间 1000 |
| 入口事件 | 按每百万个事件支付 | 已含 |
| 消息大小 | 1000000 字节 | 1000000 字节 |
| 分区 | 每个事件中心 32 | 每个事件中心1024<br/>每个 CU 2000 个 |
| 使用者组 | 每个事件中心 20 | 每个 CU 无限制，每个事件中心 1000 |
| 中转连接 | 包括 1,000 个，最大 5,000 个 | 包括 100000 个，最大 100000 个 |
| 消息保留期 | 7 天，每个 TU 包含 84 GB | 90 天，每个 CU 包含 10 TB |
| 捕获 | 按每小时支付 | 已含 |

有关配额和限制的详细信息，请参阅 [事件中心配额和限制](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>如何加入

通过[Azure 门户](https://aka.ms/eventhubsclusterquickstart)[创建事件中心群集](event-hubs-dedicated-cluster-create-portal.md)的自助服务体验现在为预览版。 如果你有任何疑问，或者需要有关加入事件中心专用层的帮助，请联系 [事件中心团队](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常见问题解答

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>后续步骤

请联系你的 Microsoft 销售代表或 Microsoft 支持部门获取有关事件中心专用层的更多详细信息。 还可以通过访问以下链接来创建群集或了解有关事件中心定价层的详细信息：

- [通过 Azure 门户创建事件中心群集](https://aka.ms/eventhubsclusterquickstart) 
- [专用事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 还可以联系 Microsoft 销售代表或 Microsoft 支持部门，获取关于专用事件中心容量的其他详细信息。
- [事件中心常见问题解答](event-hubs-faq.md)中包含了定价信息并解答了一些有关事件中心的常见问题。
