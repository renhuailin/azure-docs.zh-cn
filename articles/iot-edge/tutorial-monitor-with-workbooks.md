---
title: 教程 - 适用于 IoT Edge 的 Azure Monitor 工作簿
description: 了解如何使用适用于 IoT 的 Azure Monitor 工作簿监视 IoT Edge 模块和设备
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/13/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6ba79aa63700a35e79d49febad8510e283b35441
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207115"
---
# <a name="tutorial-monitor-iot-edge-devices"></a>教程：监视 IoT Edge 设备

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

使用 Azure Monitor 工作簿监视 Azure IoT Edge 部署的运行状况和性能。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 了解 IoT Edge 设备共享哪些指标以及指标收集器模块如何对其进行处理。
> * 将指标收集器模块部署到 IoT Edge 设备。
> * 查看从设备收集的指标的策展可视化效果。

## <a name="prerequisites"></a>先决条件

部署了模拟温度传感器模块的 IoT Edge 设备。 如果没有准备好设备，请按照[将第一个 IoT Edge 模块部署到虚拟 Linux 设备](quickstart-linux.md)中的步骤，使用虚拟机创建一个。

## <a name="understand-iot-edge-metrics"></a>了解 IoT Edge 标记

每个 IoT Edge 设备都依赖于两个模块，即运行时模块，用于管理设备上所有其他模块的生命周期和通信。 这些模块称为“IoT Edge 代理”和“IoT Edge 中心” 。 若要详细了解这些模块，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

两个运行时模块均可创建指标，让你可远程监视 IoT Edge 设备或各个模块的性能情况。 IoT Edge 代理报告各个模块和主机设备的状态，因此会创建模块正常运行的时间、设备上使用的 RAM 量和 CPU 百分比等指标。 IoT Edge 中心报告设备上的通信情况，因此会创建发送和接收的消息总数、解析直接方法所需的时间等指标。 有关可用指标的完整列表，请参阅[访问内置指标](how-to-access-built-in-metrics.md)。

