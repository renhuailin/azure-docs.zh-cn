---
title: 如何执行脱机评估 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何使用脱机评估来度量应用的有效性和分析学习循环。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 6f02bb929e0ae0e7de5feaf8707eae0cf84a5e64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830592"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>使用脱机评估分析学习循环

了解如何完成脱机评估并理解结果。

使用脱机评估可以度量个性化体验创建服务相比应用程序默认行为的有效性，了解哪些特征对个性化的作用最大，并自动发现新的机器学习值。

请阅读[脱机评估](concepts-offline-evaluation.md)了解详细信息。

## <a name="prerequisites"></a>先决条件

* 已配置的个性化体验创建服务循环
* 个性化体验创建服务循环必须拥有一定数量的数据 - 为了得到一个有意义的评估结果，我们大概建议日志中至少有 5 万个事件。 （可选）还可以事先导出学习策略文件，以便在同一项评估中进行比较和测试。

## <a name="run-an-offline-evaluation"></a>运行脱机评估

1. 在 [Azure 门户](https://azure.microsoft.com/free/cognitive-services)中，找到个性化体验创建服务资源。
1. 在 Azure 门户中，转到“评估”部分，然后选择“创建评估”。
    ![在 Azure 门户中，转到**评估**部分，然后选择**创建评估**。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 配置以下值：

    * 评估名称。
    * 开始时间和结束时间 - 这些日期指定要在评估中使用的数据范围。 这些数据必须根据[数据保留期](how-to-settings.md)值中的指定存在于日志中。
    * 将“优化发现”设置为“是”。

    > [!div class="mx-imgBorder"]
    > ![选择脱机评估设置](./media/offline-evaluation/create-an-evaluation-form.png)

1. 通过选择“确定”开始进行评估。

## <a name="review-the-evaluation-results"></a>查看评估结果

运行评估可能需要花费较长时间，具体取决于要处理的数据量、要比较的学习策略数量，以及是否请求了优化。

完成后，可以从评估列表选择评估，然后选择“使用其他潜在学习设置比较应用程序的分数”。 若要查看当前学习策略相比于新策略的运行情况，请选择该功能。

1. 查看[学习策略](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)的性能。

    > [!div class="mx-imgBorder"]
    > [![查看评估结果](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. 选择“应用”以应用能最大程度改善数据的模型的策略。

## <a name="next-steps"></a>后续步骤

* 详细了解[脱机评估的工作原理](concepts-offline-evaluation.md)。
