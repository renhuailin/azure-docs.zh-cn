---
title: 快速入门：使用 Synapse Analytics 库中的示例笔记本
description: 了解如何使用 Synapse Analytics 库中的示例笔记本探索数据和生成机器学习模型。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: ''
ms.date: 06/11/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: 3fadfc3fdead7249480b2ce841601149f750b958
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063580"
---
# <a name="quickstart-use-a-sample-notebook-from-the-synapse-analytics-gallery"></a>快速入门：使用 Synapse Analytics 库中的示例笔记本

在此快速入门中，你将学习如何将 Synapse Analytics 库中的示例机器学习笔记本复制到工作区中，修改并运行它。
示例笔记本引入纽约市出租车行程的开放数据集并使用可视化效果帮助准备此数据。 然后，它会定型一个模型，以预测给定行程是否有小费。

此笔记本演示创建模型时使用的基本步骤：数据导入、数据准备、模型定型和评估。 可以将此示例用作使用自己的数据创建模型的起点。

## <a name="prerequisites"></a>先决条件

* [Azure Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 Azure Data Lake Storage Gen2 存储帐户配置为默认存储。 你需要成为所使用的 Data Lake Storage Gen2 文件系统的存储 Blob 数据参与者。
* Azure Synapse Analytics 工作区中的 [Spark 池](../get-started-analyze-spark.md)。

## <a name="copy-the-notebook-to-your-workspace"></a>将笔记本复制到工作区

1. 打开工作区，然后从主页中选择“学习”。
1. 在“知识中心”，选择“浏览库”。
1. 在库中，选择“笔记本”。
1. 查找并选择笔记本“数据研究和 ML 建模 - 使用 Spark MLib 预测纽约市出租车”。

   :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-select-ml-notebook.png" alt-text="在库中选择机器学习示例笔记本。":::

1. 选择“继续”。
1. 在笔记本预览页上，选择“打开笔记本”。 示例笔记本将复制到工作区并打开。

    :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-open-ml-notebook.png" alt-text="将机器学习示例笔记本打开到工作区。":::

1. 在打开的笔记本中的“附加到”菜单中，选择 Apache Spark 池。

## <a name="run-the-notebook"></a>运行笔记本

笔记本分为多个单元格，每个单元格执行一项特定功能。
可以手动运行每个单元格，按顺序运行单元格，或选择“全部运行”以运行所有单元格。

笔记本中每个单元格的说明如下：

1. 导入笔记本使用的 PySpark 函数。
1. **引入日期** - 将数据从 Azure 开放数据集“NycTlcYellow”引入本地数据帧进行处理。 代码提取特定时间段内的数据 - 可修改开始日期和结束日期，以获得不同的数据。
1. 减少数据集采样以加快开发速度。 可修改此步骤以更改示例大小或采样种子。
1. **探索性数据分析** - 显示图表以查看数据。 通过它可以了解创建模型之前可能需要做的数据准备。
1. **数据准备和特征化** - 筛选通过可视化效果发现的离群值数据，并创建一些有用的派生变量。
1. **数据准备和特征化第 2 部分** - 删除不需要的列并创建一些附加功能。
1. **编码** - 将字符串变量转换为逻辑回归模型期望的数字。
1. **测试和定型数据集生成** - 将数据拆分为单独的测试和定型数据集。 可以修改用于拆分数据的分数和随机种子。
1. **定型模型** - 定型逻辑回归模型并显示其“ROC 下的面积”指标，以查看模型的工作情况。 此步骤还会保存已定型的模型，以防要在其它地方使用。
1. **评估和可视化** - 绘制模型的 ROC 曲线以进一步评估模型。

## <a name="save-the-notebook"></a>保存笔记本

若要保存笔记本，请选择工作区命令栏上的“发布”。

## <a name="copying-the-sample-notebook"></a>复制示例笔记本

若要创建此笔记本的副本，请单击顶部命令栏中的省略号，然后选择“克隆”以在工作区中创建副本，或选择“导出”以下载笔记本 (`.ipynb`) 文件的副本。

:::image type="content" source="media\quickstart-gallery-sample-notebook\copy-notebook.png" alt-text="使用“导出”或“克隆”命令创建笔记本的副本。":::

## <a name="clean-up-resources"></a>清理资源

为了确保 Spark 实例在完成操作后关闭，请结束所有已连接的会话（笔记本）。 达到 Apache Spark 池中指定的空闲时间时，池将会关闭。 也可以从笔记本右上角的状态栏中选择“停止会话”。

:::image type="content" source="media\quickstart-gallery-sample-notebook\stop-session.png" alt-text="停止会话。":::

## <a name="next-steps"></a>后续步骤

* [在 GitHub 中查看更多 Synapse 示例笔记本](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning)
* [使用 Apache Spark 进行机器学习](../spark/apache-spark-machine-learning-concept.md)
