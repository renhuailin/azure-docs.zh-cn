---
title: include 文件
description: include 文件
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: f2f5ef1ff77ccbae4170b84c325f97f2cb7dc390
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122264833"
---
下面概述了 Azure 时序见解 Gen1 中的主要限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 入口速率和容量

S1 和 S2 SKU 入口速率和容量可在配置新的 Azure 时序见解环境时提供灵活性。 SKU 容量基于所存储的事件数或字节数（以先达到容量上限者为准）指示每日入口速率。 请注意，流入量按分钟  进行度量，并使用令牌桶算法应用限制  。 流入量以 1-KB 块为单位进行度量。 例如，0.8 KB 的实际事件会度量为一个事件，2.6 KB 事件会度量为三个事件。

| S1 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 1 | 每天 1 GB（1 百万个事件） | 30 GB（3 千万个事件） |
| 10 | 每天 10 GB（1 千万个事件） | 300 GB（3 亿个事件） |

| S2 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 1 | 每天 10 GB（1 千万个事件） | 300 GB（3 亿个事件） |
| 10 | 每天 100 GB（1 亿个事件） | 3 TB（30 亿个事件） |

> [!NOTE]
> 容量呈线性增长，因此容量为 2 的 S1 SKU 每日入口速率支持 2 GB（2 百万）的事件，每月支持 60 GB（6 千万）的事件。

S2 SKU 环境每月支持更多的事件，并具有显著更高的入口容量。

| SKU  | 每月事件计数  | 每分钟事件计数 | 每分钟事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3000 万   |  720    |  720 KB   |
 |S2     |   3 亿   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>属性限制

Gen1 属性限制取决于所选的 SKU 环境。 提供的事件属性具有相应的 JSON、CSV 和图表列，可以在 [Azure 时序见解资源管理器](../articles/time-series-insights/time-series-quickstart.md)中查看。

| SKU | 最大属性数 |
| --- | --- |
| S1 | 600 属性（列） |
| S2 | 800 属性（列） |

### <a name="event-sources"></a>事件源

每个实例最多支持两个事件源。

* 了解如何[添加事件中心源](../articles/time-series-insights/how-to-ingest-data-event-hub.md)。
* 配置 [IoT 中心源](../articles/time-series-insights/how-to-ingest-data-iot-hub.md)。

### <a name="api-limits"></a>API 限制

[REST API 参考文档](/rest/api/time-series-insights/gen1-reference-data-api#current-limits)中指定了 Azure 时序见解 Gen1 的 REST API 限制。
