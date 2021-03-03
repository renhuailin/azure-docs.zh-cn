---
title: Log Analytics 与 Power BI 和 Excel 的集成
description: 如何将结果从 Log Analytics 发送到 Power BI
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d1db5f5e4164d971dff3ec6b042fa4b0a969e508
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704236"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics 与 Power BI 的集成

本文重点介绍如何将数据从 Log Analytics 送入 Microsoft Power BI，以创建更具视觉吸引力的报表和仪表板。 

## <a name="background"></a>背景 

Azure Monitor 日志是为日志引入提供端到端解决方案的平台。 [Azure Monitor Log Analytics](../platform/data-platform.md#) 是用于查询这些日志的界面。 若要详细了解包括 Log Analytics 在内的整个 Azure Monitor 数据平台，请参阅 [Azure Monitor 数据平台](../data-platform.md)。 

Microsoft Power BI 是 Microsoft 的数据可视化平台。 有关如何入门的详细信息，请参阅 [Power BI 的主页](https://powerbi.microsoft.com/)。 


通常，你可以使用免费的 Power BI 功能来集成和创建在视觉上具有吸引力的报表和仪表板。

若要使用更高级的功能，可能需要购买 Power BI Pro 或高级帐户。 这些功能包括： 
 - 共享你的工作 
 - 计划的刷新
 - Power BI 应用 
 - 数据流和增量刷新 

有关详细信息，请参阅[详细了解 Power BI 定价和功能](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>集成查询  

Power BI 使用 [M 查询语言](/powerquery-m/power-query-m-language-specification/)作为其主要查询语言。 

Log Analytics 查询可以导出到 M 并直接在 Power BI 中使用。 运行成功的查询后，从 Log Analytics UI 顶部操作栏的“导出”按钮中选择“导出到 Power BI (M 查询)”。 


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="显示了导出选项菜单下拉列表的 Log Analytics 查询" border="true":::

Log Analytics 会创建一个 .txt 文件，其中包含可以直接在 Power BI 中使用的 M 代码。

## <a name="connecting-your-logs-to-a-dataset"></a>将日志连接到数据集 

Power BI 数据集是已就绪的可用于报告和可视化效果的数据源。 若要将 Log Analytics 查询连接到数据集，请在 Power BI 中将从 Log Analytics 导出的 M 代码复制到空白查询中。 

有关详细信息，请参阅[了解 Power BI 数据集](/power-bi/service-datasets-understand/)。 

## <a name="collect-data-with-power-bi-dataflows"></a>使用 Power BI 数据流收集数据 

Power BI 数据流还允许收集和存储数据。 有关详细信息，请参阅 [Power BI 数据流](/power-bi/service-dataflows-overview)。

数据流是一种“云 ETL”，旨在帮助你收集和准备数据。 数据集是“模型”，旨在帮助你连接不同的实体并根据你的需求对其进行建模。

## <a name="incremental-refresh"></a>增量刷新 

Power BI 数据集和 Power BI 数据流都有增量刷新选项。 Power BI 数据流和 Power BI 数据集支持此功能，但你需要 Power BI Premium 才能使用此功能。  


增量刷新运行小型查询，每次运行只更新较少的数据量，而不是在运行查询时一次又一次引入所有数据。 你可以选择保存大量数据，但每次运行查询时会添加新的增量数据。 此行为非常适合运行时间较长的报表。

Power BI 增量刷新依赖于结果集中存在的日期/时间字段。 在配置增量刷新之前，请确保 Log Analytics 查询结果集包含至少一个日期/时间字段。 

有关增量刷新配置方面的详细信息和具体方法，请参阅 [Power BI 数据集和增量刷新](/power-bi/service-premium-incremental-refresh)和 [Power BI 数据流和增量刷新](/power-bi/service-dataflows-incremental-refresh)。

## <a name="reports-and-dashboards"></a>报表和仪表板卡

将数据发送到 Power BI 后，你可以继续使用 Power BI 来创建报表和仪表板。

有关详细信息，请参阅[有关如何创建你的第一个 Power BI 模型和报表的此指南](/learn/modules/build-your-first-power-bi-report/)。  

## <a name="excel-integration"></a>Excel 集成

你可以使用 Power BI 中使用的 M 集成来与 Excel 电子表格集成。 有关详细信息，请参阅[有关如何与 Excel 集成的此指南](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)，然后粘贴从 Log Analytics 导出的 M 查询。

有关其他信息，可参阅[集成 Log Analytics 和 Excel](log-excel.md)

## <a name="next-steps"></a>后续步骤

开始使用 [Log Analytics 查询](./log-query-overview.md)。