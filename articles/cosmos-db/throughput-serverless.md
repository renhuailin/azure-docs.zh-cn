---
title: 如何在 Azure Cosmos DB 上的预配吞吐量与无服务器之间进行选择
description: 了解如何针对你的工作负荷在预配吞吐量与无服务器之间进行选择。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 057004a7a322505b137c04b76c3407e4c36f6f89
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378191"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>如何在预配吞吐量与无服务器之间进行选择
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 提供了两种不同的容量模式：[预配吞吐量](set-throughput.md)和[无服务器](serverless.md)。 在这两种模式下，你可以执行完全相同的数据库操作，但这些操作的收费方式截然不同。 以下视频说明了这些模式之间的核心区别以及它们如何适应不同类型的工作负荷：

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>详细比较

| 条件 | 预配的吞吐量 | 无服务器 |
| --- | --- | --- |
| 最适用于 | 具有持续流量的工作负载，需要可预测的性能 | 具有间歇性或不可预测流量且平均峰值流量比低的工作负载 |
| 工作原理 | 对于每个容器，你预配一定数量的吞吐量（以每秒[请求单位数](request-units.md)表示）。 每秒此数量的请求单位可用于你的数据库操作。 预配的吞吐量可以手动更新，也可以通过[自动缩放](provision-throughput-autoscale.md)自动调整。 | 你对容器运行数据库操作，无需预配任何容量。 |
| 地理分布 | 可用（Azure 区域数不受限制） | 不可用（无服务器帐户只能在 1 个 Azure 区域中运行） |
| 每个容器的最大存储 | 无限制 | 50 GB |
| 性能 | SLA 涵盖的针对点读取和写入的延迟 < 10 毫秒 | SLA 涵盖的针对点读取的延迟 < 10 毫秒，针对写入的延迟 < 30 毫秒 |
| 计费模式 | 对于预配的 RU/s，无论消耗了多少 RU，都按每小时计费。 | 根据数据库操作所消耗的 RU 数量，按小时收费。 |

## <a name="estimating-your-expected-consumption"></a>估计预期消耗量

在某些情况下，可能无法确定应当为给定的工作负荷选择预配吞吐量还是选择无服务器。 你可以估算总体“预期消耗”，即一个月内可能消耗的 RU 总数（你可以借助[此处](plan-manage-costs.md#estimating-serverless-costs)显示的表格进行估算），以便更好地进行决策

示例 1：工作负荷预计的最大突发需求为 500 RU/秒，一个月内总共使用 20,000,000 个 RU。

- 在预配吞吐量模式下，你可以预配 500 RU/秒的容器，每月成本为：$0.008 * 5 * 730 = **$29.20**
- 在无服务器模式下，你需要为消耗的 RU 付费：$0.25 * 20 = **$5.00**

示例 2：工作负荷预计的最大突发需求为 500 RU/秒，一个月内总共使用 250,000,000 个 RU。

- 在预配吞吐量模式下，你可以预配 500 RU/秒的容器，每月成本为：$0.008 * 5 * 730 = **$29.20**
- 在无服务器模式下，你需要为消耗的 RU 付费：$0.25 * 250 = **$62.50**

（这些示例未考虑存储成本，这在两个模式之间是相同的）

> [!NOTE]
> 上面示例中所示的成本仅用于演示目的。 有关最新定价信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[在 Azure Cosmos DB 上预配吞吐量](set-throughput.md)
- 详细了解 [Azure Cosmos DB 无服务器](serverless.md)
- 熟悉[请求单位](request-units.md)的概念
