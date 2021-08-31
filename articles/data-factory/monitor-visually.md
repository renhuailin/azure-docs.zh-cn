---
title: 以可视化方式监视 Azure 数据工厂
description: 了解如何直观监视 Azure 数据工厂
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 81649565955d1de031e4eefca548c5d58f7e28c6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730156"
---
# <a name="visually-monitor-azure-data-factory"></a>以可视化方式监视 Azure 数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 数据工厂中创建并发布管道以后，即可将其与触发器关联，或者手动启动临时运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行。 若要打开监视体验，请选择 [Azure 门户](https://portal.azure.com/)的“数据工厂”边栏选项卡中的“监视和管理”磁贴。 如果已在 ADF UX 中，请单击左侧边栏上的“监视”图标。

默认情况下，所有数据工厂运行都以浏览器的本地时区显示。 如果你更改时区，所有日期/时间字段都会与你选择的时区保持一致。

## <a name="monitor-pipeline-runs"></a>监视管道运行

默认监视视图是所选时段内触发的管道运行的列表。 你可以更改时间范围并按状态、管道名称或注释进行筛选。 将鼠标悬停在特定管道运行上可获取特定于运行的操作，如重新运行和消耗报告。

![用于监视管道运行的列表视图](media/monitor-visually/pipeline-runs.png)

管道运行网格包含以下列：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称 |
| 运行开始时间 | 管道运行的开始日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 运行结束时间 | 管道运行的结束日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 触发者 | 启动了管道的触发器的名称 |
| 状态 | **失败**、**成功**、**正在进行**、**已取消** 或 **已排队** |
| 批注 | 与管道关联的可筛选标记  |
| 参数 | 管道运行的参数（名称/值对） |
| 错误 | 在管道失败的情况下出现的运行错误 |
| 运行 ID | 管道运行的 ID |

需要手动选择“刷新”按钮来刷新管道和活动运行的列表。 当前不支持自动刷新。

![“刷新”按钮](media/monitor-visually/refresh.png)

若要查看调试运行的结果，请选择“调试”选项卡。

