---
title: 浏览特选的可视化效果 - Azure IoT Edge
description: 使用 Azure 工作簿可视化和浏览 IoT Edge 内置指标
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7be7260e993f9cc95b542406767d6794f18836f8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015655"
---
# <a name="explore-curated-visualizations-preview"></a>浏览特选的可视化效果（预览版）

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

可以直接在 Azure 门户中使用 Azure Monitor 工作簿来可视化和浏览从 IoT Edge 设备收集的指标。 适用于 IoT Edge 设备的特选监视工作簿以公共模板的形式提供：

* 对于连接到 IoT 中心的设备，请从 Azure 门户中的“IoT 中心”边栏选项卡导航到“监视”部分中的“工作簿”页  。
* 对于连接到 IoT Central 的设备，请从 Azure 门户中的“IoT Central”边栏选项卡导航到“监视”部分中的“工作簿”页  。

特选工作簿使用来自 IoT Edge 运行时的[内置指标](how-to-access-built-in-metrics.md)。 这些视图不需要工作负载模块提供的任何指标检测结果。

## <a name="access-curated-workbooks"></a>访问特选工作簿

适用于 IoT 的 Azure Monitor 工作簿是一组模板，可用于立即开始可视化指标，你也可以根据你的解决方案对其进行自定义。

要访问特选工作簿，请使用以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心或 IoT Central 应用程序。

1. 在菜单的“监视”部分选择“工作簿” 。

1. 在公共模板列表中选择要浏览的工作簿：

  * IoT Edge 群视图：监视设备群，并深入查看特定设备以获取运行状况快照。
  * IoT Edge 设备详细信息：可视化有关 IoT Edge 设备上的消息传送、模块和主机组件的设备详细信息。
  * IoT Edge 运行状况快照：基于六个常用性能指标查看设备的运行状况。 若要访问运行状况快照工作簿，请从群视图工作簿开始，选择要查看的特定设备。 群视图工作簿会将某些必需的参数传递到运行状况快照视图。

你可以自行浏览工作簿，或使用以下部分来预览每个工作簿提供的数据和可视化效果类型。

## <a name="iot-edge-fleet-view-workbook"></a>IoT Edge 群视图工作簿

群视图工作簿提供两个可用的视图：

* “设备”视图显示活动设备的概述。
* “警报”视图显示通过[预配置的警报规则](how-to-create-alerts.md)生成的警报。

可以使用工作簿顶部的选项卡在视图之间切换。

# <a name="devices"></a>[设备](#tab/devices)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif" alt-text="群视图工作簿的“设备”部分。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif":::

在“设备”视图中查看发送指标的活动设备的概述。 此视图显示与当前 IoT 中心或 IoT Central 应用程序相关联的设备。

右侧有一个设备列表，其中显示了发送的本地消息和上游消息的组合条。 可以按设备名称筛选该列表，然后单击设备名称链接以查看其详细指标。

左侧的蜂巢可视化效果显示了哪些设备正常或不正常。 其中还显示了设备上次发送指标的时间。 有 30 分钟以上未发送指标的设备以蓝色显示。 单击蜂巢中的设备名称可查看该设备的运行状况快照。 确定运行状况时，只会考虑来自设备的最后 3 个度量值。 仅使用最近的数据能够解释报告的指标出现临时性峰值的原因。

# <a name="alerts"></a>[警报](#tab/alerts)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif" alt-text="群视图工作簿的“警报”部分。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif":::

在“警报”视图中查看通过[预先创建的警报规则](how-to-create-alerts.md)生成的警报。 通过此视图可以查看来自多个 IoT 中心或 IoT Central 应用程序的警报。

左侧提供了一个列表，其中包含警报严重性及其计数。 右侧的地图显示了每个区域的警报总数。

单击严重性行可查看警报详细信息。 单击“警报规则”链接会转到警报上下文，单击“设备”链接会打开详细指标工作簿 。 在此视图中打开设备详细信息工作簿时，该工作簿会自动调整到触发警报的大致时间范围。

---

## <a name="iot-edge-device-details-workbook"></a>IoT Edge 设备详细信息工作簿

设备详细信息工作簿提供三个可用的视图：

* “消息传送”视图可视化设备的消息路由，并报告消息传送系统的总体运行状况。
* “模块”视图显示设备上各个模块的运行情况。
* “主机”视图显示有关主机设备的信息，包括主机组件的版本信息和资源使用情况。

可以使用工作簿顶部的选项卡在视图之间切换。

设备详细信息工作簿还与基于 IoT Edge 门户的故障排除体验相集成，使你可以查看来自设备的实时日志。 可以通过选择工作簿上方的“对 \<device name> 进行实时故障排除”按钮来访问此体验。

