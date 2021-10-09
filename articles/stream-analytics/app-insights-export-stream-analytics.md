---
title: 使用流分析从 Azure Application Insights 进行导出 | Microsoft Docs
description: 流分析可以持续转换、筛选和路由从 Application Insights 导出的数据。
ms.topic: conceptual
ms.date: 01/08/2019
ms.service: stream-analytics
ms.openlocfilehash: 56ce47f232c78637b82c16501e47f2bff8707430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594392"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>使用流分析处理从 Application Insights 导出的数据

[Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)是用于处理[从 Application Insights 导出](../azure-monitor/app/export-telemetry.md)的数据的理想工具。 流分析可以从各种源提取数据。 它可以转换和筛选数据，然后将其路由到各种接收器。

在本示例中，我们将使用连续导出创建一个适配器用于从 Application Insights 提取数据，重命名和处理某些字段，然后通过管道将数据传送到 Power BI。

> [!WARNING]
> 还有便利得多的[建议方法可在 Power BI 中显示 Application Insights 数据](../azure-monitor/app/export-power-bi.md)。 本文所述的途径只是一个示例，演示如何处理导出的数据。

> [!IMPORTANT]
> 连续导出已被弃用，只有经典 Application Insights 资源支持连续导出。 [迁移到基于工作区的 Application Insights 资源](../azure-monitor/app/convert-classic-resource.md)，以使用[诊断设置](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export)导出遥测数据。


