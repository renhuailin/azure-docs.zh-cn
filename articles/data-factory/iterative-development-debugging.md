---
title: 在 Azure 数据工厂中进行迭代开发和调试
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 ADF UX 中以迭代方式开发和调试数据工厂管道
ms.date: 04/21/2021
ms.topic: conceptual
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: 39cce38b9b00201ae6fc3e24eff9d5816b69ed69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637748"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>使用 Azure 数据工厂进行迭代开发和调试
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过 Azure 数据工厂，你可以在开发数据集成解决方案时以迭代方式开发和调试数据工厂管道。 这些功能使你能够在创建拉取请求或将其发布到数据工厂服务之前测试更改。 

有关此功能的 8 分钟简介和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>调试管道

当你使用管道画布进行创作时，可使用“调试”功能测试活动。 执行测试运行时，在选择“调试”之前，不需要将更改发布到数据工厂。  当希望确保更改在你更新数据工厂工作流之前按预期工作时，此功能很有帮助。

![管道画布上的调试功能](media/iterative-development-debugging/iterative-development-1.png)

管道运行时，你可在管道画布的“输出”选项卡中查看每个活动的结果。

在管道画布的“输出”  窗口中查看测试运行的结果。

![管道画布“输出”窗口](media/iterative-development-debugging/iterative-development-2.png)

在测试运行成功后，向管道中添加更多活动并继续以迭代方式进行调试。 还可以在测试运行正在执行时将其 **取消**。

> [!IMPORTANT]
> 选择”调试”  会实际运行管道。 例如，如果管道包含复制活动，则测试运行会将数据从源复制到目标。 因此，在调试时，建议在复制活动和其他活动中使用测试文件夹。 在调试管道后，切换到要在正常操作中使用的实际文件夹。

### <a name="setting-breakpoints"></a>设置断点

Azure 数据工厂还允许你一直调试管道，直到到达管道画布中的某个特定活动。 在活动上放置要测试到的断点，然后选择“调试”即可。 数据工厂会确保测试仅运行到管道画布上的断点活动。 如果不想测试整个管道，只想测试该管道内的一部分活动，则此“调试至”  功能非常有用。

![管道画布上的断点](media/iterative-development-debugging/iterative-development-3.png)

若要设置断点，请选择管道画布上的元素。 “调试至”  选项在元素的右上角显示为空心的红色圆圈。

![在所选元素上设置断点之前](media/iterative-development-debugging/iterative-development-4.png)

选择“调试至”选项后，它将变为实心的红色圆圈，以指示已启用断点  。

![在所选元素上设置断点之后](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>监视调试运行

运行管道调试运行时，结果将显示在管道画布的“输出”窗口中。 “输出”选项卡只包含在当前浏览器会话过程中出现的最新运行。 

![管道画布“输出”窗口](media/iterative-development-debugging/iterative-development-2.png)

若要查看调试运行的历史视图或查看所有活动调试运行的列表，你可以进入“监视器”体验。 

![选择查看活动调试运行图标](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure 数据工厂服务仅将调试运行历史记录保留 15 天。 

## <a name="debugging-mapping-data-flows"></a>调试映射数据流

通过映射数据流，可构建大规模运行的无代码数据转换逻辑。 生成逻辑时，可打开调试会话，使用实时 Spark 群集以交互方式处理数据。 若要了解详细信息，请阅读[映射数据流调试模式](concepts-data-flow-debug-mode.md)。

可在“监视器”体验中监视工厂中的活动数据流调试会话。

![查看数据流调试会话](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

数据流设计器中的数据预览和数据流的管道调试最适合与数据的小示例一起使用。 但是，如果需要对大量数据测试管道或数据流中的逻辑，请使用更多的内核和最少的常规用途计算来增加调试会话中使用的 Azure Integration Runtime 的大小。
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>调试包含数据流活动的管道

在使用数据流执行调试管道运行时，有两个选项可用于该计算。 你可使用现有调试群集，也可为数据流启动新的实时群集。

使用现有调试会话将大幅缩短数据流启动时间，这是因为群集已在运行，但建议不要对复杂或并行工作负载这样做，理由是一次运行多个作业时可能会失败。

如果使用活动运行时，则将使用在每个数据流活动的集成运行时中指定的设置创建一个新群集。 这样可隔离每个作业，它应该用于复杂的工作负载或性能测试。 你还可控制 Azure IR 中的 TTL，从而使用于调试的群集资源在该时间段内仍然可用，从而服务其他作业请求。

> [!NOTE]
> 如果你有一个管道，其数据流正在并行执行或需要使用大型数据集进行测试，请选择“使用活动运行时”，使数据工厂能够使用在数据流活动中选择的 Integration Runtime。 这使得数据流能在多个群集上执行，还可适应并行数据流执行。

![运行包含数据流的管道](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>后续步骤

测试所做的更改后，请使用[在 Azure 数据工厂中进行的持续集成和部署](continuous-integration-deployment.md)将其升级到更高级的环境。
