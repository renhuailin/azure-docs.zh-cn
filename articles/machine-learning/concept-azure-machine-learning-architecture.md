---
title: 体系结构和重要概念
titleSuffix: Azure Machine Learning
description: 本文概要介绍了构成 Azure 机器学习的体系结构、术语和概念。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2021
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 065a06955adc9c157134e138a25aaee9f54f5bea
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862113"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure 机器学习的工作原理：体系结构和概念

了解 [Azure 机器学习](overview-what-is-azure-machine-learning.md)的体系结构和概念。  本文使你对组件以及它们如何协同工作以帮助你构建、部署和维护机器学习模型的过程有一个概要了解。

## <a name="workspace"></a><a name="workspace"></a> 工作区

[机器学习工作区](concept-workspace.md)是 Azure 机器学习的顶级资源。

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="示意图：工作区及其组件的 Azure 机器学习体系结构":::

工作区是用来执行以下操作的集中位置：

* 管理用于模型训练和部署的资源，例如[计算](#compute-instance)
* 存储你使用 Azure 机器学习时创建的资产，包括：
  * [环境](#environments)
  * [试验](#experiments)
  * [管道](#ml-pipelines)
  * [数据集](#datasets-and-datastores)
  * [模型](#models)
  * [Endpoints](#endpoints)

工作区包括工作区使用的其他 Azure 资源：

+ [Azure 容器注册表 (ACR)](https://azure.microsoft.com/services/container-registry/)：注册在训练期间和部署模型时使用的 Docker 容器。 为最大程度地降低成本，只有在创建部署映像时才会创建 ACR。
+ [Azure 存储帐户](https://azure.microsoft.com/services/storage/)，用作工作区的默认数据存储。  与 Azure 机器学习计算实例一起使用的 Jupyter 笔记本也存储在此处。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：存储有关模型的监视信息。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：存储计算目标使用的机密和工作区所需的其他敏感信息。

可与其他人共享工作区。

## <a name="computes"></a>计算

<a name="compute-targets"></a>[计算目标](concept-compute-target.md)是用于运行训练脚本或承载服务部署的任何计算机或计算机集。 你可以使用本地计算机或远程计算资源作为计算目标。  使用计算目标，你可以在本地计算机上开始训练，然后在不更改训练脚本的情况下横向扩展到云。

Azure 机器学习引入了为机器学习任务配置的两个完全托管的基于云的虚拟机 (VM)：

* <a name="compute-instance"></a> **计算实例**：计算实例是一个 VM，其中包含为机器学习安装的多个工具和环境。 计算实例的主要用途是用于你的开发工作站。  你可以开始运行示例笔记本，无需进行任何设置。 还可将计算实例用作训练和推理作业的计算目标。

* **计算群集**：计算群集是包含多节点缩放功能的 VM 群集。 计算群集更适合用于大型作业和生产的计算目标。  提交作业时，群集会自动纵向扩展。  用作训练计算目标，或用于开发/测试部署。

若要详细了解如何训练计算目标，请参阅[训练计算目标](concept-compute-target.md#train)。  有关部署计算目标的详细信息，请参阅[部署目标](concept-compute-target.md#deploy)。

## <a name="datasets-and-datastores"></a>数据集和数据存储

使用 [**Azure 机器学习数据集**](concept-data.md#datasets)可以更轻松地访问和处理数据。 创建数据集时，将会创建对数据源位置的引用及其元数据的副本。 由于数据保留在其现有位置中，因此不会产生额外的存储成本，也不会损害数据源的完整性。

有关详细信息，请参阅[创建和注册 Azure 机器学习数据集](how-to-create-register-datasets.md)。  有关使用数据集的更多示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)。

数据集使用[数据存储](concept-data.md#datastores)来安全地连接到你的 Azure 存储服务。 数据存储用于存储连接信息，不会损害你的身份验证凭据以及原始数据源的完整性。 它们会存储连接信息，例如与工作区关联的 Key Vault 中的订阅 ID 和令牌授权，让你能够安全地访问存储，而无需在脚本中对其进行硬编码。

## <a name="environments"></a>环境

[工作区](#workspace) > **环境**

[环境](concept-environments.md)是在其中进行机器学习模型的训练或评分的环境的封装。 此环境指定了与训练和评分脚本有关的 Python 包、环境变量和软件设置。  

如需代码示例，请参阅[如何使用环境](how-to-use-environments.md#manage-environments)中的“管理环境”部分。

## <a name="experiments"></a>试验

[工作区](#workspace) > **试验**

试验是指定的脚本中多个运行的分组。 它始终属于工作区。 当你提交运行时，需提供试验名称。 运行的信息存储在该试验下。 如果你提交试验时该名称不存在，系统会自动创建一个新试验。
  
有关使用试验的示例，请参阅[教程：训练第一个模型](tutorial-1st-experiment-sdk-train.md)。

### <a name="runs"></a>运行次数

[工作区](#workspace) > [试验](#experiments) > **运行**

一次运行就是执行一次训练脚本。 试验通常包含多个运行。

Azure 机器学习在试验中记录所有运行并存储以下信息：

* 有关运行的元数据（时间戳、持续时间等）
* 脚本记录的指标
* 试验自动收集的或由你显式上传的输出文件
* 在运行之前包含脚本的目录的快照

提交脚本以训练模型时，会生成运行。 运行可以有零次或多次子级运行。 例如，顶级运行可以有两次子级运行，其中每个可以有其自己的子级运行。

### <a name="run-configurations"></a>运行配置

[工作区](#workspace) > [试验](#experiments) > [运行](#runs) > **运行配置**

运行配置对应该如何在指定的计算目标中运行脚本进行定义。 可以使用配置来指定脚本、要在其上运行的计算目标和 Azure ML 环境、任何特定于分布式作业的配置以及一些其他属性。 有关运行的全套可配置选项的详细信息，请参阅 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)。

运行配置可以保存到包含训练脚本的目录内的文件中，   或构造为内存中对象以及用于提交运行。

有关运行配置的示例，请参阅[配置训练运行](how-to-set-up-training-targets.md)。

### <a name="snapshots"></a>快照

[工作区](#workspace) > [试验](#experiments) > [运行](#runs) > **快照**

提交运行时，Azure 机器学习会将包含该脚本的目录压缩为 zip 文件并将其发送到计算目标。 然后解压缩 zip 文件并运行脚本。 Azure 机器学习还将该 zip 文件存储为快照，作为运行记录的一部分。 有权限访问工作区的任何用户都可以浏览运行记录并下载快照。

### <a name="logging"></a>日志记录

Azure 机器学习会自动为你记录标准运行指标。 不过，你也可以[使用 Python SDK 记录任意指标](how-to-log-view-metrics.md)。

查看日志的方法有多种：实时监视运行状态，或在完成后查看结果。 有关详细信息，请参阅[监视和查看 ML 运行日志](how-to-log-view-metrics.md)。


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git 跟踪与集成

如果以本地 Git 存储库作为源目录开始训练运行，有关存储库的信息将存储在运行历史记录中。 这适用于通过脚本运行配置或 ML 管道提交的运行。 此外，还适用于从 SDK 或机器学习 CLI 提交的运行。

有关详细信息，请参阅 [Azure 机器学习的 Git 集成](concept-train-model-git-integration.md)。

### <a name="training-workflow"></a>训练工作流

运行试验来训练模型时，需执行以下步骤。 下面的训练工作流关系图对此进行了说明：

* 使用在上一部分保存的代码快照的快照 ID 调用 Azure 机器学习。
* Azure 机器学习会创建一个运行 ID （可选）和一个机器学习服务令牌，计算目标（例如机器学习计算/VM）稍后会使用该令牌来与机器学习服务通信。
* 可以选择使用托管计算目标（例如机器学习计算）或非托管计算目标（例如 VM）来运行训练作业。 以下是针对两种场景的数据流：
   * VM/HDInsight，通过 Microsoft 订阅中的 Key Vault 内的 SSH 凭据访问。 Azure 机器学习在计算目标上运行管理代码，以执行以下操作：

   1. 准备环境。 （Docker 是 VM 和本地计算机的一个选项。 请参阅适用于机器学习计算的以下步骤来了解如何在 Docker 容器上运行试验。）
   1. 下载代码。
   1. 设置环境变量和配置。
   1. 运行用户脚本（上一部分提到的代码快照）。

   * 机器学习计算，通过工作区托管标识访问。
由于机器学习计算是托管的计算目标（即，它由 Microsoft 管理），因此它会在 Microsoft 订阅下运行。

   1. 根据需要启动远程 Docker 构造。
   1. 管理代码将写入用户的 Azure 文件共享。
   1. 使用初始命令启动容器。 即，使用上一步骤中所述的管理代码。

* 运行完成后，可查询运行和指标。 在以下流示意图中，当训练计算目标将运行指标从 Cosmos DB 数据库中的存储写回到 Azure 机器学习时，将执行此步骤。 客户端可以调用 Azure 机器学习。 而机器学习又会从 Cosmos DB 数据库提取指标，然后将指标返回给客户端。

[![训练工作流](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>模型

简单地说，模型是一段接受输入并生成输出的代码。 创建机器学习模型涉及选择算法、为其提供数据以及[优化超参数](how-to-tune-hyperparameters.md)。 培训是一个迭代过程，将生成经过培训的模型，它会封装模型在培训过程中学到的内容。

你可以引入在 Azure 机器学习外部训练的模型。 你还可以通过向 Azure 机器学习中的[计算目标](#compute-targets)提交[试验](#experiments)[运行](#runs)来训练模型。 拥有模型后，请在工作区中[注册模型](#register-model)。

Azure 机器学习与框架无关。 创建模型时，可以使用任何流行的机器学习框架，例如 Scikit-learn、XGBoost、PyTorch、TensorFlow 和 Chainer。

有关使用 Scikit-learn 训练模型的示例，请参阅[教程：使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)。


### <a name="model-registry"></a><a name="register-model"></a> 模型注册表

[工作区](#workspace) > **模型**

**模型注册表** 用于跟踪 Azure 机器学习工作区中的所有模型。

模型按名称和版本标识。 每次使用与现有相同的名称注册模型时，注册表都会假定它是新版本。 该版本将递增并且新模型会以同一名称注册。

注册模型时，可以提供其他元数据标记，然后在搜索模型时使用这些标记。

> [!TIP]
> 已注册的模型是构成模型的一个或多个文件的逻辑容器。 例如，如果你有一个存储在多个文件中的模型，则可以在 Azure 机器学习工作区中将这些文件注册为单个模型。 注册后，可以下载或部署已注册的模型，并接收注册的所有文件。

无法删除在活动部署正在使用的已注册模型。

有关注册模型的示例，请参阅[使用 Azure 机器学习训练映像分类模型](tutorial-train-models-with-aml.md)。

## <a name="deployment"></a>部署

将[已注册的模型](#register-model)部署为服务终结点。 你需要下列组件：

* **环境**。 此环境封装运行模型进行推理所需的依赖项。
* **评分代码**。 此脚本接受请求、使用模型为请求评分并返回结果。
* **推理配置**。 推理配置指定以服务形式运行模型所需的环境、入口脚本和其他组件。

有关这些组件的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

### <a name="endpoints"></a>终结点

[工作区](#workspace) > **终结点**

终结点是模型到可在云中托管的 Web 服务的实例化。

#### <a name="web-service-endpoint"></a>Web 服务终结点

将模型部署为 Web 服务时，可以在 Azure 容器实例、Azure Kubernetes 服务或 FPGA 上部署终结点。 可以从模型、脚本和关联的文件创建服务。 这些项目已放入到包含模型执行环境的基础容器映像中。 映像具有负载均衡的 HTTP 终结点，可接收发送到 Web 服务的评分请求。

可以启用 Application Insights 遥测或模型遥测来监视 Web 服务。 遥测数据仅可供你访问。  它存储在你的 Application Insights 和存储帐户实例中。 如果已启用自动缩放，Azure 将自动缩放部署。

下图显示了部署为 Web 服务终结点的模型的推理工作流：

以下是详细信息：

* 用户使用 Azure 机器学习 SDK 等客户端注册模型。
* 用户使用模型、评分文件和其他模型依赖项创建映像。
* Docker 映像在创建后存储在 Azure 容器注册表中。
* 使用上一步中创建的映像将 Web 服务部署到计算目标（容器实例/AKS）中。
* 将评分请求详细信息存储在用户订阅的 Application Insights 中。
* 此外，将遥测推送到 Microsoft/Azure 订阅。

[![推理工作流](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


有关将模型部署为 Web 服务的示例，请参阅[在 Azure 容器实例中部署映像分类模型](tutorial-deploy-models-with-aml.md)。

#### <a name="real-time-endpoints"></a>实时终结点

在设计器中部署经过训练的模型时，可以[将模型部署为实时终结点](tutorial-designer-automobile-price-deploy.md)。 实时终结点通常通过 REST 终结点接收单个请求，并实时返回预测结果。 这与批处理相反，批处理一次处理多个值，并将完成后的结果保存到数据存储中。

#### <a name="pipeline-endpoints"></a>管道终结点

管道终结点允许通过 REST 终结点以编程方式调用 [ML 管道](#ml-pipelines)。 管道终结点可用于自动执行管道工作流。

管道终结点是已发布管道的集合。 通过这种逻辑组织，你可以使用同一终结点管理和调用多个管道。 管道终结点中的每个已发布管道都经过版本控制。 你可以为终结点选择默认管道，也可以在 REST 调用中指定版本。
 


## <a name="automation"></a>自动化

### <a name="azure-machine-learning-cli"></a>Azure 机器学习 CLI 

[Azure 机器学习 CLI](reference-azure-machine-learning-cli.md) 是 Azure CLI（适用于 Azure 平台的跨平台命令行界面）的一个扩展。 此扩展提供了用于自动执行机器学习活动的命令。

### <a name="ml-pipelines"></a>ML 管道

使用[机器学习管道](concept-ml-pipelines.md)可以创建和管理将各个机器学习阶段整合到一起的工作流。 例如，管道可以包括数据准备、模型训练、模型部署以及推理/评分阶段。 每个阶段可以包含多个步骤，每个步骤都能够以无人参与方式在各种计算目标中运行。 

管道步骤可重用，如果这些步骤的输出没有更改，则无需重新运行前面的步骤即可运行。 例如，如果数据未更改，你可以重新训练模型，不需要重新运行成本高昂的数据准备步骤。 管道还使数据科学家能够展开协作，同时可以处理机器学习工作流的不同环节。

## <a name="monitoring-and-logging"></a>监视和日志记录

Azure 机器学习提供以下监视和日志记录功能：

* 对于数据科学家，可监视实验并记录来自训练运行的信息。 有关详细信息，请参阅以下文章：
   * [启动、监视和取消训练运行](how-to-track-monitor-analyze-runs.md)
   * [记录训练运行的指标](how-to-log-view-metrics.md)
   * [使用 MLflow 跟踪试验](how-to-use-mlflow.md)
   * [使用 TensorBoard 将运行可视化](how-to-monitor-tensorboard.md)
* 对于管理员，可使用 Azure Monitor 监视工作区、相关 Azure 资源及事件（例如资源创建和删除）的相关信息。 有关详细信息，请参阅[如何监视 Azure 机器学习](monitor-azure-machine-learning.md)。
* 对于 DevOps 或 MLOps，可监视作为 Web 服务部署的模型生成的信息，以识别部署问题并收集提交给服务的数据 。 有关详细信息，请参阅[收集模型数据](how-to-enable-data-collection.md)和[使用 Application Insights 进行监视](how-to-enable-app-insights.md)。

## <a name="interacting-with-your-workspace"></a>与工作区交互

### <a name="studio"></a>工作室

[Azure 机器学习工作室](overview-what-is-machine-learning-studio.md)提供了你的工作区中所有项目的 Web 视图。  你可以查看数据集、试验、管道、模型和终结点的结果和详细信息。  你还可以在工作室中管理计算资源和数据存储。

你还可以通过工作室访问 Azure 机器学习中包含的交互工具：

+ [Azure 机器学习设计器](concept-designer.md)，用于执行工作流步骤，无需编写代码
+ [自动化机器学习](concept-automated-ml.md)的 Web 体验
+ [Azure 机器学习笔记本](how-to-run-jupyter-notebooks.md)，用于在集成的 Jupyter 笔记本服务器中编写和运行你自己的代码。
+ [数据标记项目](how-to-create-labeling-projects.md)，用于创建、管理和监视项目以标记数据

### <a name="programming-tools"></a>编程工具

> [!IMPORTANT]
> 下面标记了“（预览版）”的工具目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

+  使用[适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro) 来与任何 Python 环境中的服务交互。
+ 使用 [Azure 机器学习设计器](concept-designer.md)执行工作流步骤，无需编写代码。 
+ 使用 [Azure 机器学习 CLI](./reference-azure-machine-learning-cli.md) 实现自动化。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 机器学习，请参阅：

* [什么是 Azure 机器学习？](overview-what-is-azure-machine-learning.md)
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)
* [教程（第 1 部分）：训练模型](tutorial-train-models-with-aml.md)