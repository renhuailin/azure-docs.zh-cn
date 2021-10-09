---
title: 使用 Power BI 实现 AI 推理事件的实时可视化效果
description: 可以使用 Azure 视频分析器进行连续视频录制或基于事件的录制。 本教程逐步演练从 Microsoft Power BI 中的 IoT 中心实时可视化 AI 推理事件的步骤。
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 09/08/2021
ms.openlocfilehash: 68eb62e126065a6c39b5fd6648afb4b96accbd2d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401914"
---
# <a name="tutorial-real-time-visualization-of-ai-inference-events-with-power-bi"></a>教程：使用 Power BI 实现 AI 推理事件的实时可视化效果

Azure 视频分析器提供捕获、录制和分析实时视频的功能，并且可采用 AI 推理事件的形式将视频分析的结果发布到 [IoT Edge 中心](../../iot-edge/iot-edge-runtime.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)。 这些 AI 推理事件随后可以路由到其他目标，包括 Visual Studio Code 和 Azure 服务（例如时序见解和事件中心）。

通过仪表板可以很好地洞察你的业务，并一目了然地查看所有重要指标。 可以通过 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/#overview)，使用 [Microsoft Power BI](https://powerbi.microsoft.com/) 来可视化视频分析器生成的 AI 推理事件，以快速获取见解并与组织中的同事共享仪表板。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/tutorial-block-diagram.png" alt-text="显示如何通过 Azure 流分析将 Azure 视频分析器连接到 Microsoft Power BI 的方框图。":::

在本教程中，将：

- 创建并运行流分析作业，以从 IoT 中心检索所需数据并将其发送到 Power BI
- 运行生成推理事件的实时管道
- 创建 Power BI 仪表板以可视化 AI 推理

## <a name="suggested-pre-reading"></a>建议的读前准备

