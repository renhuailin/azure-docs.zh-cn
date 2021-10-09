---
title: 使数据管道可操作化
description: 了解如何为数据管道提供服务级别协议
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 09/22/2021
ms.openlocfilehash: d80d2abf4d51d99fb207ec728e29b362079ba64a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207538"
---
# <a name="deliver-service-level-agreement-for-data-pipelines"></a>为数据管道提供服务级别协议

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂受到世界各地公司的喜爱和信任。 作为 Azure 用于横向扩展无服务器数据集成和数据转换的原生云 ETL 服务，它被广泛用于实现数据管道，以准备、处理数据并将其加载到企业数据仓库或数据湖中。

通过 Git 模式下的[持续集成和交付 (CI/CD)](continuous-integration-delivery.md) 或者直接在实时模式下发布数据管道后，它们通常在 Autopilot 上运行。 它们可以使用[计划触发器](how-to-create-schedule-trigger.md)或[翻转窗口触发器](how-to-create-tumbling-window-trigger.md)按预定义的时间表运行，或者使用[存储事件触发器](how-to-create-event-trigger.md)或[自定义事件触发器](how-to-create-custom-event-trigger.md)在事件驱动的体系结构中运行。 它们受托处理任务关键型工作负载，为业务报告或数据分析和机器学习项目准备数据。

## <a name="preemptive-warnings-for-long-running-jobs"></a>针对长时间运行的作业的抢先警告

为这些数据管道提供服务级别协议 (SLA) 有两个主要挑战：

* 活动的计算环境（例如用于存储过程活动的 SQL）可能会受到限制，从而减慢整个数据管道并破坏管道 SLA。
* 管道开发人员并不总是主动监视工厂并主动寻找不符合 SLA 的长时间运行的管道。

为了解决这些问题，如果配置正确，管道运行会在不符合 SLA 的情况下发出“管道运行已用时间”指标。 结合[数据工厂警报](monitor-metrics-alerts.md#data-factory-alerts)，数据管道开发人员能够更好地向客户提供 SLA：你告诉我们管道应该运行多长时间，当管道运行时间超过预期时，我们会主动通知你。

对于要在其上创建警报的每个管道，在创作阶段，通过单击管道画布中的空白区域转到管道设置。

:::image type="content" source="media/tutorial-operationalize-pipelines/elapsed-time-1-set-up.png" alt-text="屏幕截图显示如何指定管道的预期运行持续时间。":::

在“设置”选项卡下，选中“已用时间指标”，并指定预期的管道运行持续时间，格式为 `D.HH:MM:SS`。 强烈建议将它设置为业务 SLA，即管道满足你的业务需求所需的时间。 一旦管道持续时间超过此设置，数据工厂就会在 Azure Monitor 中记录“管道运行已用时间”指标（指标 ID：`PipelineElapsedTimeRuns`）。 换而言之，在长时间运行的管道最终完成之前，你会抢先收到有关该管道的通知。

我们知道，有些管道原本就要比其他管道花费更多的时间来完成，因为它们有更多步骤或移动更多数据。 对于长时间运行的管道，没有任何一刀切的定义。 我们希望你为需要 SLA 的每个管道定义阈值。 在记录特定管道的指标时，我们将与用户定义的预期运行持续时间设置进行比较。

> [!NOTE]
> 这是一个按管道选择启用的功能。 如果没有为上述管道指定预期运行持续时间，则永远不会为管道记录任何指标。

按照步骤为指标设置[数据工厂警报](monitor-metrics-alerts.md#data-factory-alerts)。 工程师将收到电子邮件或短信通知，以介入并采取措施来满足 SLA。

## <a name="next-steps"></a>后续步骤

[数据工厂指标和警报](monitor-metrics-alerts.md)

[直观地监视](monitor-visually.md#alerts)
