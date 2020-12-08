---
title: 教程：使用 AutoML 进行机器学习模型训练
description: 关于在 Azure Synapse 中如何使用 AutoML 训练机器学习模型的教程。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463512"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>教程：在 Azure Synapse 中使用 AutoML（预览版）进行无代码机器学习模型训练

了解如何使用 [Azure 机器学习中的 AutoML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 训练的新机器学习模型轻松扩充 Spark 表中的数据。  Synapse 中的用户只需在 Azure Synapse 工作区中选择一个 Spark 表作为训练数据集，以便在无代码体验中构建机器学习模型。

本教程介绍以下操作：

> [!div class="checklist"]
> - 在使用 Azure ML 中的自动化 ML 的 Azure Synapse 工作室中使用无代码体验来训练机器学习模型。 你训练的模型类型取决于你要解决的问题。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 你必须是所用 ADLS Gen2 文件系统的存储 Blob 数据参与者。
- Azure Synapse Analytics 工作区中的 Spark 池。 有关详细信息，请参阅[在 Azure Synapse 中创建 Spark 池](../quickstart-create-sql-pool-studio.md)。
- Azure Synapse Analytics 工作区中的 Azure 机器学习链接服务。 有关详细信息，请参阅[在 Azure Synapse 中创建 Azure 机器学习链接服务](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>创建用于训练数据集的 Spark 表

本教程需要一个 Spark 表。 以下笔记本将创建 Spark 表。

1. 下载笔记本 [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. 将笔记本导入到 Azure Synapse Studio。
![导入笔记本](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. 选择要使用的 Spark 池，然后单击 `Run all`。 运行此笔记本将从打开的数据集获取纽约出租车数据，并保存到默认的 Spark 数据库中。
![全部运行](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. 笔记本运行完成后，将在默认的 Spark 数据库下创建一个新的 Spark 表。 转到数据中心并查找名为 `nyc_taxi` 的表。
![Spark 表](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>启动 AutoML 向导以训练模型

右键单击在上一步中创建的 Spark 表。 选择“机器学习”->“使用新模型进行扩充”打开向导。
![启动 AutoML 向导](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

配置面板将出现，要求你提供用于在 Azure 机器学习中创建 AutoML 试验运行的配置详细信息。 此运行将训练多个模型，其中成功运行的最佳模型将在 Azure ML 模型注册表中注册：

![配置运行步骤 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure ML 工作区**：创建 AutoML 试验运行需要 Azure ML 工作区。 还需要使用[链接服务](quickstart-integrate-azure-machine-learning.md)将 Azure Synapse 工作区与 Azure ML 工作区链接起来。 拥有了所有的先决条件后，就可以指定要用于此 AutoML 运行的 Azure ML 工作区。

- **试验名称**：指定试验名称。 提交 AutoML 运行时，需提供试验名称。 运行的信息存储在 Azure ML 工作区中的试验下。 默认情况下，这种体验将创建一个新试验，并生成建议的名称，但也可以提供现有试验的名称。

- **最佳模型**：指定 AutoML 运行中最佳模型的名称。 将此名称赋予最佳模型，并在这次运行后自动保存在 Azure ML 模型注册表中。 AutoML 运行将创建许多机器学习模型。 根据将在后面步骤中选择的主要指标，可以比较这些模型并选择最佳模型。

- **目标列**：这就是被训练来预测的模型。 选择要预测的列。

- **Spark 池**：要用于 AutoML 试验运行的 Spark 池。 将对指定的池执行计算。

- **Spark 配置详细信息**：除 Spark 池外，还可以选择提供会话配置详细信息。

在本教程中，我们选择数值列 `fareAmount` 作为目标列。

单击“继续”。

## <a name="choose-task-type"></a>选择任务类型

根据要回答的问题选择用于试验的机器学习模型类型。 由于我们选择了 `fareAmount` 作为目标列，并且它是一个数值，所以我们将选择“回归”。

单击“继续”以配置其他设置。

![任务类型选择](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>其他配置

如果选择“分类”或“回归”类型，则其他配置包括 ：

- **主要指标**：用于衡量模型运行情况的指标。 该指标将用于比较在 AutoML 运行中创建的不同模型，并确定哪个模型效果最佳。

- 训练作业时间(小时)：运行和训练模型的试验所需的最长时间（以小时为单位）。 请注意，还可以提供小于 1 的值。 例如，`0.5`。

- 最大并发迭代数：表示将并行执行的最大迭代数。

- **ONNX 模型兼容性**：如果启用，由 AutoML 训练的模型将转换为 ONNX 格式。 如果想要使用模型在 Azure Synapse SQL 池中进行评分，这一点尤其重要。

所有这些设置都具有可自定义的默认值。
![其他配置](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> 请注意，如果选择“时序预测”，则需要更多配置。 预测也不支持 ONNX 模型兼容性。

完成所有必需的配置后，即可启动 AutoML 运行。

可通过两种方法在 Azure Synapse 中启动 AutoML 运行。 若要获得无代码体验，可以直接选择“创建运行”。 如果更喜欢代码，可以选择“在笔记本中打开”，然后就可以查看创建运行和运行笔记本的代码。

### <a name="create-run-directly"></a>直接创建运行

点击“开始运行”直接启动 AutoML 运行。 一个通知将出现，指示 AutoML 运行正在启动。

在 AutoML 运行成功启动后，将看到另一个成功的通知。 还可以单击通知按钮来检查运行提交的状态。
通过单击成功通知中的链接来获得 Azure ML。
![成功通知](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>使用笔记本创建运行

选择“在笔记本中打开”以生成笔记本。 单击“全部运行”以执行笔记本。
通过此操作还有机会向 AutoML 运行添加其他设置。

![打开笔记本](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

成功提交笔记本中的运行后，在笔记本输出中将有一个指向 Azure ML 工作区中的试验运行的链接。 可以单击该链接以监视 Azure ML 中的 AutoML 运行。
![笔记本运行所有](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)）

## <a name="next-steps"></a>后续步骤

- [教程：Azure Synapse 专用 SQL 池中的机器学习模型评分](tutorial-sql-pool-model-scoring-wizard.md)。
- [快速入门：在 Azure Synapse 中创建新的 Azure 机器学习链接服务](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)