---
title: 转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 利用 Hadoop、ML 工作室（经典版）或 Azure Data Lake Analytics 在 Azure 数据工厂或 Azure Synapse Analytics 中转换或处理数据。
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 07/31/2018
ms.openlocfilehash: de3f582ea6c475a3f571164133b066469f572dc4
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122270977"
---
# <a name="transform-data-in-azure-data-factory-and-azure-synapse-analytics"></a>在 Azure 数据工厂和 Azure Synapse Analytics 中转换数据

> [!div class="op_single_selector"]
> * [映射数据流](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [机器学习工作室（经典版）](transform-data-using-machine-learning.md) 
> * [存储过程](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Azure Synapse 笔记本](../synapse-analytics/synapse-notebook-activity.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET 自定义](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>概述
本文介绍了 Azure 数据工厂和 Synapse 管道中的数据转换活动，你可以利用这些活动将原始数据转换及处理为预测和见解。 转换活动在计算环境（例如 Azure Databricks 或 Azure HDInsight）中执行。 其提供了相关文章链接，内附各转换活动的详细信息。

该服务支持以下数据转换活动，这些活动可单独添加到[管道](concepts-pipelines-activities.md)，还可与其他活动关联在一起。

## <a name="transform-natively-in-azure-data-factory-and-azure-synapse-analytics-with-data-flows"></a>使用数据流在 Azure 数据工厂和 Azure Synapse Analytics 中进行本机转换

### <a name="mapping-data-flows"></a>映射数据流

映射数据流指 Azure 数据工厂和 Azure Synapse 中以可视方式设计的数据转换。 使用数据流，数据工程师可以开发图形化数据转换逻辑，无需编写代码。 生成的数据流将作为使用横向扩展 Spark 群集的管道中的活动执行。 可以通过现有的计划、控制、流和监视功能，将数据流活动操作化。 有关详细信息，请参阅[映射数据流](concepts-data-flow-overview.md)。

### <a name="data-wrangling"></a>数据整理

Azure 数据工厂中的 Power Query 支持云规模数据整理，使你能够以迭代方式进行云规模的无代码数据准备。 数据整理与 [Power Query Online](/power-query/) 集成，并通过执行 spark，使 Power Query M 函数可用于云规模数据整理。 有关详细信息，请参阅 [Azure 数据工厂中的数据集](wrangling-overview.md)。

> [!NOTE]
> 目前仅支持 Azure 数据工厂中的 Power Query，但不支持 Azure Synapse 中的 Power Query。  有关每项服务中支持的特定功能列表，请参阅 [Azure 数据工厂和 Azure Synapse Analytics](../synapse-analytics/data-integration/concepts-data-factory-differences.md) 管道中的可用功能。

## <a name="external-transformations"></a>外部转换

（可选）可以手动编写代码转换并自行管理外部计算环境。

### <a name="hdinsight-hive-activity"></a>HDInsight Hive 活动
管道中的 HDInsight Hive 活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hive 查询。 有关此活动的详细信息，请参阅 [Hive 活动](transform-data-using-hadoop-hive.md)一文。 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig 活动
管道中的 HDInsight Pig 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 Pig 查询。 有关此活动的详细信息，请参阅 [Pig 活动](transform-data-using-hadoop-pig.md)一文。 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活动
管道中的 HDInsight MapReduce 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 MapReduce 程序。 有关此活动的详细信息，请参阅 [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)一文。

### <a name="hdinsight-streaming-activity"></a>HDInsight Streaming 活动
管道中的 HDInsight 流式处理活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hadoop 流式处理程序。 有关此活动的详细信息，请参阅 [HDInsight Streaming 活动](transform-data-using-hadoop-streaming.md)。

### <a name="hdinsight-spark-activity"></a>HDInsight Spark 活动
管道中的 HDInsight Spark 活动在自己的 HDInsight 群集上执行 Spark 程序。 有关详细信息，请参阅[通过 Azure 数据工厂或 Azure Synapse Analytics 调用 Spark 程序](transform-data-using-spark.md)。 

### <a name="ml-studio-classic-activities"></a>ML 工作室（经典版）活动
借助该服务，可轻松创建管道，这些管道使用已发布的工作室（经典）Web 服务进行预测分析。 使用管道中的[批处理执行活动](transform-data-using-machine-learning.md)，可调用工作室（经典版）Web 服务针对批量数据进行预测。

随着时间推移，需要使用新的输入数据集重新定型工作室（经典版）评分实验中的预测模型。 完成重新定型后，可使用重新定型的机器学习模型更新评分 Web 服务。 可以使用[更新资源活动](update-machine-learning-models.md)，通过新的训练模型更新 Web 服务。  

有关这些工作室（经典版）活动的详细信息，请参阅[使用机器学习工作室（经典版）活动](transform-data-using-machine-learning.md)。 

### <a name="stored-procedure-activity"></a>存储过程活动
可使用数据工厂管道中的 SQL Server 存储过程活动调用以下数据存储之一中的存储过程：你的企业或 Azure VM 中的 Azure SQL 数据库、Azure Synapse Analytics、SQL Server 数据库。 有关详细信息，请参阅[存储过程活动](transform-data-using-stored-procedure.md)一文。  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
Data Lake Analytics U-SQL 活动在 Azure Data Lake Analytics 群集上运行 U-SQL 脚本。 有关详细信息，请参阅 [Data Analytics U-SQL 活动](transform-data-using-data-lake-analytics.md)一文。 

### <a name="azure-synapse-notebook-activity"></a>Azure Synapse Notebook 活动 

Synapse 管道中的 Azure Synapse Notebook 活动在你的 Azure Synapse 工作区中运行 Synapse Notebook。 请参阅[通过运行 Azure Synapse Notebook 转换数据](../synapse-analytics/synapse-notebook-activity.md)。

### <a name="databricks-notebook-activity"></a>Databricks Notebook 活动

管道中的 Azure Databricks Notebook 活动在 Azure Databricks 工作区中运行 Databricks Notebook。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。 请参阅[通过运行 Databricks Notebook 转换数据](transform-data-databricks-notebook.md)。

### <a name="databricks-jar-activity"></a>Databricks Jar 活动

管道中的 Azure Databricks Jar 活动在 Azure Databricks 群集中运行 Spark Jar。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。 请参阅[通过运行 Azure Databricks 中的 Jar 活动转换数据](transform-data-databricks-jar.md)。

### <a name="databricks-python-activity"></a>Databricks Python 活动

管道中的 Azure Databricks Python 活动在 Azure Databricks 群集中运行 Python 文件。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。 请参阅[通过运行 Azure Databricks 中的 Python 活动转换数据](transform-data-databricks-python.md)。

### <a name="custom-activity"></a>自定义活动
如果需要采用数据工厂不支持的方式转换数据，可以使用自己的数据处理逻辑创建自定义活动，并在管道中使用该活动。 可以使用 Azure Batch 服务或 Azure HDInsight 群集配置要运行的自定义 .NET 活动。 有关详细信息，请参阅[使用自定义活动](transform-data-using-dotnet-custom-activity.md)文章。 

可以创建一项自定义活动，在安装了 R 的 HDInsight 群集上运行 R 脚本。 请参阅[使用 Azure 数据工厂和 Synapse 管道运行 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)。 

### <a name="compute-environments"></a>计算环境
为计算环境创建链接服务，并在定义转换活动时使用该服务。 支持两种类型的计算环境。 

- 按需：此情况下，计算环境由该服务完全托管。 作业提交到进程数据前，该服务会自动创建计算环境，作业完成后则自动将其删除。 针对作业执行、群集管理和启动操作，可以配置和控制按需计算环境的粒度设置。 
- 自带：此情况下，可将自己的计算环境（例如 HDInsight 群集）注册为链接服务。 计算环境由用户进行管理，该服务使用它执行活动。 

有关受支持的计算服务列表，请参阅[计算链接服务](compute-linked-services.md)一文。 

## <a name="next-steps"></a>后续步骤
请参阅以下使用转换活动的示例教程：[教程：使用 Spark 转换数据](tutorial-transform-data-spark-powershell.md)