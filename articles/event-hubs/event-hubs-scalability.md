---
title: 可伸缩性 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何使用分区和吞吐量单位来缩放 Azure 事件中心。
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: ced3d366c87d05fc1d7e8370c6e7db793dfe2535
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583655"
---
# <a name="scaling-with-event-hubs"></a>通过事件中心进行缩放

有两个因素会影响通过事件中心进行的缩放。
* 吞吐量单位（标准层）或处理单位（高级层） 
* 分区

## <a name="throughput-units"></a>吞吐量单位

事件中心的吞吐量容量由吞吐量单位  控制。 吞吐量单位是预先购买的容量单位。 单个吞吐量是指：

* 入口：最高每秒 1 MB 或每秒 1000 个事件（以先达到的限制为准）。
* 出口：最高每秒 2 MB，或每秒 4096 个事件。

超出所购吞吐量单位的容量时，入口受限，返回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 出口不会出现限制异常，但仍受限于所购买吞吐量单位的容量。 如果收到发布速率异常或者预期看到更高的出口，请务必检查为命名空间购买的吞吐量单位数量。 可以在 [Azure 门户](https://portal.azure.com)的命名空间的“规模”边栏选项卡上管理吞吐量单位。 也可使用[事件中心 API](./event-hubs-samples.md) 以编程方式管理吞吐量单位。

吞吐量单位按小时计费，需提前购买。 购买后，吞吐量单位的最短计费时限为一小时。 最多可以为一个事件中心命名空间购买 40 个吞吐量单位，这些单位跨此命名空间内的所有事件中心进行共享。

事件中心的自动膨胀功能通过增加吞吐量单位数进行自动纵向扩展，以便满足使用量需求  。 增加吞吐量单位数可防止出现限制情况，在这些情况下：

- 数据入口速率超过设置的吞吐量单位数。
- 数据出口请求速率超过设置的吞吐量单位数。

当负载的增加超过最小阈值时，事件中心服务会增加吞吐量，不会因服务器繁忙错误导致任何请求失败。 

有关自动扩充功能的详细信息，请参阅[自动缩放吞吐量单位](event-hubs-auto-inflate.md)。

## <a name="processing-units"></a>处理单位

 [事件中心高级层](./event-hubs-premium-overview.md)在托管的多租户 PaaS 环境中提供了卓越的性能和更好的隔离性。 高级层中的资源在 CPU 和内存级别隔离，因此每个租户工作负荷都独立运行。 此资源容器称为“处理单位”(PU)。 可以为每个事件中心高级层命名空间购买 1、2、4、8 或 16 个处理单位。 

使用处理单位可以引入和流式传输的数据量取决于各种因素，例如生成者、使用者、引入和处理速率，等等。 一个处理单位可以大致提供约 5-10 MB/秒流入量和 10-20 MB/秒流出量的核心容量，由于我们有足够的分区，因此存储不是限制因素。  

若要了解如何为高级层命名空间配置 PU，请参阅[配置处理单位](configure-processing-units-premium-namespace.md)。

> [!NOTE]
> 若要详细了解配额和限制，请参阅 [Azure 事件中心 - 配额和限制](event-hubs-quotas.md)。

## <a name="partitions"></a>分区
[!INCLUDE [event-hubs-partitions](./includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

- [自动缩放标准层命名空间的吞吐量单位](event-hubs-auto-inflate.md)
- [配置高级层命名空间的处理单位](configure-processing-units-premium-namespace.md)
