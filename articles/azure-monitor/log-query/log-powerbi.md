---
title: 与 Power BI 和 Excel Log Analytics 集成
description: 如何将 Log Analytics 结果发送到 Power BI
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: a4e2faf87ac3dddf91e6945343a46b02df72db0a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507442"
---
# <a name="log-analytics-integration-with-power-bi"></a>与 Power BI Log Analytics 集成

本文重点介绍如何将数据从 Log Analytics 送入 Microsoft Power BI，以创建更具视觉吸引力的报表和仪表板。 

## <a name="background"></a>背景 

Azure Monitor 日志是为引入日志提供端到端解决方案的平台。 [Azure Monitor Log Analytics](../platform/data-platform.md#) 是用于查询这些日志的接口。 有关整个 Azure Monitor 包含 Log Analytics 的数据平台的详细信息，请参阅 [Azure Monitor 数据平台](../platform/data-platform.md)。 

Microsoft Power BI 是 Microsoft 的数据可视化平台。 有关如何入门的详细信息，请参阅 [Power BI 主页](https://powerbi.microsoft.com/)。 


通常，您可以使用免费 Power BI 功能来集成和创建视觉吸引力的报表和仪表板。

更高级的功能可能需要购买 Power BI Pro 或高级帐户。 这些功能包括： 
 - 共享工作 
 - 计划的刷新
 - Power BI 应用 
 - 数据流和增量刷新 

有关详细信息，请参阅[了解有关 Power BI 定价和功能的详细](https://powerbi.microsoft.com/pricing/)信息 

## <a name="integrating-queries"></a>集成查询  

Power BI 使用 [M 查询语言](/powerquery-m/power-query-m-language-specification/) 作为其主要查询语言。 

Log Analytics 查询可以导出到 M 并直接在 Power BI 中使用。 运行成功的查询后，从 Log Analytics UI 顶部操作栏中的 " **导出** " 按钮选择 **导出到 Power BI (M 查询)** 。

:::image type="content" source="media/log-powerbi-excel/export-query2.png" alt-text="显示导出选项菜单下拉的 Log Analytics 查询" border="true":::

Log Analytics 创建一个 .txt 文件，其中包含可在 Power BI 中直接使用的 M 代码。

## <a name="connecting-your-logs-to-a-dataset"></a>将日志连接到数据集 

Power BI 数据集是可供报表和可视化的数据源。 若要将 Log Analytics 查询连接到数据集，请将 Log Analytics 导出的 M 代码从 Power BI 复制到空白查询中。 

有关详细信息，请参阅 [了解 Power BI 数据集](/power-bi/service-datasets-understand/)。 

## <a name="collect-data-with-power-bi-dataflows"></a>Power BI 数据流收集数据 

Power BI 数据流还允许收集和存储数据。 有关详细信息，请参阅 [Power BI 数据流](/power-bi/service-dataflows-overview)。

数据流是一种 "云 ETL"，旨在帮助您收集和准备您的数据。 数据集是 "模型"，旨在帮助你连接不同的实体并为需求建模。

## <a name="incremental-refresh"></a>增量刷新 

Power BI 数据集和 Power BI 数据流都有增量刷新选项。 Power BI 数据流和 Power BI 数据集支持此功能，但需要 Power BI Premium 才能使用。  


增量刷新运行小型查询，并更新每个运行的数据量较少，而不是在运行查询时再次引入所有数据。 您可以选择保存大量数据，但每次运行查询时都要增加数据的新增量。 此行为非常适合用于更长时间运行的报表。

Power BI 增量刷新依赖于结果集中存在 *日期时间* 的存在。 在配置增量刷新之前，请确保 Log Analytics 查询结果集包含至少一个 *日期时间* 。 

若要了解详细信息以及如何配置增量刷新，请参阅 [Power BI 数据集和增量刷新](/power-bi/service-premium-incremental-refresh) ，并 [Power BI 数据流和增量刷新](/power-bi/service-dataflows-incremental-refresh)。

## <a name="reports-and-dashboards"></a>报表和仪表板卡

将数据发送到 Power BI 后，你可以继续使用 Power BI 来创建报表和仪表板。

有关详细信息，请参阅 [本指南中有关如何创建第一个 Power BI 模型和报表](/learn/modules/build-your-first-power-bi-report/)的信息。  

## <a name="excel-integration"></a>Excel 集成

您可以使用 Power BI 中使用的相同 M 集成来与 Excel 电子表格集成。 有关详细信息，请参阅本 [指南中有关如何与 excel 集成](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) ，然后粘贴从 Log Analytics 导出的 M 查询。

可在[集成 Log Analytics 和 Excel](log-excel.md)中找到其他信息

## <a name="next-steps"></a>后续步骤

[Log Analytics 查询](log-query-overview.md)入门。
