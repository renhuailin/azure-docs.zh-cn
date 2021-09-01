---
title: 什么是工作区
titleSuffix: Azure Machine Learning
description: 工作区是 Azure 机器学习的顶级资源。 它保留所有训练运行的历史记录，包括日志、指标、输出和脚本快照。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2021
ms.openlocfilehash: d5d2d3a844d417b12ae2899cd5ce3d998731aab6
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860821"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什么是 Azure 机器学习工作区？

工作区是 Azure 机器学习的顶级资源，为使用 Azure 机器学习时创建的所有项目提供了一个集中的处理位置。  工作区保留所有训练运行的历史记录，包括日志、指标、输出和脚本快照。 使用此信息可以确定哪个训练运行产生最佳模型。  

拥有了所需的模型后，可将其注册到工作区。 然后，使用已注册的模型和评分脚本，将其部署到 Azure 容器实例、Azure Kubernetes 服务中或部署到现场可编程门阵列 (FPGA) 作为基于 REST 的 HTTP 终结点。

## <a name="taxonomy"></a>分类 

下图演示了工作区的分类：

[![工作区分类](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

此图显示了工作区的以下组件：

+ 工作区可以包含 [Azure 机器学习计算实例](concept-compute-instance.md)和配置了运行 Azure 机器学习所需的 Python 环境的云资源。

+ 借助[用户角色](how-to-assign-roles.md)可以与其他用户、团队或项目共享你的工作区。
+ [计算目标](concept-azure-machine-learning-architecture.md#compute-targets)用于运行试验。
+ 创建工作区的同时还会创建[关联的资源](#resources)。
+ [试验](concept-azure-machine-learning-architecture.md#experiments)是用于构建模型的训练用运行。  
+ [管道](concept-azure-machine-learning-architecture.md#ml-pipelines)是可重复使用的工作流，用于训练和重新训练模型。
+ [数据集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)帮助管理用于模型训练和管道创建的数据。
+ 有了要部署的模型后，需要创建一个已注册的模型。
+ 使用已注册的模型和评分脚本创建[部署终结点](concept-azure-machine-learning-architecture.md#endpoints)。

## <a name="tools-for-workspace-interaction"></a>用于进行工作区交互的工具

可以通过以下方式与工作区交互：

> [!IMPORTANT]
> 下面标记了“（预览版）”的工具目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

+ 在 Web 上：
    + [Azure 机器学习工作室](https://ml.azure.com) 
    + [Azure 机器学习设计器](concept-designer.md) 
+ 在任何 Python 环境中使用[适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)。
+ 在命令行上使用 Azure 机器学习 [CLI 扩展](./reference-azure-machine-learning-cli.md)
+ [Azure 机器学习 VS Code 扩展](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>使用工作区进行机器学习

机器学习任务将项目读取到和/或写入工作区。

+ 运行试验以训练模型 - 将试验运行结果写入工作区。
+ 使用自动机器学习训练模型 - 将训练结果写入工作区。
+ 在工作区中注册模型。
+ 部署模型 - 使用注册的模型来创建部署。
+ 创建并运行可重用的工作流。
+ 查看机器学习项目，如试验、管道、模型和部署。
+ 跟踪和监视模型。

## <a name="workspace-management"></a>工作区管理

还可以执行以下工作区管理任务：

| 工作区管理任务   | 门户              | 工作室 | Python SDK      | Azure CLI        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| 创建工作区        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| 管理工作区访问权限    | **&check;**   || |  **&check;**    ||
| 创建和管理计算资源    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| 创建笔记本 VM |   | **&check;** | |     ||

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

## <a name="create-a-workspace"></a><a name='create-workspace'></a> 创建工作区

可通过多种方式创建工作区：  

* 使用 [Azure 门户](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace)作为点击界面来逐步完成每个步骤。
* 使用[适用于 Python 的 Azure 机器学习 SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace) 从 Python 脚本或 Jupyter Notebook 动态创建工作区
* 需要按企业安全标准自动创建或自定义创建时，请使用 [Azure 资源管理器模板](how-to-create-workspace-template.md)或 [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md)。
* 如果使用 Visual Studio Code，请使用 [VS Code 扩展](how-to-manage-resources-vscode.md#create-a-workspace)。

> [!NOTE]
> 工作区名称不区分大小写。

## <a name="sub-resources"></a><a name="sub-resources"></a> 子资源

这些子资源是 AML 工作区中生成的主要资源。

* VM：为 AML 工作区提供计算能力，是部署和训练模型中不可或缺的一部分。
* 负载均衡器：网络负载均衡器可为每个计算实例和计算群集管理流量，即使在计算实例/群集停止时也可以工作。
* 虚拟网络：这些网络可帮助 Azure 资源与其他 Azure 资源、互联网以及其他本地网络相互通信。
* 带宽：封装跨区域的所有出站数据传输。

## <a name="associated-resources"></a><a name="resources"></a> 关联的资源

创建新工作区时，它会自动创建工作区使用的几个 Azure 资源：

+ [Azure 存储帐户](https://azure.microsoft.com/services/storage/)，用作工作区的默认数据存储。  与 Azure 机器学习计算实例一起使用的 Jupyter 笔记本也存储在此处。 
  
  > [!IMPORTANT]
  > 默认情况下，存储帐户是常规用途 v1 帐户。 创建工作区后，可以[将其升级到常规用途 v2](../storage/common/storage-account-upgrade.md)。 升级到常规用途 v2 后，请勿在存储帐户上启用分层命名空间。

  若要使用现有 Azure 存储帐户，则该帐户不能是 BlobStorage 类型或高级帐户（Premium_LRS 和 Premium_GRS）。 它也不能具有分层命名空间（与 Azure Data Lake Storage Gen2 一起使用）。 工作区的默认存储帐户不支持高级存储和分层命名空间。 可以将高级存储或分层命名空间用于非默认存储帐户。
  
+ [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)：注册在训练期间和部署模型时使用的 Docker 容器。 要最大程度地降低成本，ACR 在创建部署映像之前会“延迟加载”。

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：存储有关模型的监视信息。

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：存储计算目标使用的机密和工作区所需的其他敏感信息。

> [!NOTE]
> 使用 [Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace) 或 Azure 机器学习 CLI [通过 ARM 模板](how-to-create-workspace-template.md)创建工作区时，可以改用现有的 Azure 资源实例。

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>企业版发生了什么情况

截至 2020 年 9 月，企业版工作区中提供的所有功能现在都可在基本版工作区中使用。 无法再创建新的企业工作区。  使用 `sku` 参数的所有 SDK、CLI 或 Azure 资源管理器调用将继续运行，但将预配基本工作区。

从 12 月 21 日起，所有企业版工作区都将自动设置为基本版，该版本具有相同的功能。 在此过程中不会发生停机。 2021 年 1 月 1 日，企业版将正式停用。 

在这两个版本中，客户都应支付使用的 Azure 资源的成本，并且无需为 Azure 机器学习支付任何额外费用。 有关更多详细信息，请参阅 [Azure 机器学习定价页](https://azure.microsoft.com/pricing/details/machine-learning/)。

## <a name="next-steps"></a>后续步骤

若要详细了解如何根据组织要求规划工作区，请参阅[组织和设置 Azure 机器学习](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization)。

若要开始使用 Azure 机器学习，请参阅：

+ [什么是 Azure 机器学习？](overview-what-is-azure-machine-learning.md)
+ [创建和管理工作区](how-to-manage-workspace.md)
+ [教程：Azure 机器学习入门](quickstart-create-resources.md)
+ [教程：使用自动化机器学习创建第一个分类模型](tutorial-first-experiment-automated-ml.md) 
+ [教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)