这两个模块会自动公开这些指标，以便你可以创建自己的解决方案来访问和报告这些指标。 为了简化此过程，Microsoft 提供了 [azureiotedge-metrics-collector](https://hub.docker.com/_/microsoft-azureiotedge-metrics-collector) 模块，用于为没有或不想要自定义解决方案的用户处理此过程。 指标收集器模块从两个运行时模块和可能需要监视的任何其他模块收集指标，并将这些指标传输到设备外。

指标收集器模块以两种方式之一将指标发送到云。 第一种方式将在本教程中使用，该方式将指标直接发送到 Log Analytics。 第二种方式（仅在网络策略需要时才建议使用）通过 IoT 中心发送指标，然后设置路由以将指标消息传递到 Log Analytics。 无论使用哪种方式，只要指标位于 Log Analytics 工作区中，就可以通过 Azure Monitor 工作簿进行查看。

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

Log Analytics 工作区是收集指标数据所必需的，并提供查询语言和与 Azure Monitor 的集成，以便监视设备。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 搜索并选择“Log Analytics 工作区”。

1. 选择“创建”，然后按提示创建新工作区。

1. 创建工作区后，选择“转到资源”。

1. 从主菜单的“设置”下，选择“代理管理” 。

1. 复制“工作区 ID”和“主密钥”的值 。 本教程稍后将使用这两个值来配置指标收集器模块，以将指标发送到此工作区。

## <a name="retrieve-your-iot-hub-resource-id"></a>检索 IoT 中心资源 ID

配置指标收集器模块时，为其提供 IoT 中心的 Azure 资源管理器资源 ID。 立即检索该 ID。

1. 在 Azure 门户，导航到 IoT 中心。

1. 从左侧菜单的“设置”下，选择“属性” 。

1. 复制“资源 ID”的值。 它应当采用 `/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Devices/IoTHubs/<iot_hub_name>` 格式。

## <a name="deploy-the-metrics-collector-module"></a>部署指标收集器模块

将指标收集器模块部署到要监视的每台设备。 该模块像其他所有模块那样在设备上运行，并监视其分配的终结点，以收集指标并将其发送到云。

按照以下步骤部署和配置收集器模块：

1. 登录 [Azure 门户](https://portal.azure.com)并转到 IoT 中心。

1. 从左侧菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 从 IoT Edge 设备列表中选择目标设备的设备 ID，打开设备详细信息页面。

1. 在上方的菜单栏上，选择“设置模块”，打开包含三步的模块部署页面。

1. 从门户部署模块的第一步是声明设备上应该有哪些模块。 如果使用的是在快速入门中创建的同一设备，则应该可以看到列出的“SimulatedTemperatureSensor”。 如果未出现，请立即添加：

   1. 选择“添加”，然后从下拉菜单中选择“市场模块” 。

   1. 搜索并选择“SimulatedTemperatureSensor”。

1. 添加和配置指标收集器模块：

   1. 选择“添加”，然后从下拉菜单中选择“市场模块” 。
   1. 搜索并选择“IoT Edge 指标收集器”。
   1. 从模块列表中选择指标收集器模块，打开其配置详细信息页面。
   1. 导航到“环境变量”选项卡。
   1. 请更新以下值：

      | 名称 | 值 |
      | ---- | ----- |
      | **ResourceId** | 在上一部分检索到的 IoT 中心资源 ID。 |
      | **UploadTarget** | `AzureMonitor` |
      | **LogAnalyticsWorkspaceId** | 在上一部分检索到的 Log Analytics 工作区 ID。 |
      | **LogAnalyticsSharedKey** | 在上一部分检索到的 Log Analytics 密钥。 |

   1. 删除“OtherConfig”环境变量，该变量是将来可能需要添加的额外配置选项的占位符。 本教程并不需要。
   1. 选择“更新”以保存更改。

1. 选择“下一步: 路由”继续进行部署模块的第二步。

1. 门户会自动为指标收集器添加路由。 如果将收集器模块配置为通过 IoT 中心发送指标，会用到此路由，但在本教程中，我们将指标直接发送到 Log Analytics，因此不需要它。 删除“FromMetricsCollectorToUpstream”路由。

1. 选择“查看 + 创建”继续部署模块的最后一步。

1. 选择“创建”以完成部署。

完成模块部署后，返回到设备详细信息页面，可以在其中看到四个列为“在部署中指定”的模块。 可能需要一些时间才能将所有四个模块都列为“由设备报告”，这意味着它们已成功启动并将其状态报告给 IoT 中心。 刷新页面以查看最新状态。

## <a name="monitor-device-health"></a>监视设备运行状况

设备监视工作簿可能需要 15 分钟才可供查看。 部署指标收集器模块后，该模块会开始将指标消息发送到 Log Analytics，并在该处整理到一个表中。 你提供的 IoT 中心资源 ID 将引入的指标链接到它们所属的中心。 因此，策展 IoT Edge 工作簿可使用资源 ID 查询指标表，从而检索指标。

Azure Monitor 为 IoT 提供了三个默认工作簿模板：

* “IoT Edge 队列视图”工作簿显示活动设备的的概述，以便你可以识别所有运行不正常的设备并深入了解每台设备的运行情况。 此工作簿还会显示通过你可能创建的任何警报规则生成的警报。
* “IoT Edge 设备详细信息”工作簿提供围绕三种类别的可视化效果：消息传递、模块和主机。 消息视图可直观呈现设备的消息路由，并报告消息系统的整体运行状况。 模块视图显示了设备上各个模块的运行情况。 主机视图显示有关主机设备的信息，包括主机组件的版本信息和资源使用情况。
* “IoT Edge 运行状况快照”工作簿针对可配置的阈值测量设备信号，以确定设备的运行状况是否良好。 只能从队列视图工作簿中访问此工作簿，该工作簿传递初始化特定设备的运行状况快照所需的参数。

### <a name="explore-the-fleet-view-and-health-snapshot-workbooks"></a>浏览队列视图和运行状况快照工作簿

队列视图工作簿会显示你的所有设备，并可以选择特定设备以查看其运行状况快照。 使用以下步骤浏览工作簿可视化效果：

1. 返回到 Azure 门户中的 IoT 中心页面。

1. 向下滚动主菜单，找到“监视”部分，然后选择“工作簿” 。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/workbooks-gallery.png" alt-text="选择工作簿以打开 Azure Monitor 工作簿库。":::

1. 选择“IoT Edge 队列视图”工作簿。

1. 你应该会看到运行指标收集器模块的设备。 该设备列为“运行正常”或“运行不正常” 。

1. 选择设备名称以打开 IoT Edge 运行状况快照，并查看有关设备运行状况的特定详细信息。

1. 在任何时间图表上，使用 X 轴下方的箭头图标，或单击图表并拖动光标以更改时间范围。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/health-snapshot-custom-time-range.png" alt-text="单击并拖动或使用任意图表上的箭头图标来更改时间范围。":::

1. 关闭运行状况快照工作簿。 从队列视图工作簿中选择“工作簿”，返回到工作簿库。

### <a name="explore-the-device-details-workbook"></a>浏览设备详细信息工作簿

设备详细信息工作簿显示单个设备的性能详细信息。 使用以下步骤浏览工作簿可视化效果：

1. 从工作簿库中，选择“IoT Edge 设备详细信息”工作簿。

1. 设备详细信息工作簿中显示的第一页是已选中“路由”选项卡的“消息”视图 。

   在左侧，表中会显示设备上的路由（按终结点整理）。 对于我们的设备，我们会看到“上游终结点”（用于指代路由到 IoT 中心的特殊术语）正在接收来自模拟温度传感器模块的“temperatureOutput”输出的消息 。

   在右侧，图形中会随时间跟踪连接的客户端数量。 可以单击并拖动图形以更改时间范围。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-routing.png" alt-text="选择消息视图以查看设备上的通信状态。":::

1. 选择“图形”选项卡以查看路由的不同可视化效果。 在图形页上，可以拖放不同的终结点来重新排列图形。 要直观呈现的路由很多时，此功能非常有用。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-graph.png" alt-text="选择图形视图以查看设备路由的交互图。":::

1. “运行状况”选项卡会报告消息传递的任何问题，例如删除的消息或断开连接的客户端。

1. 选择“模块”视图以查看部署在设备上的所有模块的状态。 可以选择每个模块，详细了解它们使用的 CPU 和内存量。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-modules-availability.png" alt-text="选择模块视图以查看部署到设备的每个模块的状态。":::

1. 选择“主机”视图以查看有关主机设备的信息，包括其操作系统、IoT Edge 守护程序版本和资源使用情况。

## <a name="view-module-logs"></a>查看模块日志

查看设备的指标后，你可能希望进一步了解并检查各个模块。 IoT Edge 通过实时模块日志功能在 Azure 门户中提供故障排除支持。

1. 从设备详细信息工作簿中，选择“实时故障排除”。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-troubleshoot-live.png" alt-text="选择设备详细信息工作簿右上方的“实时故障排除”按钮。":::

1. 故障排除页面随即打开，显示 IoT Edge 设备中的“edgeAgent”日志。 如果在设备详细信息工作簿中选择了特定的时间范围，该设置会传递到故障排除页。

1. 使用下拉菜单切换到设备上运行的其他模块的日志。 使用“重启”按钮可重启模块。

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/troubleshoot-device.png" alt-text="使用下拉菜单查看不同模块的日志，并使用重启按钮重启模块。":::

还可以从 IoT Edge 设备的详细信息页访问故障排除页面。 有关详细信息，请参阅[在 Azure 门户中对 IoT Edge 设备进行故障排除](troubleshoot-in-portal.md)。

## <a name="next-steps"></a>后续步骤

在继续学习教程的剩余内容时，请将指标收集器模块保留在设备上，并返回到这些工作簿，了解信息在添加更复杂的模块和路由时的变化情况。

继续学习下一教程，在该教程中设置开发人员环境，开始向设备部署自定义模块。

> [!div class="nextstepaction"]
> [使用 Linux 容器开发 IoT Edge 模块](tutorial-develop-for-linux.md)
