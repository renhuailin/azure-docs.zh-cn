---
title: 自带 ML 到 Azure Sentinel | Microsoft Docs
description: 本文介绍了如何在 Azure Sentinel 中自行创建和使用机器学习算法进行数据分析。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 839d235412b70669dd3cb9b9942509ef9af7b3c4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779659"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>自带机器学习 (ML) 到 Azure Sentinel

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

机器学习 (ML) 是 Azure Sentinel 的主要基础结构之一，是它的主要独特属性之一。 Azure Sentinel 在几个体验中提供 ML：内置于[融合](fusion.md)关联引擎和 Jupyter Notebook，以及新发布的自行构建 ML (BYO ML) 平台。 

ML 检测模型可适应个人环境和用户行为变化，从而减少[假正](false-positives.md)并识别使用传统方法无法找到的威胁。 许多安全组织都了解 ML 的安全性价值，但是没多少组织能够拥有在安全和 ML 方面都具有专业知识的专业人员。 我们在这里展示了为安全组织和专业人员设计的框架，请在 ML 旅程中与我们一起成长。 刚刚接触 ML 或没有必要专业知识的组织可以从 Azure Sentinel 的内置 ML 功能中获得重大的保护价值。

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="机器学习框架":::


## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>什么是自带机器学习 (BYO-ML) 平台？

对于具有 ML 资源并想要针对其独特的业务需求构建自定义 ML 模型的组织，我们提供了“BYO-ML 平台”。 该平台利用 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) 环境和 Jupyter Notebook 来生成 ML 环境。 它提供以下组件：

- 一个 BYO-ML 包，其中包含的库可帮助访问数据并将结果推送回 Log Analytics (LA)，这样可以将结果与检测、调查和搜寻集成。 

- ML 算法模板，可以自定义这些模板，以适应组织中的特定安全问题。 

- 示例笔记本，用于训练模型和安排模型评分。 

除此之外，还可以自带的 ML 模型和/或 Spark 环境，与 Azure Sentinel 集成。

使用 BYO-ML 平台，可以快速开始自行构建 ML 模型： 

- 带有示例数据的笔记本有助于获得端到端的动手体验，而无需担心处理生产数据。

- 与 Spark 环境集成的包减少了管理基础结构的难题和摩擦。

- 库支持数据移动。 培训和评分笔记本演示了端到端体验，并可作为模板用于适应环境。

### <a name="use-cases"></a>用例
 
BYO-ML 平台和包可显著减少自行构建 ML 检测所需的时间和精力，并释放解决 Azure Sentinel 中特定安全问题的功能。 平台支持以下用例：

“训练 ML 算法以获取自定义模型”：可以采用现有的 ML 算法（由Microsoft 或用户社区共享）并轻松地用自己的数据训练该算法，以获取更适合数据和环境的自定义 ML 模型。

“修改 ML 算法模板以获取自定义模型”：可以修改 ML 算法模板（由 Microsoft 或用户社区共享），并用自己的数据来训练修改后的算法，以根据特定问题来派生自定义模型。

“自行创建模型”：使用 Azure Sentinel 的 BYO-ML 平台和实用程序从头开始自行创建模型。

“集成 Databricks/Spark 环境”：将现有的 Databricks/Spark 环境集成到 Azure Sentinel 中，并使用 BYO-ML 库和模板为其独特的情况构建 ML 模型。

“自行导入 ML 模型”：可以自行导入 ML 模型，并使用 BYO-ML 平台和实用程序将它们与 Azure Sentinel 集成。

“共享 ML 算法”：共享 ML 算法，以便社区采用和调整。

“使用 ML 来实现 SecOps”：自行使用自定义 ML 模型和结果进行搜寻、检测、调查和响应。

本文演示了 BYO-ML 平台的组件，以及如何利用平台和异常资源访问算法，通过 Azure Sentinel 提供自定义 ML 检测。

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark 环境

