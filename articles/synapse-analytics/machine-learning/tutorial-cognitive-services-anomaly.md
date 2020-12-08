---
title: 教程：使用认知服务进行异常情况检测
description: 本教程介绍如何利用认知服务在 Synapse 中进行异常情况检测
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463506"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>教程：使用认知服务进行异常情况检测（预览版）

在本教程中，你将了解如何使用[认知服务](https://go.microsoft.com/fwlink/?linkid=2147492)在 Azure Synapse 中轻松扩充数据。 我们将使用[异常检测器](https://go.microsoft.com/fwlink/?linkid=2147493)进行异常情况检测。 Azure Synapse 中的用户可以直接选择某一个表进行扩充，以检测异常。

本教程涉及：

> [!div class="checklist"]
> - 获取包含时序数据的 Spark 表数据集的步骤。
> - 通过 Azure Synapse 中的向导体验来使用异常检测器认知服务扩充数据。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Azure Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 你必须是所用 ADLS Gen2 文件系统的存储 Blob 数据参与者。
- Azure Synapse Analytics 工作区中的 Spark 池。 有关详细信息，请参阅[在 Azure Synapse 中创建 Spark 池](../quickstart-create-sql-pool-studio.md)。
- 使用本教程之前，还需完成本教程所述的预配置步骤。 [在 Azure Synapse 中配置认知服务](tutorial-configure-cognitive-services-synapse.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>创建 Spark 表

本教程需要一个 Spark 表。

1. 下载以下笔记本文件，其中包含用于生成 Spark 表的代码：[prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. 将文件上传到 Azure Synapse 工作区。
![上传笔记本](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. 打开笔记本文件，然后选择“全部运行”单元格。
![创建 Spark 表](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. 现在，默认的 Spark 数据库中应显示名为 anomaly_detector_testing_data 的 Spark 表。

## <a name="launch-cognitive-services-wizard"></a>启动认知服务向导

1. 右键单击在上一步中创建的 Spark 表。 选择“机器学习-> 使用现有模型进行扩充”打开向导。
![启动评分向导](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. 此时将显示配置面板，并且你需要选择认知服务模型。 选择“异常检测器”。

![启动评分向导 - 步骤 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>提供身份验证详细信息

为对认知服务进行身份验证，需引用要在 Key Vault 中使用的机密。 以下输入取决于应在本步骤前完成的[前提步骤](tutorial-configure-cognitive-services-synapse.md)。

- **Azure 订阅**：选择你的密钥保管库所属的 Azure 订阅。
- **认知服务帐户**：这是你将连接到的文本分析资源。
- **Azure Key Vault 链接服务**：作为前提步骤的一部分，你已创建了与文本分析资源链接的服务。 请在这里选择该服务。
- **机密名称**：这是你的密钥保管库中的机密的名称，它包含用于向认知服务资源进行身份验证的密钥。

![提供 Azure Key Vault 详细信息](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>配置“异常情况检测”

接下来，需要配置异常情况检测。 请提供以下详细信息：

- 粒度：数据采样速率。 例如，如果数据中每分钟对应一个值，则粒度为一分钟。 选择“每月一次” 

- 时间戳：代表系列时间的列。 选择“时间戳”列

- 时序值：表示“时间戳”列所指定时间所对应系列值的列。 选择“列值”

- 分组：对系列进行分组的列。 也就是说，在此列中具有相同值的所有行应形成一个时序。 选择“列组”

完成后，选择“打开笔记本”。 这将生成包含 PySpark 代码的笔记本，它将使用 Azure 认知服务进行异常情况检测。

![配置异常检测器](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>打开笔记本并运行

你刚才打开的笔记本使用 [mmlspark 库](https://github.com/Azure/mmlspark)连接到认知服务。

你提供的 Azure Key Vault 详细信息可让你在不泄露它们的情况下安全地引用此体验中的机密。

现在，可以“全部运行”单元格以执行异常情况检测。 详细了解[认知服务 - 异常检测器](https://go.microsoft.com/fwlink/?linkid=2147493)。

![运行“异常情况检测”](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 认知服务进行情绪分析](tutorial-cognitive-services-sentiment.md)
- [教程：Azure Synapse 专用 SQL 池中的机器学习模型评分](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)