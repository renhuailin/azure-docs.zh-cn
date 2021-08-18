---
title: 为基于事件的视频录制配置信号入口 - Azure
description: 本文提供有关如何在管道中配置信号入口的指导。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: c0b38005010d2718235700f0ed13575e15119103
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604076"
---
# <a name="configuring-a-signal-gate-for-event-based-video-recording"></a>为基于事件的视频录制配置信号入口

在管道中，[信号入口处理器节点](pipeline.md#signal-gate-processor)可以在入口被事件触发时将媒体从一个节点转发到另一个节点。 当被触发时，信号门会打开，允许媒体在指定的持续时间内流过。 如果没有事件触发信号门，则信号门会关闭，媒体会停止流动。 可以使用信号门处理器进行基于事件的视频录制。

> [!NOTE]
> 信号门处理器节点后面必须紧跟视频接收器或文件接收器。

在本文中，你将了解如何配置信号门处理器。

## <a name="suggested-prereading"></a>建议的预读取

- [管道拓扑](pipeline.md)
- [基于事件的视频录制](event-based-video-recording-concept.md)

## <a name="problem"></a>问题

用户可能希望在信号门被事件触发之前或之后的某个特定时间开始录制。 用户知道他们系统内的可接受延迟。 因此，他们希望指定信号门处理器的延迟。 他们还希望指定其录制的最短和最长持续时间，而不管接收了多少个新事件。
 
### <a name="use-case-scenario"></a>用例场景

假设你想要在每次打开大楼的前门时录制视频。 你希望该录制： 

- 包括门打开之前的 X 秒。 
- 在门未被再次打开的情况下，包括最后的至少 Y 秒。 
- 在门重复打开的情况下，包括最后的至多 Z 秒。 
 
你知道你的门传感器的延迟为 K 秒。 为了减少事件被视为迟到而被忽略的几率，你希望等待至少 K 秒，以便事件到达。

## <a name="solution"></a>解决方案

若要解决此问题，请修改信号门处理器参数。

若要配置信号门处理器，请使用以下四个参数：

- 激活评估窗口
- 激活信号偏移量
- 最小激活窗口
- 最大激活窗口

信号门处理器被触发后，它会在最短激活时间内保持打开状态。 激活事件从最早事件的时间戳加上激活信号偏移量后的那个时间点开始。 

如果信号门处理器在打开时再次触发，则计时器会重置，信号门至少会在最小激活时间内保持打开状态。 信号门处理器保持打开状态的时间从不会超过最大激活时间。 

如果一个事件（事件 1）在另一个事件（事件 2）之前发生，但系统滞后，导致事件 1 在事件 2 之后到达信号门处理器，则事件 1 可能会被忽略，具体取决于媒体时间戳。 事件 2 到达后，如果事件 1 没有在激活评估窗口关闭之前到达，则会忽略事件 1。 系统不会让其通过信号门处理器。 

将为每个事件设置相关 ID。 这些 ID 是从初始事件设置的。 对于每个后续事件，它们会按顺序编号。

> [!IMPORTANT]
> 媒体时间基于媒体中发生事件时的媒体时间戳。 事件到达信号门的顺序可能没有反映事件按媒体时间到达的顺序。

### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>基于事件到达信号门的物理时间的参数

* **minimumActivationTime（录制的最短可能持续时间）** ：信号门处理器被触发以接收新事件后，在不被 maximumActivationTime 中断的情况下保持打开状态的最小秒数。
* **maximumActivationTime（录制的最长可能持续时间）** ：从初始事件起，信号门处理器在被触发以接收新事件（无论接收什么事件）后保持打开状态的最大秒数。
* **activationSignalOffset**：从激活信号门处理器到开始录制视频之间的秒数。 通常此值为负，因为是在触发事件之前开始录制。
* **activationEvaluationWindow**：从初始触发事件开始，在初始事件之前发生的事件必须在该秒数内（以媒体时间计）到达信号门处理器，才不会被忽略并被视为延迟到达。

> [!NOTE]
> “延迟到达”是指在激活评估窗口过去之后但在初始事件之前到达的任何事件（以媒体时间计）。

### <a name="limits-of-parameters"></a>参数限制

* **activationEvaluationWindow**：0 秒到 10 秒
* **activationSignalOffset**：-1 分钟到 1 分钟
* minimumActivationTime：10 秒到 1 小时
* maximumActivationTime：10 秒到 1 小时

在用例中，你将按以下要求设置参数：

* **activationEvaluationWindow**：K 秒
* **activationSignalOffset**：-X 秒
* **minimumActivationWindow**：Y 秒
* **maximumActivationWindow**：Z 秒

下面是一个示例，说明了“信号入口处理器”节点部分在管道拓扑中查找以下参数值的方式：

* **activationEvaluationWindow**：1 秒
* **activationSignalOffset**：-5 秒
* **minimumActivationTime**：20 秒
* **maximumActivationTime**：40 秒

> [!IMPORTANT]
> 每个参数值都应使用 [ISO 8601 持续时间格式](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 例如，PT1S = 1 秒。

```
"processors":              
[
          {
            "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```

现在，请考虑此信号门处理器配置在不同录制场景中的表现情况。

### <a name="recording-scenarios"></a>录制场景

**一个事件来自一个源（正常激活）**

如果信号门处理器接收一个事件，则会在事件到达信号门之前提前 5 秒（激活信号 = 5秒）开始录制。 剩余的录制为 20 秒（最小激活时间 = 20 秒），因为在最小激活时间结束之前没有其他事件到达来重新触发信号门。

示例示意图：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/normal-activation.svg" alt-text="示意图显示了正常激活，即一个事件来自一个源。":::

* 录制持续时间 = -偏移量 + minimumActivationTime = [E1+偏移量, E1+minimumActivationTime]

**两个事件来自一个源（重新触发的激活）**

如果信号门处理器接收两个事件，则会在事件到达信号门之前提前 5 秒（激活信号偏移量 = 5秒）开始录制。 另外，事件 2 在事件 1 之后 5 秒到达。 因为事件 2 在事件 1 的最小激活时间（20 秒）结束之前到达，所以信号门会重新触发。 剩余的录制为 20 秒（最小激活时间 = 20 秒），因为在事件 2 的最小激活时间结束之前没有其他事件到达来重新触发信号门。

示例示意图：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/retriggering-activation.svg" alt-text="示意图显示了重新触发的激活，即两个事件来自一个源。":::

* 录制持续时间 = -偏移量 +（事件 2 到达时间 - 事件 1 到达时间）+ minimumActivationTime

**N 个事件来自一个源（最大激活）**

如果信号门处理器接收 N 个事件，则会在第一个事件到达信号门之前提前 5 秒（激活信号偏移量 = 5秒）开始录制。 当每个事件在上一个事件 20 秒的最小激活时间结束之前到达时，信号门会被连续重新触发。 它会保持打开状态，直到第一个事件后经过 40 秒的最大激活时间。 然后，信号门会关闭，不再接受任何新事件。

示例示意图：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/maximum-activation.svg" alt-text="示意图显示了最大激活，即 N 个事件来自一个源。":::
 
* 录制持续时间 = -偏移量 + maximumActivationTime

> [!IMPORTANT]
> 前面的示意图假定每个事件到达的物理时间和媒体时间相同。 也就是说，假设它们没有延迟到达。

### <a name="naming-video-or-files"></a>命名视频或文件

管道允许将视频录制到云，或者在边缘设备上录制为 MP4 文件。 这些文件可以由[连续视频录制](use-continuous-video-recording.md)或[基于事件的视频录制](record-event-based-live-video.md)生成。

建议用于录制到云的命名结构是将视频资源命名为“<anytext>-${System.TopologyName}-${System.PipelineName}”。 给定的实时管道只能连接到一个支持 RTSP 的 IP 相机，并且应将该相机的输入录制到一个视频资源。 例如，可以在视频接收器上设置 `VideoName`，如下所示：

```
"VideoName": "sampleVideo-${System.TopologyName}-${System.PipelineName}"
```
请注意，替换模式由 `$` 符号后跟大括号定义：${variableName}。

使用基于事件的录制在边缘设备上录制 MP4 文件时，可以使用：

```
"fileNamePattern": "sampleFilesFromEVR-${System.TopologyName}-${System.PipelineName}-${fileSinkOutputName}-${System.Runtime.DateTime}"
```

> [!Note]
> 在上面的示例中，变量 fileSinkOutputName 是在创建实时管道时定义的示例变量名称。 这不是系统变量。 请注意，使用 DateTime 如何确保每个事件都具有唯一的 MP4 文件名称。

#### <a name="system-variables"></a>系统变量

可以使用的一些系统定义的变量是：

| 系统变量        | 说明                                                  | 示例              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | 采用 ISO8601 文件标准格式的 UTC 日期时间（基本表示形式为 YYYYMMDDThhmmss）。 | 20200222T173200Z     |
| System.Runtime.PreciseDateTime | 采用 ISO8601 文件标准格式的包含毫秒的 UTC 日期时间（基本表示形式为 YYYYMMDDThhmmss.sss）。 | 20200222T173200.123Z |
| System.TopologyName    | 用户提供的执行管道拓扑的名称。          | IngestAndRecord      |
| System.PipelineName    | 用户提供的执行实时管道的名称。          | camera001            |

> [!Tip]
> 在云中命名视频时，不能使用 System.Runtime.PreciseDateTime 和 System.Runtime.DateTime。

## <a name="next-steps"></a>后续步骤

尝试使用[基于事件的视频录制教程](record-event-based-live-video.md)。 从编辑 [topology.json](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) 开始。 修改 signalgateProcessor 节点的参数，然后按照本教程其余部分的说明进行操作。 查看视频录制，分析参数的效果。
