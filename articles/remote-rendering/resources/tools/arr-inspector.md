---
title: ArrInspector 检测工具
description: ArrInspector 工具的用户手册
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97723819"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 检测工具

ArrInspector 是一个基于 Web 的工具，用于检查正在运行的 Azure 远程渲染会话。 它用于调试，可检查所渲染场景的结构、显示日志消息以及监视服务器的实时性能。

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>连接到 ArrInspector

获取 ARR 服务器的主机名（以 `mixedreality.azure.com` 结尾）后，使用 [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector) 进行连接。 此函数在运行应用程序的设备上创建一个 `StartArrInspector.html`。 若要启动 ArrInspector，请在电脑上使用某个浏览器（Edge、Firefox 或 Chrome）打开该文件。 该文件的有效期仅为 24 小时。

如果调用 `ConnectToArrInspectorAsync` 的应用已在电脑上运行：

* 如果你使用的是 Unity 集成，它可能会自动启动。
* 否则，你需要在“User Folders\\LocalAppData\\[your_app]\\AC\\Temp”中查找该文件。

如果应用在 HoloLens 上运行，请执行以下操作：

1. 使用 [Windows 设备门户](/windows/mixed-reality/using-the-windows-device-portal)访问 HoloLens。
1. 转到“系统”>“文件资源管理器”。
1. 导航到“User Folders\\LocalAppData\\[your_app]\\AC\\Temp”。
1. 将 StartArrInspector.html 保存到你的电脑。
1. 打开 StartArrInspector.html 以加载会话的 ArrInspector。

## <a name="the-performance-panel"></a>“性能”面板

![“性能”面板](./media/performance-panel.png)

此面板显示服务器公开的所有单帧性能值的图。 这些值当前包括帧时间、FPS、CPU 和内存使用量、内存统计信息（例如总体 RAM 使用量、对象计数，等等）。

若要可视化这些参数之一，请单击“添加新项”按钮并选择对话框中显示的可用值之一。 此操作会向面板中添加一个新的滚动图表，实时跟踪这些值。 在其右侧，你可以看到最小值、最大值和当前值。

你可以通过用鼠标拖动此图的内容来平移此图，但只有当 ArrInspector 处于暂停状态时才能进行水平平移。

按住 CTRL 的同时进行拖动可以实现缩放。 还可以用底部的滑块来控制水平缩放。

默认情况下，垂直范围是基于当前显示的值计算的，最小值和最大值显示在右侧的文本框中。 手动设置值时，无论是通过直接在文本框中键入值，还是通过平移/缩放，此图都会使用这些值。 若要还原自动垂直框架，请单击右上角的图标。

![垂直范围](./media/vertical-range.png)

## <a name="the-log-panel"></a>“日志”面板

![“日志”面板](./media/log-panel.png)

“日志”面板显示服务器端生成的日志消息列表。 在连接时，它最多显示 200 条以前的日志消息，并会在出现新的日志消息时输出这些新消息。

你可以使用顶部的按钮基于日志类型 `[Error/Warning/Info/Debug]` 来筛选列表。
![日志筛选器按钮](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>“计时数据捕获”面板

![计时数据捕获](./media/timing-data-capture.png)

此面板用于从服务器捕获计时信息并下载该信息。 该文件使用 [Chrome 跟踪 JSON 格式](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)。 若要检查数据，请在 Chrome 中打开 URL `Chrome://tracing`，然后将下载的文件拖放到页面中。 计时数据持续收集在一个固定大小的环形缓冲区中。 写出时，捕获仅包含最新信息（也就是几秒钟到几分钟的信息）。

## <a name="the-scene-inspection-panel"></a>“场景检查”面板

![“场景检查”面板](./media/scene-inspection-panel.png)

此面板显示所渲染场景的结构。 对象层次结构位于左侧，所选对象的内容位于右侧。 该面板是只读的，将会进行实时更新。

## <a name="the-vm-debug-information-panel"></a>“VM 调试信息”面板

![“VM 调试信息”面板](./media/state-debugger-panel.png)

此面板提供了一些调试功能。

### <a name="restart-service"></a>重新启动服务

“重启服务”按钮会重启 ArrInspector 连接到的虚拟机上的运行时。 所连接的任何客户端都会断开连接，必须重新加载 ArrInspector 页面才能连接到重启的服务。

### <a name="collect-debug-information"></a>收集调试信息

“收集 VM 的调试信息”按钮会打开一个对话框，你可以通过该对话框触发 ARR 实例，以收集 VM 的调试信息：

![“VM 调试信息”对话框](./media/state-debugger-dialog.png)

调试信息有助于 Azure 远程渲染团队分析正在运行的 ARR 实例中发生的任何问题。 此对话框有一个文本字段，用于提供其他详细信息，例如重现问题的步骤。

单击“开始收集”按钮后，该对话框会关闭，收集过程将开始。 收集 VM 的信息可能需要几分钟的时间。

![VM 调试信息收集正在进行](./media/state-debugger-panel-in-progress.png)

收集完成后，你会在 ArrInspector 窗口中收到通知。 此通知包含标识此特定集合的 ID。 请务必保存此 ID，你需要将其传递给 Azure 远程渲染团队。

![VM 调试信息收集成功](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> 你无法下载或以其他方式访问 VM 调试信息。 只有 Azure 远程渲染团队有权访问所收集的数据。 你需要与我们联系并发送集合 ID，这样我们才能调查你看到的问题。

## <a name="pause-mode"></a>暂停模式

在右上角，有一个开关可用来暂停面板的实时更新。 使用此模式时，可以仔细检查某个特定状态。

![暂停模式](./media/pause-mode.png)

重新启用实时更新时，会重置所有面板。