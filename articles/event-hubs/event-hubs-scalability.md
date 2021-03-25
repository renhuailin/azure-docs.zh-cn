---
title: 可伸缩性 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何使用分区和吞吐量单位来缩放 Azure 事件中心。
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601266"
---
# <a name="scaling-with-event-hubs"></a>通过事件中心进行缩放

有两个因素会影响通过事件中心进行的缩放。
*   吞吐量单位
*   分区

## <a name="throughput-units"></a>吞吐量单位

事件中心的吞吐量容量由吞吐量单位  控制。 吞吐量单位是预先购买的容量单位。 单个吞吐量是指：

* 入口：最高每秒 1 MB 或每秒 1000 个事件（以先达到的限制为准）。
* 出口：最高每秒 2 MB，或每秒 4096 个事件。

超出所购吞吐量单位的容量时，入口受限，返回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 出口不会出现限制异常，但仍受限于所购买吞吐量单位的容量。 如果收到发布速率异常或者预期看到更高的出口，请务必检查为命名空间购买的吞吐量单位数量。 可以在 [Azure 门户](https://portal.azure.com)的命名空间的“规模”边栏选项卡上管理吞吐量单位。 也可使用[事件中心 API](./event-hubs-samples.md) 以编程方式管理吞吐量单位。

吞吐量单位按小时计费，需提前购买。 购买后，吞吐量单位的最短计费时限为一小时。 最多可以为一个事件中心命名空间购买 20 个吞吐量单位，这些单位跨此命名空间内的所有事件中心进行共享。

事件中心的自动膨胀功能通过增加吞吐量单位数进行自动纵向扩展，以便满足使用量需求  。 增加吞吐量单位数可防止出现限制情况，在这些情况下：

- 数据入口速率超过设置的吞吐量单位数。
- 数据出口请求速率超过设置的吞吐量单位数。

当负载的增加超过最小阈值时，事件中心服务会增加吞吐量，不会因服务器繁忙错误导致任何请求失败。 

有关自动扩充功能的详细信息，请参阅[自动缩放吞吐量单位](event-hubs-auto-inflate.md)。

## <a name="partitions"></a>分区
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

- [自动缩放吞吐量单位](event-hubs-auto-inflate.md)
- [事件中心服务概述](./event-hubs-about.md)
