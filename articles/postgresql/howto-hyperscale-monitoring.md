---
title: 如何查看指标 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 如何访问 Azure Database for PostgreSQL - 超大规模 (Citus) 的数据库指标
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/05/2021
ms.openlocfilehash: b698a69631ba2ad8aba58f6de17d8cbee1cbafaf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620812"
---
# <a name="how-to-view-metrics-in-azure-database-for-postgresql---hyperscale-citus"></a>如何查看 Azure Database for PostgreSQL - 超大规模 (Citus) 中的指标

资源指标针对超大规模 (Citus) 服务器组的每个节点提供，且可跨节点聚合。

## <a name="view-metrics"></a>查看指标

若要访问超大规模 (Citus) 服务器组的指标，请在 Azure 门户中打开“监视”下的“指标”。

:::image type="content" source="media/howto-hyperscale-monitoring/metrics.png" alt-text="“指标”屏幕":::

选择维度和聚合（例如“CPU 百分比”和“最大值”），可查看跨所有节点聚合的指标。 有关每个指标的说明，请参阅[此处](concepts-hyperscale-monitoring.md#list-of-metrics)。

:::image type="content" source="media/howto-hyperscale-monitoring/dimensions.png" alt-text="选择维度":::

### <a name="view-metrics-per-node"></a>查看每个节点的指标

在同一图上单独查看每个节点的指标称为“拆分”。
若要启用它，请选择“应用拆分”：

:::image type="content" source="media/howto-hyperscale-monitoring/splitting.png" alt-text="“应用拆分”按钮":::

选择要拆分的值。 对于超大规模 (Citus) 节点，选择“服务器名称”。

:::image type="content" source="media/howto-hyperscale-monitoring/split-value.png" alt-text="选择拆分值":::

现在，每个节点将采用一种颜色编码的线绘制指标。

:::image type="content" source="media/howto-hyperscale-monitoring/split-chart.png" alt-text="多节点图表线":::

## <a name="next-steps"></a>后续步骤

* 查看超大规模 (Citus) [监视概念](concepts-hyperscale-monitoring.md)
