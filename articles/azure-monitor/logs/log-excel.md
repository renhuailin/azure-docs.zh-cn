---
title: 集成 Log Analytics 和 Excel
description: 将 Log Analytics 查询获取到 Excel 中并在 Excel 中刷新结果。
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 06/10/2021
ms.openlocfilehash: 7cfe8ac2badd292b73b77dd13d7e7ca4bf24a428
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114474118"
---
# <a name="integrate-log-analytics-and-excel"></a>集成 Log Analytics 和 Excel

可以使用 M 查询和 Log Analytics API 来集成 Azure Monitor Log Analytics 和 Microsoft Excel。 该集成可用于发送一定数量的记录和一定 MB 的数据。 这些限制记录在 Azure 门户部分的 [Azure Monitor Log Analytics 工作区限制](../service-limits.md#log-analytics-workspaces)中。 

> [!NOTE]
> 由于 Excel 是本地客户端应用程序，因此本地硬件和软件限制会影响其性能以及其处理大型数据集的能力。

## <a name="create-your-m-query-in-log-analytics"></a>在 Log Analytics 中创建 M 查询 

1. 像往常一样，在 Log analytics 中创建和运行查询。 如果在用户界面中达到记录数限制，请不要担心。  建议使用相对日期（如“ago”函数或 UI 时间选取器），以便 Excel 刷新正确的数据集。
  
2. 导出查询 - 对查询及其结果感到满意后，使用 Log Analytics 的“导出”菜单下的“导出到 Power BI (M 查询)”菜单选项，将查询导出到 M：

:::image type="content" source="media/log-excel/export-query.png" alt-text="包含数据和导出选项的 Log Analytics 查询" border="true":::



如果选择此选项，则会下载一个 .txt 文件，其中包含可在 Excel 中使用的 M 代码。

上面所示的查询将导出以下 M 代码。 下面是为我们的示例中的查询导出的 M 代码示例：

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

若要导入查询，请执行以下操作： 

1. 打开 Microsoft Excel。 
1. 在功能区中，转到“数据”菜单。 选择“获取数据”。 在“从其他源”中，选择“空查询”：
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Excel 中用于从空查询进行导入的选项" border="true":::

1. 在 Power Query 窗口中，选择“高级编辑器”：

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel 高级查询编辑器" border="true":::

 
1. 将高级编辑器中的文本替换为从 Log Analytics 导出的查询：

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="创建空白查询" border="true":::
 
1. 选择“完成”，然后选择“加载并关闭”。 Excel 将使用 Log Analytics API 执行查询，并且随后会显示结果集。
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Excel 中的查询结果" border="true":::

> [!Note]
> 如果记录数小于预期值，则结果的量可能超出了 61MiB 限制。 请尝试在查询中使用 `project` 或 `project-away` 将列限制为所需的列。

##  <a name="refreshing--data"></a>刷新数据

可以直接从 Excel 中刷新数据。 在 Excel 功能区中的“数据”菜单组中，选择“刷新”按钮。
 
## <a name="next-steps"></a>后续步骤

有关 Excel 与外部数据源的集成的详细信息，请参阅[从外部数据源导入数据 (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)
