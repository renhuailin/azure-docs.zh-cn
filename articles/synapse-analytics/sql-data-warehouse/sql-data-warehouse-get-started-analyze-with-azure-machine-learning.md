---
title: 使用 Azure 机器学习分析数据
description: 使用 Azure 机器学习基于存储在 Azure Synapse 中的数据生成预测机器学习模型。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: bb4f39f14dad9426f48cc86597f74333c830bba5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736192"
---
# <a name="analyze-data-with-azure-machine-learning"></a>使用 Azure 机器学习分析数据

本教程使用 [Azure 机器学习设计器](../../machine-learning/concept-designer.md)构建预测性机器学习模型。 该模型基于 Azure Synapse 中存储的数据。 本教程的场景是预测客户是否有可能购买自行车，这样自行车店 Adventure Works 就可以策划一个针对性的营销活动。

## <a name="prerequisites"></a>先决条件

要逐步完成本教程，需要以下各项：

* 随 AdventureWorksDW 示例数据预先加载的 SQL 池。 若要预配此 SQL 池，请参阅[创建 SQL 池](create-data-warehouse-portal.md)并选择加载示例数据。 如果你已有数据仓库但没有示例数据，则可[手动加载示例数据](./load-data-from-azure-blob-storage-using-copy.md)。
* 一个 Azure 机器学习工作区。 按照[此教程](../../machine-learning/how-to-manage-workspace.md)创建一个新工作区。

## <a name="get-the-data"></a>获取数据

所用数据位于 AdventureWorksDW 的 dbo.vTargetMail 视图中。 为了在本教程中使用数据存储，需要先将数据导出到 Azure Data Lake Storage 帐户，因为 Azure Synapse 当前不支持数据集。 可以通过 Azure 数据工厂使用[复制活动](../../data-factory/copy-activity-overview.md)将数据从数据仓库导出到 Azure Data Lake Storage。 使用以下查询进行导入：

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

当数据在 Azure Data Lake Storage 中可用后，使用 Azure 机器学习中的数据存储[连接到 Azure 存储服务](../../machine-learning/how-to-access-data.md)。 按照以下步骤创建数据存储和相应的数据集：

1. 从 Azure 门户启动 Azure 机器学习工作室或从 [Azure 机器学习工作室](https://ml.azure.com/)登录。

1. 在“管理”部分的左窗格中单击“数据存储”，然后单击“新建数据存储”。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="屏幕截图显示了 Azure 机器学习界面的左窗格":::

1. 为数据存储提供一个名称，选择“Azure Blob 存储”作为类型，提供位置和凭据。 然后单击“创建” 。

1. 接下来，在“资产”部分的左窗格中单击“数据集”。 选择“创建数据集”，并使用选项“从数据存储”。

1. 指定数据集的名称，并选择“表格”作为类型。 然后，单击“下一步”继续操作。

1. 在“选择或创建数据集”部分，选择“以前创建的数据存储”选项。 选择之前创建的数据存储。 单击“下一步”并指定路径和文件设置。 如果文件包含列标题，请确保指定列标题。

1. 最后，单击“创建”以创建数据集。

## <a name="configure-designer-experiment"></a>配置设计器试验

接下来，按照以下步骤进行设计器配置：

1. 单击“创作”部分的左窗格中的“设计器”选项卡。

1. 选择“易用的预生成模块”以生成新管道。

1. 在右侧的设置窗格中，指定管道的名称。

1. 另外，请使用设置按钮选择以前预配的群集作为整个试验的目标计算群集。 关闭“设置”窗格。

## <a name="import-the-data"></a>导入数据

1. 在搜索框下的左窗格中选择“数据集”子选项卡。

1. 将之前创建的数据集拖动到画布中。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="屏幕截图显示了画布上的数据集模块。":::

## <a name="clean-the-data"></a>清理数据

若要清理数据，请删除与模型无关的列。 请遵循以下步骤进行配置：

1. 在左窗格中选择“模块”子选项卡。

1. 将“数据转换 < 操作”下的“选择数据集中的列”模块拖放到画布中 。 将此模块连接到“数据集”模块。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="屏幕截图显示了画布上的列选择模块。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. 单击此模块以打开属性窗格。 单击“编辑”列以指定要删除哪些列。

1. 排除两个列：CustomerAlternateKey 和 GeographyKey。 单击“保存” 

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="屏幕截图显示了删除的列。":::

## <a name="build-the-model"></a>构建模型

数据按 80-20 的比例进行拆分：80% 用于训练机器学习模型，20% 用于测试该模型。 此二元分类问题中使用了“双类”算法。

1. 将“拆分数据”模块拖到画布中。

1. 在属性窗格中，为“第一个输出数据集中的行的比例”输入 0.8。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="屏幕截图显示拆分比例为 0.8。":::

1. 将“双类提升决策树”模块拖放到画布中。

1. 将“训练模型”模块拖到画布中。 通过将该模块连接到“双类提升决策树”（ML 算法）模块和“拆分数据”（训练算法时基于的数据）模块来指定输入 。

1. 对于“训练模型”模块，请在“属性”窗格的“标签列”选项中选择“编辑列”。 选择“BikeBuyer”列作为要预测的列，然后选择“保存”。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="屏幕截图显示了选定的标签列“BikeBuyer”。":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="屏幕截图显示了连接到“双类提升决策树”和“拆分数据”模块的“训练模型”模块。":::

## <a name="score-the-model"></a>为模型评分

现在，测试模型在使用测试数据时表现如何。 将比较两个不同的算法，看哪一个算法的表现更佳。 请遵循以下步骤进行配置：

1. 将“评分模型”模块拖放到画布中，并将其连接到“训练模型”和“拆分模型”模块  。

1. 将“双类贝叶斯平均感知器”拖到试验画布中。 我们将比较此算法与双类提升决策树的表现。

1. 复制“训练模型”和“评分模型”模块并将其粘贴在画布中。 

1. 将“评估模型”模块拖放到画布以比较两种算法。

1. 单击“提交”以设置管道运行。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="屏幕截图显示了画布上的所有剩余模块。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. 运行完成后，右键单击“评估模型”模块，并单击“可视化评估结果”。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="结果的屏幕截图。":::

提供的指标包括 ROC 曲线、精度和召回率示意图以及提升曲线。 查看这些指标，可以看到第一个模型的表现优于第二个。 若要查看第一个模型的预测结果，请右键单击“评分模型”模块，并单击“可视化评分的数据集”以查看预测的结果。

你会看到另外两个列已添加到测试数据集。

* 评分概率：客户购买自行车的可能性。
* 评分标签：模型执行的分类 – 自行车的购买者 (1) 或不是购买者 (0)。 标签的概率阈值设置为 50%，并可以调整。

将“BikeBuyer”列（实际列）与“评分标签”列（预测列）进行比较，以查看模型的表现。 接下来，你可以使用此模型针对新客户进行预测。 你可以[将此模型发布为 Web 服务](../../machine-learning/tutorial-designer-automobile-price-deploy.md)或将结果写回 Azure Synapse。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 机器学习，请参阅 [Azure 上的机器学习简介](../../machine-learning/overview-what-is-azure-machine-learning.md)。

若要了解数据仓库中的内置评分，请参阅[此文](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)。