---
title: 使用数据流处理自动化机器学习 (AutoML) 模型中的数据
description: 了解如何使用 Azure 数据工厂数据流处理自动机器学习 (AutoML) 模型中的数据。
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: e8352b687a3cdfac7ea2a819e1217906598a6837
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563260"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>使用数据流处理自动化机器学习 (AutoML) 模型中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

机器学习项目采用自动化机器学习 (AutoML) 来训练、调整和获取最佳模型，此过程将自动使用你指定用于分类、回归和时序预测的目标指标。 

一项挑战是数据仓库或事务数据库中的原始数据会形成一个大型数据集（如 10 GB），而大型数据集训练模型所需的时间更长，因此建议在训练 Azure 机器学习模型之前先优化数据处理。 本教程将介绍如何使用 ADF 将数据集分区到 Azure 机器学习数据集的 parquet 文件。 

在自动化机器学习 (AutoML) 项目中，将应用以下三种数据处理方案：

* 在训练模型之前将大型数据分区到 parquet 文件。 

     [Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)通常用于在训练模型之前处理数据。 Pandas 数据帧适用于小于 1 GB 的数据，但如果数据大小超过 1 GB，则 Pandas 数据帧处理数据的速度会下降，有时甚至会出现内存不足的错误消息。 建议将 [Parquet 文件](https://parquet.apache.org/)格式用于机器学习，因为它是二进制纵栏式格式。
    
    Azure 数据工厂映射数据流是以可视方式设计的数据转换，数据工程师无需编写代码。 由于管道使用横向扩展的 Spark 群集，因此处理大型数据的功能非常强大。

* 拆分训练数据集和测试数据集。
    
    训练数据集将用于训练模型，测试数据集将用于评估机器学习项目中的模型。 映射数据流有条件拆分活动将拆分训练数据和测试数据。 

* 删除不符合条件的数据。

    可能需要删除不符合条件的数据，如行数为 0 的 parquet 文件。 在本教程中，我们将使用聚合活动获取行计数，行计数将是删除不符合条件的数据的条件。 


## <a name="preparation"></a>准备工作
使用以下 Azure SQL 数据库表。 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>将数据格式转换为 parquet

数据流会将 Azure SQL 数据库表转换为 parquet 文件格式。 

**源数据集**：Azure SQL 数据库的事务表

**接收器数据集**：采用 Parquet 格式的 Blob 存储


## <a name="remove-unqualified-data-based-on-row-count"></a>基于行计数删除不符合条件的数据

假设删除行计数小于 2 的数据。 

1. 使用聚合活动获取行计数：基于 Col2 的“分组依据”和使用 count(1) 作为行计数的“聚合” 。 

    ![配置聚合活动以获取行数](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. 使用接收器活动，在“接收器”选项卡中选择“接收器类型”作为缓存，然后从“设置”选项卡中的“键列”下拉列表中选择所需的列   。 

    ![配置 CacheSink 活动以获取缓存接收器中的行数](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. 使用派生列活动在源流中添加行计数列。 在“派生列的设置”选项卡中，使用 CacheSink#lookup 表达式从 SinkCache 获取行计数。
    ![配置派生列活动以在源 1 中添加行计数](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. 使用有条件拆分活动可删除不符合条件的数据。 在此示例中采用基于 Col2 列的行计数，条件是删除行数小于 2 的数据，因此将删除两行（ID = 2 和 ID = 7）。 你需要将不符合条件的数据保存到 blob 存储，以便进行数据管理。 

    ![配置有条件拆分活动以获取大于或等于 2 的数据](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    创建一个用于获取行计数的新源，在后续步骤中将在初始源中使用行计数。 
>    *    出于性能考虑，请使用 CacheSink。 

## <a name="split-training-data-and-test-data"></a>拆分训练数据和测试数据 

1. 我们想要拆分每个分区的训练数据和测试数据。 在此示例中，对于相同的 Col2 值，请获取前 2 行作为测试数据，并获取其余行作为训练数据。 

    使用窗口活动为每个分区添加一个列行号。 在“概述”选项卡上，选择要进行分区的列（在本教程中，将对 Col2 列进行分区），在“排序”选项卡中进行排序（在本教程中，将基于 ID 进行排序），然后在“窗口列”选项卡中，为每个分区添加一个列作为行号  。 
    ![配置窗口活动以添加一个新列作为行号](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. 使用有条件拆分活动，以拆分每个分区，前 2 行作为测试数据集，其余行作为训练数据集。 在“有条件拆分设置”选项卡中，使用表达式 lesserOrEqual(RowNum,2) 作为条件。 

    ![配置有条件拆分活动，以将当前数据集拆分为训练数据集和测试数据集](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>使用 parquet 格式对训练数据集和测试数据集进行分区

1. 使用接收器活动，在“优化”选项卡中，使用“每个分区的唯一值”将列设置为分区的列键 。 
    ![配置接收器活动以设置训练数据集的分区](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    让我们回顾一下整个管道逻辑。
    ![整个管道逻辑](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)来生成数据流逻辑的其余部分。