[Apache Spark™](http://spark.apache.org/) 通过提供用于构建数据管道的统一框架，在简化大数据方面向前迈了一大步。 Azure Databricks 通过提供一个围绕 Spark 构建的零管理云平台，在这方面更进一步。 我们建议将 Databricks 用于 BYO-ML 平台，这样就可以专注于找到直接影响业务的答案，而不是处理数据管道和平台问题。
如果已有 Databricks 或任何其他 Spark 环境，并希望使用现有的设置，BYO-ML 包也会正常运行。 

## <a name="byo-ml-package"></a>BYO-ML 包

BYO ML 包中包含 Microsoft 在安全性 ML 前端的最佳实践和研究。 在此包中，我们提供了以下针对安全性问题的实用程序、笔记本和算法模板的列表。

| 文件名 | 说明 |
| --------- | ----------- |
| azure_sentinel_utilities.whl | 包含用于从 Azure 读取 blob 和写入 Log Analytics 的实用程序。 |
| AnomalousRASampleData | 笔记本演示如何结合使用 Azure Sentinel 中的异常资源访问模型与所生成的训练和测试示例数据。 |
| AnomalousRATraining.ipynb | 用于训练算法，构建和保存模型的笔记本。 |
| AnomalousRAScoring.ipynb | 用于安排要运行的模型，直观显示结果和将分数写回到 Azure Sentinel 的笔记本。 |
|

我们提供的第一个 ML 算法模板用于[异常资源访问检测](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)。 它基于协作筛选算法，并使用 Windows 文件共享访问日志训练（事件 ID 为 5140 的安全事件）。 日志中此模型所需的关键信息是用户和所访问资源的配对。 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>示例演练：异常文件共享访问检测 

现在已经熟悉了 BYO-ML 平台的关键组件，下面提供了一个示例，演示如何使用平台和组件来提供自定义 ML 检测。

### <a name="setup-the-databricksspark-environment"></a>设置 Databricks/Spark 环境

如果还没有 Databricks 环境，需要自行设置一个。 有关说明，请参阅 [Databricks 快速入门](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal)文档。

### <a name="auto-export-instruction"></a>自动导出指令

如果要基于 Azure Sentinel 中自己的数据构建自定义 ML 模型，需要将数据从 Log Analytics 导出到 Blob 存储或事件中心资源，以便 ML 模型可以从 Databricks 访问。 了解如何[将数据引入到 Azure Sentinel 中](connect-data-sources.md)。

在此示例中，需要在 Azure blob 存储中有文件共享访问日志的训练数据。 数据的格式记录在笔记本和库中。

可以使用 [Azure 命令行接口 (CLI)](/cli/azure/monitor/log-analytics) 从 Log Analytics 中自动导出数据。 

如果要运行这些命令，必须在 Log Analytics 工作区、存储帐户和 EventHub 资源中分配“参与者”角色。 

下面是用于设置自动导出的一组示例命令：

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>导出自定义数据

对于 Log Analytics 自动导出不支持的自定义数据，可以使用逻辑应用或其他解决方案来移动数据。 可以参考[将 Log Analytics 数据导出到 Blob 存储](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530)博客和脚本。

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>与 Azure Sentinel 之外的数据关联

还可以将 Azure Sentinel 之外的数据引入 blob 存储或事件中心，并将其与 Azure Sentinel 数据相关联，以构建 ML 模型。 
 
### <a name="copy-and-install-the-related-packages"></a>复制并安装相关的包

将上面提到的 Azure Sentinel GitHub 存储库中的 BYO-ML 包复制到 Databricks 环境。 然后打开笔记本并按照笔记本中的说明在群集上安装所需的库。

### <a name="model-training-and-scoring"></a>模型训练和评分

按照两个笔记本中的说明，根据自己的环境和资源更改配置，按步骤训练和构建模型，然后安排模型为传入文件共享访问日志评分。

### <a name="write-results-to-log-analytics"></a>将结果写入 Log Analytics

安排评分后，可以使用评分笔记本中的模块将评分结果写入与 Azure Sentinel 实例相关联的 Log Analytics 工作区。

### <a name="check-results-in-azure-sentinel"></a>在 Azure Sentinel 中检查结果

如果要查看评分结果以及相关日志详细信息，请返回到 Azure Sentinel 门户。 在“日志” > “自定义日志”中，可以在“AnomalousResourceAccessResult_CL”表（或自己的自定义表名称）中看到结果。  可以使用这些结果来增强调查和搜寻体验。

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="异常资源访问日志":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>用 ML 结果构建自定义分析规则

一旦确认了 ML 结果位于自定义日志表中，并且对分数的保真度感到满意，就可以根据结果创建检测。 从 Azure Sentinel 门户中转到“分析”，并[创建新的检测规则](detect-threats-custom.md)。 下面是一个示例，演示了用于创建检测的查询。

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="为 B Y O M L 检测创建自定义分析规则":::

### <a name="view-and-respond-to-incidents"></a>查看和响应事件
基于 ML 结果设置分析规则后，如果结果超出了查询中设置的阈值，则一个事件会生成并显示在 Azure Sentinel 上的“事件”页上。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Sentinel 的 BYO-ML 平台来自行创建或自行导入机器学习算法，以便分析数据和检测威胁。

- 请参阅 [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)中有关机器学习和众多其他相关主题的帖子。
