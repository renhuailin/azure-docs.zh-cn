---
title: 优化 Gen2 缓存
description: 了解如何通过 Azure 门户监视 Gen2 缓存。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9de795c54f55295fa69ed7fcb5dd894e2963385b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566624"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>如何监视自适应缓存

本文介绍如何监视和排查查询性能下降的问题，只需确定工作负荷是否在充分利用专用 SQL 池的自适应缓存即可。

专用 SQL 池存储体系结构自动将最常查询的列存储段分层到驻留在基于 NVMe 的 SSD 上的缓存中。 如果查询检索驻留在缓存中的段，则可提高性能。
 
## <a name="troubleshoot-using-the-azure-portal"></a>使用 Azure 门户进行故障排除

可以使用 Azure Monitor 来查看缓存指标，以便对查询性能问题进行排除故障。 首先转到 Azure 门户，然后依次单击“监视”  、“指标”  和“+ 选择范围”  ：

![屏幕截图显示了从 Azure 门户的“指标”中选择的“选择范围”。](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

使用搜索栏和下拉栏找到你的专用 SQL 池。 然后选择“应用”。

![屏幕截图显示了“选择范围”窗格，可在其中选择数据仓库。](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

排查缓存问题时，关键指标是“缓存命中百分比”和“缓存使用百分比”。  选择“缓存命中百分比”  ，然后使用“添加指标”  按钮添加“缓存已用百分比”  。 

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
