---
title: Azure 快速入门 - 在 Azure 门户中运行第一个 Batch 作业
description: 本快速入门介绍如何使用 Azure 门户创建 Batch 帐户、包含计算节点的池以及在池中运行基本任务的作业。
ms.date: 05/25/2021
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 573ec2ece283e1e9b754ef9fd9b19a7e1b4883a3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475994"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>快速入门：在 Azure 门户中运行第一个 Batch 作业

通过使用 Azure 门户创建 Batch 帐户、计算节点（虚拟机）池以及在池中运行任务的作业，开始使用 Azure Batch。

完成本快速入门后，你将了解 [Batch 服务的重要概念](batch-service-workflow-features.md)，并可使用更逼真的工作负载进行更大规模的 Batch 试用。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-batch-account"></a>创建批处理帐户

请按以下步骤创建一个用于测试的示例 Batch 帐户。 创建池和作业需要 Batch 帐户。 还可将 Azure 存储帐户与 Batch 帐户相关联。 虽然本快速入门不需要，但存储帐户可以用于为大多数现实世界的工作负荷部署应用程序和存储输入和输出数据。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。

1. 在搜索框中键入“Batch 服务”，然后选择“Batch 服务”。

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Azure 市场中的 Batch 服务的屏幕截图。":::

1. 选择“创建”。

1. 在“资源组”字段中，选择“新建”，然后输入资源组的名称 。

1. 为“帐户名称”输入值。 该名称在所选的 Azure “位置”中必须是唯一的。 它只能包含小写字母和数字，且必须介于 3 到 24 个字符之间。

1. 在“存储帐户”下单击“选择存储帐户”，然后选择一个现有存储帐户或新建一个 。

1. 保留其他设置不变。 选择“查看 + 创建”，然后选择“创建”，创建 Batch 帐户 。

出现“部署成功”消息后，转到你创建的 Batch 帐户。

## <a name="create-a-pool-of-compute-nodes"></a>创建计算节点池

有了 Batch 帐户以后，请创建一个示例池，其中包含用于测试的 Windows 计算节点。 此快速入门中的池包含两个运行 Windows Server 2019 映像的节点，该映像来自 Azure 市场。

1. 在 Batch 帐户中，选择“池” > “添加”。

1. 输入名为“mypool”的 **池 ID**。

1. 在操作系统中使用以下设置（可浏览其他选项）。
  
   |设置  |值  |
   |---------|---------|
   |**映像类型**|市场|
   |**发布者**     |microsoftwindowsserver|
   |**产品/服务**     |windowsserver|
   |**Sku**     |2019-datacenter-core-smalldisk|

1. 向下滚动，以便输入“节点大小”和“规模”设置。  就此快速示例来说，建议的节点大小在性能和成本之间达成了很好的平衡。
  
   |设置  |Value  |
   |---------|---------|
   |**节点定价层**     |Standard_A1_v2|
   |**目标专用节点**     |2|

1. 让其余设置保留默认值，然后选择“确定”以创建该池。

Batch 会立即创建池，但分配和启动计算节点则需要数分钟。 在此期间，池的“分配状态”为“调整大小”。 当池在调整大小时，可以继续操作，创建作业和任务。

数分钟后，分配状态更改为“稳定”，节点启动。 若要检查节点的状态，请选择该池，然后选择“节点”。 节点在状态为“空闲”时可以运行任务。

## <a name="create-a-job"></a>创建作业

有了池之后，请创建可在其中运行的作业。 Batch 作业是一个或多个任务的逻辑组。 作业包含任务的公用设置，例如优先级以及运行任务的池。 创建任务之前，作业都不会有任务。

1. 在 Batch 帐户视图中，选择“作业” > “添加”。

1. 输入名为“myjob”的 **作业 ID**。

1. 在“池”中，选择“mypool”。

1. 其余设置保留默认值，然后选择“确定”。

## <a name="create-tasks"></a>创建任务

现在，选择该作业以打开“任务”页。 这就是你要创建在作业中运行的示例任务的位置。 通常创建多个可在计算节点上运行的任务，由 Batch 进行排队和分发。 此示例创建两个相同的任务。 每个任务都运行一个命令行，用于显示计算节点上的 Batch 环境变量，然后等待 90 秒。

使用 Batch 时，可以在命令行中指定应用或脚本。 Batch 提供了多种将应用和脚本部署到计算节点的方式。

若要创建第一个任务，请执行以下操作：

1. 选择“添加”  。

1. 输入名为“mytask”的 **任务 ID**。

1. 在“命令行”中输入 `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`。 其余设置保留默认值，然后选择“提交”。

若要创建第二个任务，请重复上述步骤。 输入其他任务 ID（例如“mytask2”），但是使用相同的命令行。

创建任务后，Batch 会让其排队在池中运行。 可以使用节点来运行该任务后，任务就会运行。 在我们的示例中，如果第一个任务仍在一个节点上运行，Batch 将在池中的另一个节点上启动第二个任务。

## <a name="view-task-output"></a>查看任务输出

你创建的示例任务将在几分钟内完成。 若要查看已完成的任务的输出，请选择该任务，然后选择文件 `stdout.txt` 以查看该任务的标准输出。 输出内容与以下示例类似：

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="已完成任务的输出的屏幕截图。":::

内容显示在节点上设置的 Azure Batch 环境变量。 创建自己的 Batch 作业和任务时，可以在任务命令行中引用这些环境变量，并在命令行运行的应用和脚本中引用它们。

## <a name="clean-up-resources"></a>清理资源

若要继续学习 Batch 教程和示例，可以继续使用在本快速入门中创建的 Batch 帐户和关联的存储帐户。 Batch 帐户本身不收费。

只要有节点在运行，就会对池收费，即使没有计划作业。 不再需要池时，请将其删除。 在帐户视图中，选择“池”和池的名称。 然后选择“删除”。  删除池后，节点上的所有任务输出都会被删除。

若不再需要资源组、Batch 帐户以及所有相关资源，请将其删除。 为此，请选择 Batch 帐户所在的资源组，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

本快速入门创建了 Batch 帐户、Batch 池和 Batch 作业。 作业运行示例任务，你查看了在其中一个节点上产生的输出。 了解 Batch 服务的重要概念以后，即可使用更逼真的工作负荷进行更大规模的 Batch 试用。 若要详细了解 Azure Batch，请继续学习 Azure Batch 教程。

> [!div class="nextstepaction"]
> [Azure Batch 教程](./tutorial-parallel-dotnet.md)
