---
title: 规划 Gen1 环境 - Azure 时序见解 | Microsoft Docs
description: 准备、配置和部署 Azure 时序见解 Gen1 环境的最佳做法。
services: time-series-insights
ms.service: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e93c415e464b344a24593428c267fb09e6566e08
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136825"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>规划 Azure 时序见解 Gen1 环境

> [!CAUTION]
> 这是一篇 Gen1 文章。

本文介绍如何根据预期入口速率和数据保留要求规划 Azure 时序见解 Gen1 环境。

## <a name="video"></a>视频

**观看此视频，了解有关 Azure 时序见解中的数据保留的详细信息，以及如何为其计划**：<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>最佳实践

若要开始使用 Azure 时序见解，最好是知道每分钟想要推送的数据量以及需要存储数据的时间。

有关 Azure 时序见解 SKU 的容量和保留期的详细信息，请阅读 [Azure 时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

若要最合理地规划 Azure 时序见解环境以取得长期成功，请考虑以下属性：

- [存储容量](#storage-capacity)
- [数据保留期](#data-retention)
- [流入容量](#ingress-capacity)
- [调整事件](#shape-your-events)
- [确保已准备好参考数据](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>存储容量

默认情况下，Azure 时序见解根据预配的存储量（单位数乘以每个单位的存储量）和流入量来保留数据。

## <a name="data-retention"></a>数据保留

可以更改 Azure 时序见解环境中的“数据保留时间”设置。  可以启用最长 400 天的保留期。

Azure 时序见解具有两种模式：

- 一种模式针对最新数据进行了优化。 它强制执行 **清除旧数据** 的策略，使实例可以使用最新数据。 此模式默认已启用。
- 其他模式将优化数据，使其保持低于配置的保留限制。 “暂停流入”  可防止新数据在被选为“超出存储限制时的行为”  时流入。

可在 Azure 门户的环境配置页中调整保留期并在这两种模式之间切换。

> [!IMPORTANT]
> 可在 Azure 时序见解 Gen1 环境中配置最长 400 天的数据保留。

### <a name="configure-data-retention"></a>配置数据保留

1. 在 [Azure 门户](https://portal.azure.com)中，选择时序见解环境。

1. 在“时序见解环境”窗格中的“设置”下，选择“存储配置”    。

1. 在“数据保留时间(天)”框中，输入 1 到 400 的值  。

   [![配置保留期](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 若要详细了解如何实施适当的数据保留策略，请阅读[如何配置保留期](./time-series-insights-how-to-configure-retention.md)。

## <a name="ingress-capacity"></a>入口容量

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>环境规划

在规划 Azure 时序见解环境时，需要重点考虑的第二个方面是流入容量。 每日入口存储和事件容量每分钟度量一次，以 1 KB 块为单位。 允许的最大数据包大小为 32 KB。 大于 32 KB 的数据包将被截断。

可以在单一环境中，将 S1 或 S2 SKU 的容量增加到 10 个单位。 无法从 S1 环境迁移到 S2 环境。 无法从 S2 环境迁移到 S1 环境。

对于流入容量，首先应该确定每月所需的流入总量。 接下来，确定每分钟的需求。

限制和延迟对每分钟容量的影响很大。 如果数据流入的高峰期持续时间少于 24 小时，则 Azure 时序见解可以两倍于上表中所列的速度“赶上”流入速率。

例如，如果你使用单个 S1 SKU，流入数据的速率为每分钟 720 个事件，数据流入高峰的速率为 1,440 个事件（或更少）且持续时间不到 1 小时，则环境中不会出现明显的延迟。 但是，如果速率大于每分钟 1,440 个事件且超过 1 小时，则可能会发生数据延迟，这可在环境中查看并查询。

你可能无法提前知道想要推送多少数据。 在这种情况下，可以在 Azure 门户订阅的 [Azure IoT 中心](../iot-hub/monitor-iot-hub.md)和 [Azure 事件中心](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics)查找遥测数据。 这些遥测数据有助于确定如何预配环境。 在 Azure 门户使用相应事件源的“指标”页查看遥测数据  。 如果你了解数据源指标，便可以更有效地规划和预配 Azure 时序见解环境。

### <a name="calculate-ingress-requirements"></a>计算入口需求

若要计算流入要求：

- 确认流入容量高于平均每分钟的速率，并且环境足够大，能够在 1 小时内处理相当于两倍容量的预期流入量。

- 如果发生持续超过 1 小时的流入高峰，请使用高峰速率作为平均值。 使用可以应对高峰速率的容量预配环境。

### <a name="mitigate-throttling-and-latency"></a>缓解限制和延迟

有关如何避免限制和延迟的信息，请阅读[缓解延迟和限制](time-series-insights-environment-mitigate-latency.md)。

## <a name="shape-your-events"></a>塑造事件

必须确保向 Azure 时序见解发送事件的方式支持预配的环境大小。 （相反，可将环境大小映射到 Azure 时序见解读取的事件数和每个事件的大小。）另外，必须考虑到在查询数据时要用作切片和筛选依据的属性。

> [!TIP]
> 请查看[发送事件](time-series-insights-send-events.md)中的 JSON 塑形文档。

## <a name="ensure-that-you-have-reference-data"></a>确保已获得参考数据

参考数据集是对来自事件源的事件进行补充的项集合。  Azure 时序见解流入引擎将来自事件源的每个事件与参考数据集中的相应数据行联接到一起。 然后可以查询补充后的事件。 该联接基于参考数据集中定义的“主键”列。 

> [!NOTE]
> 参考数据不以追溯方式进行联接。 在配置并上传参考数据集后，只会将当前和将来的流入数据与参考数据集相匹配并联接到其中。 如果你打算将大量的历史数据发送到 Azure 时序见解，但未事先在 Azure 时序见解中上传或创建参考数据，到时可能需要从头开始，这是一件很麻烦的事。

若要详细了解如何在 Azure 时序见解中创建、上传和管理参考数据，请阅读[参考数据集文档](time-series-insights-add-reference-data-set.md)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>后续步骤

- 开始[在 Azure 门户中创建新的 Azure 时序见解环境](time-series-insights-get-started.md)。

- 了解如何[向 Azure 时序见解添加事件中心事件源](./how-to-ingest-data-event-hub.md)。

- 了解如何[配置 IoT 中心事件源](./how-to-ingest-data-iot-hub.md)。
