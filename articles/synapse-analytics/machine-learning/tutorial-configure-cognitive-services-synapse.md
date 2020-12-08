---
title: 教程：Azure Synapse 中认知服务的先决条件
description: 有关如何在 Azure Synapse 中配置使用认知服务的先决条件的教程
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: cf76a3d09da83693791873feb9e6ab03c432e47f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463494"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>教程：在 Azure Synapse 中使用认知服务的先决条件

在本教程中，你将了解如何设置在 Azure Synapse 中安全利用认知服务的先决条件。

本教程涉及：
> [!div class="checklist"]
> - 创建认知服务资源。 例如文本分析或异常检测器。
> - 将认知服务资源的身份验证密钥存储为 Azure Key Vault 中的机密，并配置 Azure Synapse 工作区的访问权限。
> - 在 Azure Synapse Analytics 工作区中创建 Azure Key Vault 链接服务。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Azure Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 你必须是所用 ADLS Gen2 文件系统的存储 Blob 数据参与者。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

[Azure 认知服务](https://go.microsoft.com/fwlink/?linkid=2147492)包含许多不同类型的服务。 下面是 Synapse 教程中使用的一些示例。

### <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源
在 Azure 门户中创建[异常检测器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

![创建异常检测器](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>创建文本分析资源
在 Azure 门户中创建[文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

![创建文本分析](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>创建 Key Vault 并配置机密和访问权限

1. 在 Azure 门户中创建 [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault)。
2. 请转到“Key Vault”->“访问策略”，并向 [Azure Synapse 工作区 MSI](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-identity) 授予从 Azure Key Vault 读取机密的权限。

>请确保已保存策略更改。 此步骤很容易被忽略。

![添加访问策略](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. 请转到认知服务资源（例如“异常检测器”->“密钥和终结点”），将两个密钥之一复制到剪贴板。

4. 请转到“Key Vault”->“机密”创建新机密。 指定机密的名称，然后将上一步的密钥粘贴到“值”字段中。 最后，单击“创建”。

![创建机密](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> 请确保记住或记下此密钥名称！ 稍后从 Azure Synapse Studio 连接到认知服务时，将使用它。

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>在 Azure Synapse 中创建 Azure Keyvault 链接服务

1. 在 Azure Synapse Studio 中打开工作区。 导航到“管理”->“链接服务”。 创建指向刚刚创建的 Key Vault的“Azure Key Vault”链接服务。 然后，通过单击“测试连接”按钮并检查它是否为绿色来验证连接。 如果一切正常，请先单击“创建”，然后单击“全部发布”以保存所做的更改。
![链接服务](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

现在，可以继续学习使用 Azure Synapse Studio 中的 Azure 认知服务体验的教程之一。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 认知服务进行情绪分析](tutorial-cognitive-services-sentiment.md)
- [教程：使用 Azure 认知服务进行异常情况检测](tutorial-cognitive-services-sentiment.md)
- [教程：Azure Synapse 专用 SQL 池中的机器学习模型评分](tutorial-sql-pool-model-scoring-wizard.md)。
- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)