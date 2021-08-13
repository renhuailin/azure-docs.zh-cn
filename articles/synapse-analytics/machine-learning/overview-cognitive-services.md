---
title: Azure Synapse Analytics 中的认知服务
description: 使用来自 Azure 认知服务的预先训练模型，通过 Azure Synapse Analytics 中的人工智能 (AI) 丰富你的数据。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: 3af2efda3d54f8db29d4817357ea8cb826b7c608
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294136"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的认知服务

使用来自 Azure 认知服务的预先训练模型，可以在 Azure Synapse Analytics 中通过人工智能 (AI) 丰富你的数据。

[Azure 认知服务](../../cognitive-services/what-are-cognitive-services.md)是基于云的服务，即使未掌握 AI 或数据科学技能，也能为你的数据添加认知智能。 可以通过多种方式将这些服务与 Synapse Analytics 中的数据结合使用：

- Synapse Analytics 中的认知服务向导在 Synapse Notebook 中生成 PySpark 代码，Synapse Notebook 使用 Spark 表中的数据连接到认知服务。 然后，使用预先训练的机器学习模型，该服务会为你完成将 AI 添加到数据的工作。

- 教程[使用适用于 Apache Spark 的 Microsoft 机器学习生成机器学习应用程序（预览）](tutorial-build-applications-use-mmlspark.md)演示了如何使用适用于 Apache Spark 的 Microsoft 机器学习 (MMLSpark) 调用多项认知服务。

- 从向导生成的 PySpark 代码或本教程中提供的示例 MMLSpark 代码开始，可以编写自己的代码，以将其他认知服务与数据结合使用。 有关可用服务的详细信息，请参阅[什么是 Azure 认知服务？](../../cognitive-services/what-are-cognitive-services.md)。

## <a name="get-started"></a>入门

本教程（[在 Azure Synapse 中使用认知服务的先决条件](tutorial-configure-cognitive-services-synapse.md)）指导你完成在 Synapse Analytics 中使用认知服务之前需要执行的几个步骤。

## <a name="tutorials"></a>教程

以下教程提供了在 Synapse Analytics 中使用认知服务的完整示例。

- [使用认知服务进行情绪分析](tutorial-cognitive-services-sentiment.md) - 使用客户评论的示例数据集，生成一个 Spark 表，其中一列用于指示每行中评论的情绪。

- [使用认知服务进行异常情况检测](tutorial-cognitive-services-anomaly.md) - 使用时序数据的示例数据集，生成一个 Spark 表，其中一列指示每行的数据是否异常。

- [使用适用于 Apache Spark 的 Microsoft 机器学习生成机器学习应用程序（预览）](tutorial-build-applications-use-mmlspark.md) - 本教程演示了如何使用 MMLSpark 从认知服务访问多个模型。

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)
- [什么是认知服务？](../../cognitive-services/what-are-cognitive-services.md)
- [使用 Synapse Analytics 库中的示例笔记本](quickstart-gallery-sample-notebook.md)