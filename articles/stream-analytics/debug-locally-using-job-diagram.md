---
title: 在 Visual Studio 中使用作业关系图在本地调试 Azure 流分析查询
description: 本文介绍如何在适用于 Visual Studio 的 Azure 流分析工具中使用作业关系图在本地调试查询。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019442"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>在 Visual Studio 中使用作业关系图在本地调试 Azure 流分析查询

不输出结果或输出意外结果的作业是流式处理查询的常见故障排除方案。 在 Visual Studio 中本地测试查询时，可以使用作业关系图检查每个步骤的中间结果集和指标。 作业关系图可以帮助你在解决问题时快速找出问题的根源。

## <a name="debug-a-query-using-job-diagram"></a>使用作业关系图调试查询

可以使用一个 Azure 流分析脚本将输入数据转换为输出数据。 作业关系图显示了数据如何通过多个查询步骤从输入源（事件中心、IoT 中心等）流出，最后流入输出接收器。 每个查询步骤映射到在脚本中使用 `WITH` 语句定义的临时结果集。 可以查看每个中间结果集中每个查询步骤的数据和指标，以找出问题的根源。

> [!NOTE]
> 此作业关系图仅显示单个节点中的本地测试的数据和指标。 不应将其用于性能优化和故障排除。

### <a name="start-local-testing"></a>启动本地测试

使用此[快速入门](stream-analytics-quick-create-vs.md)了解如何使用 Visual Studio 创建流分析作业，或者如何[将现有作业导出到本地项目](stream-analytics-vs-tools.md#export-jobs-to-a-project)。 若要使用本地输入数据测试查询，请按照这些[说明](stream-analytics-live-data-local-testing.md)操作。 如果要使用实时输入进行测试，请转到下一步。

> [!NOTE]
> 如果将作业导出到本地项目并想要针对实时输入流进行测试，则需要再次为所有输入指定凭据。  

从脚本编辑器中选择输入和输出源，并选择“本地运行”。 作业关系图在右侧显示。

### <a name="view-the-intermediate-result-set"></a>查看中间结果集  

1. 选择查询步骤以导航到脚本。 你将自动定向到左侧编辑器中的相应脚本。

   ![作业关系图导航脚本](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 选择查询步骤，然后在弹出的对话框中选择“预览”。 结果集将显示在底部结果窗口的选项卡中。

   ![作业关系图预览结果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>查看步骤指标

在本部分，你将浏览适用于关系图每个组成部分的指标。

#### <a name="input-sources-live-stream"></a>输入源（实时流）

![作业关系图实时输入源](./media/debug-locally-using-job-diagram/live-input.png)

|指标|说明|
|-|-|
|TaxiRide| 输入的名称。|
|**事件中心** | 输入源类型。|
|**事件**|事件读取数。|
|积压的事件源|还需要为事件中心和 IoT 中心输入读取多少条消息。|
|事件（以字节为单位）|读取的字节数。|
| 降级的事件|出现了非反序列化问题的事件计数。|
|早期事件| 应用程序时间戳在高水印之前的事件数。|
|**较晚事件**| 应用程序时间戳在高水印之后的事件数。|
|**事件源**| 数据单元读取数。 例如，blob 的数目。|

#### <a name="input-sources-local-input"></a>输入源（本地输入）

![作业关系图本地输入源](./media/debug-locally-using-job-diagram/local-input.png)

|指标|说明|
|-|-|
|TaxiRide| 输入的名称。|
|**行计数**| 从该步骤生成的行数。|
|**数据大小**| 从此步骤生成的数据大小。|
|本地输入| 使用本地数据作为输入。|

#### <a name="query-steps"></a>查询步骤

![作业关系图查询步骤](./media/debug-locally-using-job-diagram/query-step.png)

|指标|说明|
|-|-|
|TripData|临时结果集的名称。|
|**行计数**| 从该步骤生成的行数。|
|**数据大小**| 从此步骤生成的数据大小。|
  
#### <a name="output-sinks-live-output"></a>输出接收器（实时输出）

![显示本地输出接收器的作业关系图。](./media/debug-locally-using-job-diagram/live-output.png)

|指标|说明|
|-|-|
|regionaggEH|输出的名称。|
|**事件**|输出到接收器的事件数。|

#### <a name="output-sinks-local-output"></a>输出接收器（本地输出）

![作业关系图本地输出接收器](./media/debug-locally-using-job-diagram/local-output.png)

|指标|说明|
|-|-|
|regionaggEH|输出的名称。|
|本地输出| 输出到本地文件的结果。|
|**行计数**| 输出到本地文件的行数。|
|**数据大小**| 输出到本地文件的数据大小。|

### <a name="close-job-diagram"></a>关闭作业关系图

如果不再需要作业关系图，请选择右上角的“关闭”。 关闭关系图窗口后，需要再次启动本地测试才能看到它。

### <a name="view-job-level-metrics-and-stop-running"></a>查看作业级别指标和停止运行

其他作业级别指标显示在弹出控制台中。 如果要停止作业，请在控制台中按 Ctrl+C。

![作业关系图停止作业](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>限制

* 由于身份验证模型的限制，不支持 Power BI 和 Azure Data Lake Storage Gen1 输出接收器。

* 只有云输入选项具有[时间策略](./stream-analytics-time-handling.md)支持，本地输入选项不具备此支持。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)
* [使用用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据（预览）](stream-analytics-live-data-local-testing.md)