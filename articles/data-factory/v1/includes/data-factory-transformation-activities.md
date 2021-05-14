---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: e38d7e07582fcbaa5b81e2dd694ddc39d6906be1
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108753221"
---
Azure 数据工厂支持以下转换活动，这些活动既可以单独添加到管道，也可以与其他活动关联在一起添加。

| 数据转换活动 | 计算环境 |
|:--- |:--- |
| [Hive](../data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 流式处理](../data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../data-factory-spark.md) | HDInsight [Hadoop] |
| [Azure 机器学习工作室（经典版）活动：批处理执行和更新资源](../data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [存储过程](../data-factory-stored-proc-activity.md) |Azure SQL、Azure Synapse Analytics 或 SQL Server |
| [Data Lake Analytics U-SQL](../data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../data-factory-use-custom-activities.md) |HDInsight [Hadoop] 或 Azure Batch |

> [!NOTE]
> 可以通过 MapReduce 活动在 HDInsight Spark 群集上运行 Spark 程序。 有关详细信息，请参阅[从 Azure 数据工厂调用 Spark 程序](../data-factory-spark.md)。
> 可以创建一项自定义活动，在安装了 R 的 HDInsight 群集上运行 R 脚本。 请参阅 [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)（使用 Azure 数据工厂运行 R 脚本）。
> 
> 

