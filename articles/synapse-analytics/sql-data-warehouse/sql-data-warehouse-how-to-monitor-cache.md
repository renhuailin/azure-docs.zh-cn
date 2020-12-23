---
title: 优化 Gen2 缓存
description: 了解如何通过 Azure 门户监视 Gen2 缓存。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 041751b5b23dbb3153f1ae638303579a860c0e5b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020157"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>如何监视自适应缓存

本文介绍如何通过确定工作负荷是否以最佳方式利用专用 SQL 池的自适应缓存来监视和解决速度缓慢的查询性能问题。

专用 SQL 池存储体系结构会自动将最常查询的列存储段划分为位于基于 NVMe 的 Ssd 的缓存中。 当查询检索驻留在缓存中的段时，性能会更高。
 
## <a name="troubleshoot-using-the-azure-portal"></a>使用 Azure 门户进行故障排除

您可以使用 Azure Monitor 查看缓存指标以对查询性能进行故障排除。 首先转到 Azure 门户，然后依次单击“监视”  、“指标”  和“+ 选择范围”  ：

![屏幕截图显示了从 Azure 门户的“指标”中选择的“选择范围”。](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

使用搜索和下拉栏定位专用的 SQL 池。 然后选择“应用”。

![屏幕截图显示了“选择范围”窗格，可在其中选择数据仓库。](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

缓存故障排除的关键指标是 **缓存命中百分比** 和 **缓存已用百分比**。 选择“缓存命中百分比”  ，然后使用“添加指标”  按钮添加“缓存已用百分比”  。 

![缓存指标](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>缓存命中和使用百分比

下表根据缓存指标的值对场景进行了说明：

|                                | **缓存命中百分比高** | **缓存命中百分比低** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **缓存使用百分比高** |          方案 1           |          方案 2          |
| **缓存使用百分比低**  |          方案 3           |          方案 4          |

**场景 1：** 你的缓存使用已优化。 [排查](sql-data-warehouse-manage-monitor.md)可能导致查询速度变慢的其他方面的情况。

**场景 2：** 当前工作数据集不适合放置在缓存中，这会因物理读取导致缓存命中百分比低。 考虑提升性能级别并重新运行工作负荷，以便填充缓存。

**场景 3：** 查询运行速度慢的原因可能与缓存无关。 [排查](sql-data-warehouse-manage-monitor.md)可能导致查询速度变慢的其他方面的情况。 也可以考虑[缩减实例](sql-data-warehouse-manage-monitor.md)，通过缩减缓存大小来节省成本。 

**场景 4：** 你使用了冷缓存，这可能是查询速度慢的原因。 考虑重新运行查询，因此工作数据集现在应该位于缓存中。 

> [!IMPORTANT]
> 如果缓存命中百分比或缓存使用百分比在重新运行工作负荷后未更新，则表明工作集可能已驻留在内存中。 仅缓存聚集列存储表。

## <a name="next-steps"></a>后续步骤
有关常规查询性能优化的详细信息，请参阅[监视查询执行](sql-data-warehouse-manage-monitor.md#monitor-query-execution)。
