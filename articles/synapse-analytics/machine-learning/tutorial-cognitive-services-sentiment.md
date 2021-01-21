---
title: 教程：使用认知服务进行情绪分析
description: 本教程介绍如何利用认知服务在 Synapse 中进行情绪分析
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 6a4833cf0d73939e01fd3e3e7263c6cba3c0a28a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222184"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>教程：使用认知服务进行情绪分析（预览版）

在本教程中，你将了解如何使用[认知服务](../../cognitive-services/index.yml)在 Azure Synapse 中轻松扩充数据。 我们将使用[文本分析](../../cognitive-services/text-analytics/index.yml)功能来进行情绪分析。 Azure Synapse 中的用户可以直接选择包含文本列的表，以扩充情绪。 这些情绪可以是积极的、消极的、混杂的或中性的，系统还将返回概率。

本教程涉及：

> [!div class="checklist"]
> - 获取 Spark 表数据集的步骤，该数据集中包含用于进行情感分析的文本列。
> - 通过 Azure Synapse 中的向导体验来使用文本分析认知服务扩充数据。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Azure Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 你必须是所用 ADLS Gen2 文件系统的存储 Blob 数据参与者。
- Azure Synapse Analytics 工作区中的 Spark 池。 有关详细信息，请参阅[在 Azure Synapse 中创建 Spark 池](../quickstart-create-sql-pool-studio.md)。
- 使用本教程之前，还需完成本教程所述的预配置步骤。 [在 Azure Synapse 中配置认知服务](tutorial-configure-cognitive-services-synapse.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>创建 Spark 表

本教程需要一个 Spark 表。

1. 下载以下 CSV 文件，其中包含用于文本分析的数据集：[FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. 将文件上传到 Azure Synapse ADLSGen2 存储帐户。
![上传数据](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. 通过右键单击文件并选择“新建笔记本”->“创建 Spark 表”，从 .csv 文件创建 Spark 表。
![创建 Spark 表](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. 在代码单元格中命名表，然后在 Spark 池上运行笔记本。 请记得设置“header = True”。
![运行笔记本](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>启动认知服务向导

1. 右键单击在上一步中创建的 Spark 表。 选择“机器学习-> 使用现有模型进行扩充”打开向导。
![启动评分向导](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. 此时将显示配置面板，并且你需要选择认知服务模型。 选择“文本分析 - 情绪分析”。

![启动评分向导 - 步骤 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>提供身份验证详细信息

为对认知服务进行身份验证，需引用要在 Key Vault 中使用的机密。 以下输入取决于应在本步骤前完成的[前提步骤](tutorial-configure-cognitive-services-synapse.md)。

- **Azure 订阅**：选择你的密钥保管库所属的 Azure 订阅。
- **认知服务帐户**：这是你将连接到的文本分析资源。
- **Azure Key Vault 链接服务**：作为前提步骤的一部分，你已创建了与文本分析资源链接的服务。 请在这里选择该服务。
- **机密名称**：这是你的密钥保管库中的机密的名称，它包含用于向认知服务资源进行身份验证的密钥。

![提供 Azure Key Vault 详细信息](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>配置“情绪分析”

接下来，需要配置情绪分析。 请选择以下详细信息：
- **语言**：选择要对其进行情绪分析的文本的语言。 选择“EN”。
- **文本列**：这是数据集中你要对其进行分析以确定情绪的文本列。 选择“表列注释”。

完成后，单击“打开笔记本”。 这将生成使用 PySpark 代码的笔记本来通过 Azure 认知服务执行情绪分析。

![配置“情绪分析”](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>打开笔记本并运行

你刚才打开的笔记本使用 [mmlspark 库](https://github.com/Azure/mmlspark)连接到认知服务。

你提供的 Azure Key Vault 详细信息可让你在不泄露它们的情况下安全地引用此体验中的机密。

现在，可以“全部运行”单元格，以使用情绪扩充数据。 情绪将返回为“积极”、“消极”、“中性”或“混杂”，还将返回每个情绪的概率。 详细了解[认知服务 - 情绪分析](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md)。

![运行情绪分析](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>后续步骤
- [教程：使用 Azure 认知服务进行异常情况检测](tutorial-cognitive-services-sentiment.md)
- [教程：Azure Synapse 专用 SQL 池中的机器学习模型评分](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)