# <a name="messaging"></a>[消息传送](#tab/messaging)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif" alt-text="设备详细信息工作簿的“消息传送”部分。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif":::

“消息传送”视图包括三个子部分：路由详细信息、路由图和消息传送运行状况。 在任意时间图表中拖动鼠标可将全局时间范围调整为所选范围。

“路由”部分显示发送方模块与接收方模块之间的消息流。 其中显示了消息计数、速率和连接的客户端数等信息。 单击发送方或接收方可以进一步深入查看信息。 单击某个发送方会显示该发送方经历的延迟趋势图表，及其发送的消息数。 单击某个接收方会显示该接收方的队列长度趋势，及其接收的消息数。

“图”部分显示模块之间的消息流的视觉表示形式。 可以通过拖动和缩放来调整图。

“运行状况”部分显示与消息传送子系统的总体运行状况相关的各项指标。 如果指出了任何错误，你可以逐步深入查看详细信息。

# <a name="modules"></a>[模块](#tab/modules)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif" alt-text="设备详细信息工作簿的“模块”部分。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif":::

“模块”视图显示从 edgeAgent 模块收集的指标，该模块报告设备上所有正在运行的模块的状态。 此视图包含如下所述的信息：

* 模块可用性
* 每个模块的 CPU 和内存使用率
* 所有模块的 CPU 和内存使用率
* 模块重启次数和重启时间线。

# <a name="host"></a>[主机](#tab/host)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif" alt-text="设备详细信息工作簿的“主机”部分。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif":::

“主机”视图显示来自 edgeAgent 模块的指标。 此视图包含如下所述的信息：

* 主机组件版本信息
* 运行时间
* 主机级别的 CPU、内存和磁盘空间使用率

# <a name="live-logs"></a>[实时日志](#tab/livelogs)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif" alt-text="通过设备详细信息工作簿访问实时日志。" lightbox="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif":::

此工作簿直接与基于门户的故障排除体验相集成。 单击“实时故障排除”按钮会转到故障排除屏幕。 在此处，可以轻松地按需查看从设备拉取的模块日志。 时间范围已自动设置为工作簿的时间范围，因此你会立即进入时态上下文。 还可以在此体验中重启任何模块。

---

## <a name="iot-edge-health-snapshot-workbook"></a>IoT Edge 运行状况快照工作簿

可以从群视图工作簿内部访问运行状况快照工作簿。 群视图工作簿将传入一些参数，这些参数是初始化运行状况快照视图所必需的。 在蜂巢中选择某个设备名称可查看该设备的运行状况快照。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png" alt-text="通过在群视图工作簿中选择设备来访问运行状况快照工作簿。" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png":::

运行状况快照由六个信号直接构成：

* 上游消息
* 本地消息
* 队列长度
* 磁盘使用
* 主机级 CPU 利用率
* 主机级内存利用率

这些信号是根据可配置的阈值度量的，用于确定设备是否正常。 可以调整阈值，或者可以通过编辑工作簿来添加新信号。 请参阅下一部分了解工作簿自定义。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif" alt-text="查看运行状况快照工作簿。" lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif":::

## <a name="customize-workbooks"></a>自定义工作簿

[Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)的可自定义程度很高。 可以根据你的需求编辑公共模板。 所有可视化效果由针对 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 表的以资源为中心的 [KQL](/azure/data-explorer/kusto/query/) 查询驱动。 请参阅以下有关编辑运行状况阈值的示例。

若要开始自定义工作簿，请先进入编辑模式。 在工作簿菜单栏中选择“编辑”按钮。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png" alt-text="进入工作簿编辑模式。" lightbox="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png":::

特选工作簿广泛使用工作簿组。 可能需要单击多个嵌套组对应的“编辑”才能查看可视化查询。

将所做的更改另存为新的工作簿。 可与团队[共享](../azure-monitor/visualize/workbooks-access-control.md)保存的工作簿，或者[以编程方式将工作簿部署](../azure-monitor/visualize/workbooks-automate.md)为组织资源部署的一部分。

例如，你可能想要更改将设备视为正常或不正常时所需达到的阈值。 为此，可以在群视图工作簿模板中不断深入钻取，直到看到 device-health-graph 查询项，其中包括了此工作簿比较设备时所依据的所有指标阈值。

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif" alt-text="持续打开嵌套组件的编辑模式，直到看到该查询项。" lightbox="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif":::

## <a name="next-steps"></a>后续步骤

使用[警报规则](how-to-create-alerts.md)和[自定义模块中的指标](how-to-add-custom-metrics.md)自定义监视解决方案。