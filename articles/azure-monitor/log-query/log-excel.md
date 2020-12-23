---
title: 集成 Log Analytics 和 Excel
description: 获取 Excel 中的 Log Analytics 查询并刷新 Excel 中的结果。
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d903d1bb16ba3576d0092979f1cc6b82fac1c0be
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507447"
---
# <a name="integrate-log-analytics-and-excel"></a>集成 Log Analytics 和 Excel

你可以使用 M 查询和 Log Analytics API 来集成 Azure Monitor Log Analytics 和 Microsoft Excel。  此集成允许您将500000记录发送到 Excel。

> [!NOTE]
> 由于 Excel 是本地客户端应用程序，因此本地硬件和软件限制会影响它的性能和处理大型数据集的能力。

## <a name="create-your-m-query-in-log-analytics"></a>在 Log Analytics 中创建 M 查询 

1. 与往常一样，在 Log analytics 中 **创建和运行查询** 。 如果在用户界面中遇到10000记录限制，请不要担心。  建议使用相对日期，如 "前" 函数或 UI 时间选取器，以便 Excel 刷新正确的数据集。
  
2. **导出查询** -对查询及其结果感到满意后，使用 *"导出" 菜单下* 的 "Log Analytics **导出到 Power BI (m 查询)** 菜单选项，将查询导出到 m：

:::image type="content" source="media/log-excel/export-query.png" alt-text="通过数据和导出选项 Log Analytics 查询" border="true":::



如果选择此选项，则会下载一个 .txt 文件，其中包含可在 Excel 中使用的 M 代码。

上面所示的查询将导出以下 M 代码。 下面是在示例中为查询导出的 M 代码的示例：

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>将查询连接到 Excel 

导入查询。 

1. 打开 Microsoft Excel。 
1. 在功能区中，切换到 " **数据** " 菜单。 选择 " **获取数据** "。 在 " **其他源** " 中，选择 " **空白查询** "：
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="&quot;从 Excel 中的空白导入&quot; 选项" border="true":::

1. 在 "Power query" 窗口中，选择 " **高级编辑器** "：

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel 高级查询编辑器" border="true":::

 
1. 用从 Log Analytics 导出的查询替换 "高级编辑器" 中的文本：

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="创建空白查询" border="true":::
 
1. 选择 " **完成** "，然后 **加载并关闭** 。 Excel 使用 Log analytics API 执行查询并显示结果集。
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Excel 中的查询结果" border="true":::

##  <a name="refreshing--data"></a>正在刷新数据

您可以直接从 Excel 中刷新数据。 在 Excel 功能区的 " **数据** " 菜单组中，选择 " **刷新** " 按钮。
 
## <a name="next-steps"></a>后续步骤

有关 Excel 与外部数据源的集成的详细信息，请参阅 [从外部数据源导入数据 (Power Query) ](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)