![选择查看活动调试运行图标](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>监视活动运行

若要获取某特定管道运行的单个活动运行的详细视图，请单击管道名称。

![查看活动运行](media/monitor-visually/view-activity-runs.png)

此列表视图显示了与每个管道运行对应的活动运行。 将鼠标悬停在该特定活动运行上可获取特定于运行的信息，如 JSON 输入、JSON 输出和详细的特定于活动的监视体验。

![存在有关 SalesAnalyticsMLPipeline 的信息，后跟活动运行的列表。](media/monitor-visually/activity-runs.png)

| **列名** | **说明** |
| --- | --- |
| 活动名称 | 管道中的活动的名称 |
| 活动类型 | 活动类型，如“复制”、“ExecuteDataFlow”或“AzureMLExecutePipeline” |
| 操作 | 图标，用于查看 JSON 输入信息、JSON 输出信息或详细的特定于活动的监视体验 | 
| 运行开始时间 | 活动运行的开始日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 状态 | **失败**、**成功**、**正在进行** 或 **已取消** |
| Integration Runtime | 运行活动时所在的 Integration Runtime |
| 用户属性 | 活动的用户定义属性 |
| 错误 | 在活动失败的情况下出现的运行错误 |
| 运行 ID | 活动运行的 ID |

如果某个活动失败，可通过单击错误列中的图标来查看详细的错误消息。 

![将出现一条包含错误详细信息的通知，其中包括错误代码、失败类型和错误详细信息。](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>提升要监视的用户属性

将任何管道活动属性提升为用户属性，使其成为受监视的实体。 例如，可以将管道中复制活动的“源”和“目标”属性提升为用户属性。

> [!NOTE]
> 最多只能将五个管道活动属性提升为用户属性。

![创建用户属性](media/monitor-visually/promote-user-properties.png)

创建用户属性后，便可在监视列表视图中监视它们。

![将用户属性的列添加到活动运行列表](media/monitor-visually/choose-user-properties.png)

 如果复制活动的源是表名，则可以将源表名称作为活动运行列表视图中的列进行监视。

![具有用户属性的列的活动运行列表](media/monitor-visually/view-user-properties.png)


## <a name="rerun-pipelines-and-activities"></a>重新运行管道和活动
 
 容器活动的重新运行行为如下所示：
 
- `Wait`- 活动将像以前一样运行。
- `Set Variable`- 活动将像以前一样运行。
- `Filter`- 活动将像以前一样运行。
- `Until` 活动会评估表达式，并会循环，直到条件得到满足。 可能仍会根据重新运行规则跳过内部活动。
- `Foreach` 活动将始终在其收到的项上循环。 可能仍会根据重新运行规则跳过内部活动。
- `If and switch` - 始终会对条件进行评估。 可能仍会根据重新运行规则跳过内部活动。
- `Execute pipeline activity` - 将触发子管道，但仍可能会根据重新运行规则跳过子管道中的所有活动。


若要从开始处重新运行之前已运行过的管道，请将鼠标悬停在该特定管道运行上，并选择“重新运行”。 如果选择多个管道，可以使用“重新运行”按钮来运行所有这些管道。

![重新运行管道](media/monitor-visually/rerun-pipeline.png)

如果需要从某个特定点开始重新运行，可从活动运行视图中执行此操作。 选择需要作为开始位置的活动，并选择“从活动重新运行”。 

![重新运行活动运行](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>从失败的活动重新运行

如果某个活动失败、超时或被取消，则可以通过选择“从失败的活动重新运行”来从该失败的活动重新运行管道。

![重新运行失败的活动](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>查看重新运行历史记录

可以查看列表视图中所有管道运行的重新运行历史记录。

![查看历史记录](media/monitor-visually/rerun-history-1.png)

还可以查看特定管道运行的重新运行历史记录。

![查看管道运行的历史记录](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>监视消耗

通过单击管道运行旁边的消耗图标，可以查看运行消耗的资源。 

![屏幕截图显示了可查看管道所用资源的位置。](media/monitor-visually/monitor-consumption-1.png)

单击此图标会打开相应管道运行所使用的资源的消耗报告。 

![监视消耗](media/monitor-visually/monitor-consumption-2.png)

可以将这些值插入到 [Azure 定价计算器](https://azure.microsoft.com/pricing/details/data-factory/)中，以估计管道运行的成本。 若要详细了解 Azure 数据工厂定价，请参阅[了解定价](pricing-concepts.md)。

> [!NOTE]
> 定价计算器返回的这些值是估计值。 它并不反映 Azure 数据工厂将向你收取的确切金额 

## <a name="gantt-views"></a>甘特视图

甘特图是一种可用于查看某个时间范围内的运行历史记录的视图。 切换到甘特视图后，将会看到所有管道运行都按名称分组，并显示为与运行所用时间长度相关的条形。 还可以按已在管道上创建的注释/标记进行分组。 在活动运行级别也可使用甘特视图。

![甘特图示例](media/monitor-visually/select-gantt.png)

条形长度表示管道持续时间。 还可以选择条形来查看更多详细信息。

![甘特图持续时间](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>警报

可在数据工厂中发出有关受支持指标的警报。 在“数据工厂监视”页上，依次选择“监视” > “警报和指标”，以开始操作。

![数据工厂监视页](media/monitor-visually/start-page.png)

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>创建警报

1.  选择“新建警报规则”，以新建警报。

    ![“新建警报规则”按钮](media/monitor-visually/new-alerts.png)

1.  指定规则名称，然后选择警报严重性。

    ![规则名称和严重性的框](media/monitor-visually/name-and-severity.png)

1.  选择警报条件。

    ![目标条件的框](media/monitor-visually/add-criteria-1.png)

    ![屏幕截图显示了选择一个指标来设置警报条件的位置。](media/monitor-visually/add-criteria-2.png)

    ![条件列表](media/monitor-visually/add-criteria-3.png)

    可以针对各种指标创建警报，包括 ADF 实体计数/大小、活动/管道/触发器运行、Integration Runtime (IR) CPU 利用率/内存/节点计数/队列，以及 SSIS 包执行和 SSIS IR 启动/停止操作的指标。

1.  配置警报逻辑。 可以针对所选的指标为所有管道和对应的活动创建警报。 还可以选择特定的活动类型、活动名称、管道名称或故障类型。

    ![用于配置警报逻辑的选项](media/monitor-visually/alert-logic.png)

1.  为警报配置电子邮件、短信、推送和语音通知。 为警报通知创建一个操作组，或选择现有的操作组。

    ![用于配置通知的选项](media/monitor-visually/configure-notification-1.png)

    ![用于添加通知的选项](media/monitor-visually/configure-notification-2.png)

1.  创建警报规则。

    ![用于创建警报规则的选项](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](./monitor-programmatically.md)一文。