- 视频分析器中的[监视和日志记录](monitor-log-edge.md)
- [从 IoT 中心内置终结点读取设备到云消息](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)
- [Power BI 仪表板](/power-bi/create-reports/service-dashboards)简介

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本教程基于使用[越线示例](use-line-crossing.md)来检测对象在实时视频源中越过虚拟线的时间。 可以选择为其他管道创建可视化效果 - 需要具有 IoT 中心消息接收器的管道。 请确保创建了实时管道，但仅在创建流分析作业后才激活它。

  > [!TIP]
  >
  > - [越线示例](use-line-crossing.md)使用一段 5 分钟的视频录制。 若要在可视化效果方面获得最佳效果，请使用[其他数据集](https://github.com/Azure/video-analyzer/tree/main/media#other-dataset)中提供的高速公路上的 60 分钟车辆录制。
  > - 请参阅[常见问题解答](faq-edge.yml)中的配置和部署部分，以了解如何将示例视频文件添加到 rtsp 模拟器。 添加后，编辑 `rtspUrl` 值以指向新视频文件。
  > - 如果遵循越线示例并且在使用 [AVA C# 示例存储库](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)，则在“属性”->“参数”->“值”中将 operations.json 文件编辑为 `"rtsp://rtspsim:554/media/camera-3600s.mkv"`，以将视频源更改为 60 分钟的录制。

- 一个 [Power BI](https://powerbi.microsoft.com/) 帐户。

## <a name="create-and-run-a-stream-analytics-job"></a>创建和运行流分析作业

[流分析](https://azure.microsoft.com/services/stream-analytics/#overview)是一项完全托管的实时分析服务，旨在帮助你分析和处理快速移动数据流。 在此部分中，你会创建流分析作业，定义输入、输出以及用于检索所需数据的查询。

### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“创建资源”。 在搜索框中键入“流分析作业”，然后从下拉列表中选择它。 在“流分析作业”概述页面中，选择“创建” 
2. 为作业输入以下信息：

   - **作业名称**：作业的名称。 该名称必须全局唯一。
   - 订阅：选择用于设置越线示例的相同订阅。
   - 资源组：使用 IoT Edge 中心在设置越线示例时所使用的相同资源组。
   - **位置**：与资源组使用同一位置。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-asa-job.png" alt-text="创建新的流分析作业的屏幕截图。":::

3. 选择“创建” 。

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

1. 打开流分析作业。
2. 在“作业拓扑”下选择“输入”。
3. 在“输入”窗格中选择“添加流输入”，然后从下拉列表中选择“IoT 中心” 。 在新输入窗格中输入以下信息：

   - 输入别名：输入唯一的输入别名，如“iothubinput”。
   - 从订阅选择 IoT 中心：选中此单选按钮。
   - 订阅：选择本文使用的 Azure 订阅。
   - IoT 中心：选择用于设置越线示例的 IoT 中心。
   - 共享访问策略名称：选择希望流分析作业对你的 IoT 中心使用的共享访问策略的名称。 对于本教程，可以选择 iothubowner。 有关详细信息，请参阅[访问控制和权限](../../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)。
   - 共享访问策略密钥：系统会根据你选择的共享访问策略名称自动填充此字段。

   将所有其他字段保留设置为默认值。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-iothub-input.png" alt-text="将 IoT 中心输入添加到流分析作业的屏幕截图。":::

4. 选择“保存”。 

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下选择“输出”。
2. 在“输出”窗格中，选择“添加”，然后在下拉列表中选择“Power BI” 。
3. 在“Power BI - 新建输出”窗格上：

   - 输出别名：输入唯一的输出别名，如“powerbioutput”。
   - 组工作区：选择目标组工作区。
   - 身份验证模式：保留默认的“用户令牌”以进行测试。

   > [!NOTE]
   > 对于生产作业，建议[使用托管标识在 Power BI 中对流分析作业进行身份验证](../../stream-analytics/powerbi-output-managed-identity.md)。

   - 数据集名称：输入数据集名称。
   - 表名称：输入表名称。
   - 授权：选择“授权”，并按照提示登录 Power BI 帐户（令牌有效期为 90 天）。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-pbi-output.png" alt-text="将 Power BI 输出添加到流分析作业的屏幕截图。":::

4. 选择“保存”  。

   > [!NOTE]
   > 有关使用 Power BI 作为流分析作业输出的详细信息，请单击[此处](../../stream-analytics/power-bi-output.md)。 了解有关为 Power BI 帐户[续订身份验证](../../stream-analytics/stream-analytics-power-bi-dashboard.md#renew-authorization)的详细信息。

### <a name="configure-the-query-for-stream-analytics-job"></a>为流分析作业配置查询

1. 在“作业拓扑”下选择“查询”。
2. 对查询进行以下更改：

   ```SQL
   SELECT
       CAST(InferenceRecords.ArrayValue.event.properties.total AS bigint) as EventTotal,
       CAST(i.EventProcessedUtcTime AS datetime) as EventProcessedUtcTime
   INTO [YourOutputAlias]
   FROM [YourInputAlias] i
   CROSS APPLY GetArrayElements(inferences) AS InferenceRecords
   WHERE InferenceRecords.ArrayValue.subType = 'lineCrossing'
   ```

   > [!NOTE]
   >
   > - 在以上查询中，FROM 子句中的 i 在语法上是必需的，用于提取不在 inferences 数组中嵌套的 EventProcessedUtcTime 值 。
   >   以上查询经过自定义，可为[越线教程](use-line-crossing.md)获取 AI 推理。
   > - 如果在运行另一个管道，请确保根据该管道的 AI 推理架构自定义查询。 了解有关[在流分析作业中分析 JSON](../../stream-analytics/stream-analytics-parsing-json.md) 的详细信息。

3. 将 [YourOutputAlias] 替换为“将输出添加到流分析作业”一步中使用的输出别名，例如“powerbioutput”。 请注意输出和输入别名的正确序列。
4. 将 [YourInputAlias] 替换为“将输入添加到流分析作业”一步中使用的输入别名，例如“iothubinput”。
5. 查询应类似于以下屏幕截图。 单击“测试查询”，以采用 EventTotal 和对应 EventProcessedUtcTime 值的表的形式验证测试结果。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/asa-query.png" alt-text="在流分析作业中测试和保存查询的屏幕截图。":::

6. 选择“保存查询”。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中选择“概述”，然后选择“启动” > “现在” > “启动”。 成功启动作业后，作业状态将从“已创建”更改为“正在运行”。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/start-asa.png" alt-text="启动和运行流分析作业的屏幕截图。":::

> [!TIP]
> IoT 中心允许内置事件中心的数据保留期在默认情况下为 1 天，最长为 7 天。 可以在创建 IoT 中心时设置保留时间。 IoT 中心的数据保留时间取决于所选层级和单位类型。 单击[此处](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)了解更多信息。 若要延长数据保留期，请使用 [Azure 存储作为输出](../../stream-analytics/blob-storage-azure-data-lake-gen2-output.md)，然后将 Power BI 连接到存储帐户中的文件。

## <a name="run-a-sample-pipeline"></a>运行示例管道

如果上面步骤中创建的流分析作业处于“正在运行”状态，请转到越线教程的[运行示例程序](use-line-crossing.md#run-the-sample-program)部分并激活实时管道。 实时管道会开始将 AI 推理结果发送到 IoT 中心，流分析作业随后会选取这些结果。

## <a name="create-a-power-bi-dashboard-to-visualize-ai-events"></a>创建 Power BI 仪表板来可视化 AI 事件

在 Power BI 中，可以通过 2 种方式可视化流式处理数据：

1. 通过为流式处理数据集创建的表创建报表，并固定到仪表板
2. 具有自定义流式处理数据集的仪表板磁贴

   > [!NOTE]
   > 在本文中，我们会使用第一种方法创建报表，然后固定到仪表板。 此方法会将视觉对象上的数据保留更长持续时间，并基于传入数据自动聚合。 若要了解有关第二种方法的详细信息，请参阅[在 Power BI 中设置自定义流式处理数据集](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi)。

### <a name="create-and-publish-a-power-bi-report"></a>创建和发布 Power BI 报表

以下步骤说明如何使用 Power BI 服务创建和发布报表。

1. 请确保在设备（在上一步中启动以运行管道）上激活了示例管道。
2. 登录 [Power BI 帐户](https://powerbi.microsoft.com/)，选择顶部菜单中的“Power BI 服务”。
3. 在边侧菜单中选择你使用的工作区。
4. 在“全部”选项卡或“数据集 + 数据流”选项卡中，应该会看到在“将输出添加到流分析作业”一步中指定的数据集 。
5. 将鼠标悬停在创建流分析输出时所提供的数据集名称上，选择“更多选项”菜单（数据集名称右侧三个点），然后选择“创建报表” 。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-report.png" alt-text="在 Power BI 中创建报表的屏幕截图。":::

6. 创建折线图以实时显示越线事件。

   - 在报表创建页的“可视化”窗格中，选择折线图图标以添加折线图 。
   - 在“字段”窗格中展开一个表，该表是在为流分析作业创建输出时指定的。 
   - 将 EventProcessedUtcTime 拖至“可视化效果”窗格中的“轴”  。
   - 将 EventTotal 拖到“值”中 。
   - 已创建一个折线图。 X 轴显示 UTC 时区的日期和时间。 y 轴显示实时管道中 EventTotal 总和的自动汇总值。 将“值”更改为 EventTotal 的“最大值”可获取 EventTotal 的最新值。 可以更改聚合函数以显示“平均值”、“计数(不重复)”等。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/powerbi-report.png" alt-text="Power BI 中越线报表的屏幕截图。":::

7. 单击右上方的“保存”以保存报表。

### <a name="pin-visual-to-a-dashboard"></a>将视觉对象固定到仪表板

单击右上方的“固定到仪表板”，并选择要将此视觉对象固定到的位置（在现有仪表板或新仪表板中），然后相应地按照提示进行操作。

（可选）还可以[嵌入播放器小组件](embed-player-in-power-bi.md)，以便与报表并排查看视频播放。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/pinned-dashboard.png" alt-text="Power BI 仪表板，其中固定了报表并且在磁贴中添加了播放器小组件。":::

> [!NOTE]
> 由于用于生成报表的方法，视频播放和报表创建不会同步。 有关更接近实时的体验，请参阅[在 Power BI 中设置自定义流式处理数据集](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi)。

## <a name="interpret-the-dashboard"></a>解释仪表板

越线处理器节点使用 lineCoordinates 参数检测对象何时越过拓扑中指定的线。 对象越过这些坐标时，将触发事件并显示：

- EventTotal：自视频开始以来，任何对象在任何方向（顺时针或逆时针）上越线的总次数。 了解有关[越线事件推理](use-line-crossing.md#line-crossing-events)的详细信息。
- 事件处理 UTC 时间：流分析作业处理每个事件的时间。 或者，可以自定义流分析查询以提取 EventEnqueuedUtcTime，该值表示事件到达 IoT 中心准备进一步处理的时间。

在以上仪表板中，你会看到随着时间推移，EventTotal 的数量不断增加，因为越来越多的对象越过虚拟线。 例如通过此可视化效果，可以快速识别出车辆经过高速公路的频率在下午 3:52:00 与下午 3:53:30 之间较高。 随后可以使用这些见解缩小对高速公路上特定时间交通拥堵原因的分析范围。

## <a name="clean-up-resources"></a>清理资源

如果你要学习其他快速入门或教程，请保留创建的资源。 否则，请转到 Azure 门户，转到资源组，选择运行本文的先决条件并创建流分析作业所用的资源组，然后选择“删除资源组”。

你在 Power BI 中创建了一个数据集 LineCrossingDataset。 要删除该数据集，请登录到你的 [Power BI](https://powerbi.microsoft.com/) 帐户。 在左侧菜单的“工作区”下，选择你使用的工作区。 在“数据集 + 数据流”选项卡下的数据集列表中，将鼠标悬停在该数据集上。 选择数据集名称右侧出现的三个垂直点以打开“更多选项”菜单，然后选择“删除”并按照提示操作 。 删除数据集时，报表也随之删除。

## <a name="next-steps"></a>后续步骤

- 通过[在 Power BI 中嵌入播放器小组件](embed-player-in-power-bi.md)，将视频播放与遥测相结合
- 了解有关[监视和日志记录事件](monitor-log-edge.md)的详细信息