![通过流分析导出到 PowerBI 的块关系图。](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>在 Azure 中创建存储
连续导出始终将数据输出到 Azure 存储帐户，因此首先需要创建存储。

1. 在 [Azure 门户](https://portal.azure.com)上的订阅中创建一个“经典”存储帐户。
   
   ![Azure 门户的屏幕截图，请选择“新建”、“数据”、“存储”。](./media/app-insights-export-stream-analytics/030.png)
2. 创建容器
   
    ![新存储的屏幕截图，请选择“容器”，单击“容器”磁贴，然后单击“添加”。](./media/app-insights-export-stream-analytics/040.png)
3. 复制存储访问密钥
   
    稍后需要使用它来设置流分析服务的输入。
   
    ![存储的屏幕截图，请打开“设置”、“密钥”，并复制主访问密钥。](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>开始向 Azure 存储连续导出

[连续导出](../azure-monitor/app/export-telemetry.md)会将数据从 Application Insights 移入 Azure 存储。 

1. 在 Azure 门户中，浏览到为应用程序创建的 Application Insights 资源。
   
    ![Azure 门户中所有资源的屏幕截图，请依次选择 Application Insights 和应用程序。](./media/app-insights-export-stream-analytics/050.png)
2. 创建连续导出。
   
    ![Application Insights 的屏幕截图，请选择“设置”、“连续导出”，然后选择“添加”。](./media/app-insights-export-stream-analytics/060.png)

    选择前面创建的存储帐户：

    ![连续导出的屏幕截图，请选择“+添加”，然后设置导出目标。](./media/app-insights-export-stream-analytics/070.png)

    设置想要查看的事件类型：

    ![添加连续导出和选择事件类型的屏幕截图。](./media/app-insights-export-stream-analytics/080.png)

1. 让我们累积一些数据。 请休息一下，让其他人先使用该应用程序一段时间。 应用程序中会逐渐传入遥测数据，[指标资源管理器](../azure-monitor/essentials/metrics-charts.md)中会显示统计图表，[诊断搜索](../azure-monitor/app/diagnostic-search.md)中会显示各个事件。
   
    此外，数据将导出到存储。 
2. 检查导出的数据 在 Visual Studio 中，请选择“查看”>“Cloud Explorer”，并打开“Azure”>“存储”。 （如果没有此菜单选项，则需要安装 Azure SDK：打开“新建项目”对话框，打开 Visual C# /云/获取用于 .NET 的 Microsoft Azure SDK。）
   
    ![屏幕截图中显示了如何设置要查看的事件类型。](./media/app-insights-export-stream-analytics/04-data.png)
   
    记下派生自应用程序名称和检测密钥的路径名称的共同部分。

事件以 JSON 格式写入 Blob 文件。 每个文件可能包含一个或多个事件。 因此我们想要读取事件数据，并筛选出所需的字段。 可以针对数据执行各种操作，但我们目前的计划是使用流分析通过管道将数据传送到 Power BI。

## <a name="create-an-azure-stream-analytics-instance"></a>创建 Azure 流分析实例

在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 流分析服务，并创建新的流分析作业：

![屏幕截图中显示了 Azure 门户中用于创建流分析作业的主页。](./media/app-insights-export-stream-analytics/001.png)

![屏幕截图中显示了创建新的流分析作业时所需的详细信息。](./media/app-insights-export-stream-analytics/002.png)

创建新作业后，选择“转到资源”。

![屏幕截图中显示了新的流分析作业部署成功时接收到的消息。](./media/app-insights-export-stream-analytics/003.png)

### <a name="add-a-new-input"></a>添加新输入

![屏幕截图中显示了如何向流分析作业添加输入。](./media/app-insights-export-stream-analytics/004.png)

将此位置设置为从连续导出 Blob 接收输入：

![屏幕截图中显示了如何配置流分析作业以从连续导出 blob 中获取输入。](./media/app-insights-export-stream-analytics/0005.png)

现在需要使用存储帐户的主访问密钥（前面已记下此密钥）。 将此密钥设置为存储帐户密钥。

### <a name="set-path-prefix-pattern"></a>设置路径前缀模式

**请务必将“日期格式”设置为 YYYY-MM-DD（包含短划线）。**

“路径前缀模式”指定流分析在存储中的哪个位置查找输入文件。 需要将它设置为与连续导出存储数据的方式相对应。 设置如下：

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

在本示例中：

* `webapplication27` 是 Application Insights 资源的名称，**采用全小写**。
* `1234...` 是 Application Insights 资源的检测密钥，但 **省略了短划线**。 
* `PageViews` 是要分析的数据类型。 可用的类型取决于在连续导出中设置的筛选器。 检查导出的数据以查看其他可用类型，并查看[导出数据模型](../azure-monitor/app/export-data-model.md)。
* `/{date}/{time}` 是以文本形式写入的模式。

> [!NOTE]
> 检查存储，确保路径正确。

## <a name="add-new-output"></a>添加新输出

现在选择作业 >“输出” > “添加”。

![屏幕截图中显示了如何选择流分析作业以添加新输出。](./media/app-insights-export-stream-analytics/006.png)


![新输出的屏幕截图，请选择新、“输出”、“添加”，然后选择“Power BI”。](./media/app-insights-export-stream-analytics/010.png)

提供 **工作或学校帐户**，以授权流分析访问 Power BI 资源。 然后为输出、目标 Power BI 数据集和表指定名称。

## <a name="set-the-query"></a>设置查询

查询控制从输入到输出的转换。

使用“测试”功能检查输出是否正确。 在测试中提供从输入页获取的示例数据。

### <a name="query-to-display-counts-of-events"></a>用于显示事件计数的查询

粘贴以下查询：

```SQL
SELECT
  flat.ArrayValue.name,
  count(*)
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.[event]) as flat
GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input 是为流输入指定的别名
* pbi-output 是定义的输出别名
* 之所以使用 [OUTER APPLY GetElements](/stream-analytics-query/apply-azure-stream-analytics)，是因为事件名称在嵌套的 JSON 数组中。 然后，使用“选择”来选择事件名称，以及在相应时间段内使用该名称的实例计数。 [Group By](/stream-analytics-query/group-by-azure-stream-analytics) 子句将元素分组成以 1 分钟为单位的时间段。

### <a name="query-to-display-metric-values"></a>用于显示指标值的查询

```SQL
SELECT
  A.context.data.eventtime,
  avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.context.custom.metrics) as flat
GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime
```

* 此查询将钻取指标遥测数据，获取事件时间和指标值。 指标值在数组内部，因此我们使用了 OUTER APPLY GetElements 模式来提取行。 在本例中，“myMetric”是指标的名称。

### <a name="query-to-include-values-of-dimension-properties"></a>用于包含维度属性值的查询

```SQL
WITH flat AS (
SELECT
  MySource.context.data.eventTime as eventTime,
  InstanceId = MyDimension.ArrayValue.InstanceId.value,
  BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
FROM MySource
OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
)
SELECT
  eventTime,
  InstanceId,
  BusinessUnitId
INTO AIOutput
FROM flat
```

* 此查询包含不依赖于特定维度的维度属性的值，该维度位于维度数组中某个固定的索引处。

## <a name="run-the-job"></a>运行作业

可以选择从过去的某个日期启动作业。

![流分析的屏幕截图，请选择该作业并单击“查询”。 粘贴以下示例。](./media/app-insights-export-stream-analytics/008.png)

等待作业运行。

## <a name="see-results-in-power-bi"></a>在 Power BI 中查看结果

> [!WARNING]
> 还有便利得多的[建议方法可在 Power BI 中显示 Application Insights 数据](../azure-monitor/app/export-power-bi.md)。 本文所述的途径只是一个示例，演示如何处理导出的数据。


使用工作或学校帐户打开 Power BI，并选择已定义为流分析作业输出的数据集和表。

![Power BI 的屏幕截图，请选择数据集和字段。](./media/app-insights-export-stream-analytics/200.png)

现在，可以在 [Power BI](https://powerbi.microsoft.com) 中的报告和仪表板内使用此数据集。

![屏幕截图中显示了根据 Power BI 中的数据集生成的报告示例。](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>没有数据？

* 请检查是否已正确地将[日期格式设置](#set-path-prefix-pattern)为 YYYY-MM-DD（包括短划线）。

## <a name="next-steps"></a>后续步骤
* [连续导出](../azure-monitor/app/export-telemetry.md)
* [属性类型和值的详细数据模型参考。](../azure-monitor/app/export-data-model.md)
* [Application Insights](../azure-monitor/app/app-insights-overview.md)
