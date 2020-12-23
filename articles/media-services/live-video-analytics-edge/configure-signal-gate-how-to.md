---
title: 为基于事件的视频录制配置信号入口-Azure
description: 本文提供了有关如何在 media graph 中配置信号入口的指导。
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410787"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>为基于事件的视频录制配置信号入口

在媒体图中，当事件触发入口时， [信号入口处理器节点](media-graph-concept.md#signal-gate-processor) 允许将媒体从一个节点转发到另一个节点。 触发时，入口会打开，并允许媒体流过指定的持续时间。 如果没有触发入口的事件，入口会关闭，media 停止流动。 可以使用信号入口处理器来记录基于事件的视频。

本文介绍如何配置信号入口处理器。

## <a name="suggested-prereading"></a>建议的 prereading
-   [媒体图](media-graph-concept.md)
-   [基于事件的视频录制](event-based-video-recording-concept.md)


## <a name="problem"></a>问题
用户可能需要在事件触发入口之前或之后在特定时间开始记录。 用户在其系统中知道可接受的延迟。 因此，它们需要指定信号入口处理器的滞后时间。 它们还需要指定其录制的最短和最长持续时间，而不管接收了多少个新事件。
 
### <a name="use-case-scenario"></a>用例场景
假设你想要在每次打开大楼的前门时录制视频。 您希望记录： 

- 在门打开之前包含 *X* 秒。 
- 如果门未再次打开，则至少为 *Y* 秒。 
- 如果门重复打开，则最大为 *Z* 秒。 
 
你知道你的门传感器的延迟为 *K* 秒。 若要降低事件被忽略的几率延迟，你需要至少用 *K* 秒来接收事件。


## <a name="solution"></a>解决方案

若要解决此问题，请修改信号入口处理器参数。

若要配置信号入口处理器，请使用以下四个参数：
- 激活评估窗口
- 激活信号偏移
- 最小激活窗口
- 最大激活窗口

触发信号入口处理器时，它会保持打开状态，以达到最小激活时间。 激活事件从最早事件的时间戳开始，加上激活信号偏移量。 

如果信号入口处理器在打开时再次触发，则计时器将重置，并且入口保持打开状态至少为最小激活时间。 信号入口处理器的打开时间从不超过最大激活时间。 

事件 1) 事件1发生在另一个事件 (事件 2) 之前发生的事件，如果系统滞后，事件1在事件2之后出现，则可能会被忽略。 ( 如果事件1在到达事件2和激活评估窗口之间不会到达，则忽略事件1。 它不是通过信号入口处理器传递的。 

为每个事件设置相关 Id。 从初始事件设置这些 Id。 它们对于每个事件都是连续的。

> [!IMPORTANT]
> 媒体时间基于媒体中发生事件时的媒体时间戳。 到达信号入口的事件序列可能不反映到达媒体时间的事件的顺序。


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>参数，基于事件到达信号入口的物理时间

* **minimumActivationTime (记录的最短持续时间)** ：触发接收新事件后，信号入口处理器保持打开状态的最小秒数，除非 maximumActivationTime 中断。
* **maximumActivationTime (记录的最长持续时间)** ：触发接收新事件后，信号入口处理器保持打开状态的最大秒数，无论接收到哪些事件。
* **activationSignalOffset** ：激活信号入口处理器和视频录制开始之间等待的秒数。 通常，此值为负，因为它会在触发事件之前开始记录。
* **activationEvaluationWindow** ：从初始触发事件开始，在初始事件之前发生的事件的秒数，在这段时间内，在被忽略并被视为延迟之前，初始事件之前发生的事件必须到达信号入口处理器。

> [!NOTE]
> *延迟到达* 是指在 "激活评估" 窗口经过之后但在媒体时间内的初始事件之前到达的任何事件。

### <a name="limits-of-parameters"></a>参数的限制

* **activationEvaluationWindow** ：0秒到10秒
* **activationSignalOffset** ：-1 分钟到1分钟
* **minimumActivationTime** ：1秒到1小时
* **maximumActivationTime** ：1秒到1小时


在用例中，可以按如下所示设置参数：

* **activationEvaluationWindow** ： *K* 秒
* **activationSignalOffset** ： *-X* 秒
* **minimumActivationWindow** ： *Y* 秒
* **maximumActivationWindow** ： *Z* 秒


下面是一个示例，说明了 " **信号入口处理器** " 节点部分如何在 media graph 拓扑中查找以下参数值：
* **activationEvaluationWindow** ：1秒
* **activationSignalOffset** ：-5 秒
* **minimumActivationTime** ：20秒
* **maximumActivationTime** ：40秒

> [!IMPORTANT]
> 每个参数值应有[ISO 8601 持续时间格式](https://en.wikipedia.org/wiki/ISO_8601#Durations
)。 例如，PT1S = 1 秒。


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
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


现在，请考虑此信号入口处理器配置在不同记录方案中的行为方式。

### <a name="recording-scenarios"></a>录制方案

**来自一个源的一个事件 ( *正常激活* )**

接收一个事件的信号入口处理器会生成一个记录，该记录在启动5秒 (激活信号 = 5 秒后) 事件到达入口。 记录的其余部分为20秒 (最小激活时间 = 20 秒) ，因为在重新触发的最小激活时间结束之前没有其他事件到达。

示例关系图：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="显示一个源事件的正常激活关系图。":::

* 记录的持续时间 =-偏移量 + minimumActivationTime = [E1 + offset，E1 + minimumActivationTime]


**一个源中的两个事件 ( *retriggered activation* )**

接收两个事件的信号入口处理器会生成一个记录，该记录在事件到达入口之前 (激活信号偏移量 = 5) 秒后开始。 而且，事件2在事件1后到达5秒。 由于事件2在事件1的最小激活时间结束之前到达 (20 秒) ，因此 retriggered。 记录的其余部分为20秒 (最小激活时间 = 20 秒) ，因为在从事件2到重新触发的最小激活时间结束之前，不会到达任何其他事件。

示例关系图：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="显示一个源中两个事件的 retriggered 激活的关系图。":::

* 记录的持续时间 =-发生事件2的偏移量 + (到达事件 1) + minimumActivationTime


**来自一个源的 *N* 个事件 ( *最大激活* )**

接收 *N* 个事件的信号入口处理器会生成一个记录，该记录在第一个事件到达入口之前 (激活信号偏移量 = 5) 秒后开始。 当每个事件在上一个事件的最小激活时间的最小激活时间结束之前，该门会持续 retriggered。 它保持打开状态，直到第一个事件的最长激活时间为40秒。 然后，入口关闭，不再接受任何新事件。

示例关系图：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="显示来自一个源的 N 个事件的最大激活关系图。":::
 
* 记录的持续时间 =-偏移量 + maximumActivationTime

> [!IMPORTANT]
> 前面的关系图假定每个事件在物理时间和媒体时间都到达同一时刻。 也就是说，它们假设没有延迟抵达。

## <a name="next-steps"></a>后续步骤

试用 [基于事件的视频录制教程](event-based-video-recording-tutorial.md)。 首先编辑上的 [topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)。 修改 "signalgateProcessor" 节点的参数，然后按照本教程的其余部分进行操作。 查看视频录制，分析参数的效果。



