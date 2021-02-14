---
title: 使用数据流处理自动机器学习 (AutoML) 模型中的数据
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
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520892"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>使用数据流处理自动机器学习 (AutoML) 模型中的数据

机器学习项目采用自动机器学习 (AutoML) ，以使用为分类、回归和时序预测指定的目标指标自动定型、调整和获取最佳模型。 

一项挑战是数据仓库或事务数据库中的原始数据是大型数据集，例如：10GB，大型数据集需要更长的时间来训练模型，因此建议在训练 Azure 机器学习模型之前优化数据处理。 本教程将介绍如何使用 ADF 将数据集分区到 Azure 机器学习数据集的 parquet 文件。 

在自动机器学习 (AutoML) 项目中，它将应用以下三种数据处理方案：

* 在定型模型之前将大数据分区到 parquet 文件。 

     [Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) 通常用于在定型模型之前处理数据。 Pandas 数据帧适用于小于 1 GB 的数据，但如果数据大小超过 1 GB，则 Pandas 数据帧会减缓处理数据的速度，甚至会出现内存错误消息。 建议将[Parquet 文件](https://parquet.apache.org/)格式用于机器学习，因为它是二进制纵栏式格式。
    
    Azure 数据工厂映射数据流是以直观的方式设计的数据转换，可在数据工程师间免费处理。 处理大型数据非常强大，因为管道使用扩展的 Spark 群集。

* 拆分定型数据集和测试数据集。
    
    训练数据集将用于定型模型，测试数据集将用于评估机器学习项目中的模型。 映射数据流有条件拆分活动会拆分定型数据和测试数据。 

* 删除非限定数据。

    您可能想要删除未限定的数据，例如：包含零行的 parquet 文件。 在本教程中，我们将使用聚合活动获取行数计数，行计数将是删除未限定数据的条件。 


## <a name="preparation"></a>准备工作
使用 Azure SQL 数据库的下表。 
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

**源数据集**： Azure SQL 数据库的事务表

**接收器数据集**： Parquet 格式的 Blob 存储


## <a name="remove-unqualified-data-based-on-row-count"></a>基于行计数删除未限定的数据

假设删除小于2的行计数。 

1. 使用聚合活动获取行数的计数： **按 Col2 分组** **，并使用** 计数 (1) 作为行计数。 

    ![配置聚合活动以获取行数](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. 使用接收器活动，在 "**接收器**" 选项卡中选择 **接收器类型** 作为缓存，然后从 "**设置**" 选项卡的 "**键列**" 下拉列表中选择所需列。 

    ![配置 CacheSink 活动以获取缓存接收器中的行数](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. 使用派生列活动在源流中添加行计数列。 在 **派生列的 "设置** " 选项卡中，使用 CacheSink # lookup 表达式从 SinkCache 获取行计数。
    ![配置派生列活动以添加源1中的行数](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. 使用有条件拆分活动可删除未限定的数据。 在此示例中，基于 Col2 列的行计数，条件是删除小于2的行计数，因此将删除两行 ID = 2 和 ID = 7)  (。 你需要将未限定的数据保存到 blob 存储，以便进行数据管理。 

    ![配置有条件拆分活动以获取大于或等于2的数据](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    创建一个新的源，用于获取稍后步骤中将在原始源中使用的行数。 
>    *    从性能角度使用 CacheSink。 

## <a name="split-training-data-and-test-data"></a>拆分定型数据和测试数据 

1. 我们想要拆分每个分区的定型数据和测试数据。 在此示例中，对于相同的 Col2 值，请将前2行作为测试数据，并将 rest 行作为定型数据获取。 

    使用 "窗口活动" 为每个分区添加一个列行号。 在本教程的 " **通过** " 选项卡中，选择 "分区 (列"，将为 Col2) 分区，在本教程中提供 **排序** 选项卡 (，将基于 ID 到订单) ，在 " **窗口列** " 选项卡中添加一个列作为每个分区的行号。 
    ![配置 Window 活动以便将一个新列添加为行号](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. 使用 "有条件拆分" 活动可将每个分区前2行拆分为测试数据集，并将其余行拆分为定型数据集。 在 "有 **条件拆分设置** " 选项卡中，使用 expression LesserOrEqual (RowNum，2作为条件) 。 

    ![配置有条件拆分活动，以将当前数据集拆分为定型数据集和测试数据集](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>采用 parquet 格式的分区定型数据集和测试数据集

1. 使用接收器活动，在 " **优化** " 选项卡中，使用 " **每个分区的唯一值** " 将列设置为 partition 的列键。 
    ![将接收器活动配置为设置定型数据集的分区](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    让我们回顾一下整个管道逻辑。
    ![整个管道的逻辑](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)来生成数据流逻辑的其余部分。
