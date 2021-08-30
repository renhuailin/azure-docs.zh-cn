---
title: 使用 Hadoop 流式处理活动转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 说明如何使用 Azure 数据工厂中的 Hadoop 流式处理活动通过在 HDInsight 群集上运行 Hadoop 流式处理程序来转换数据。
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: synapse
ms.date: 05/08/2020
ms.openlocfilehash: 4285ee6ae7c5799fa8b65df6bf5adfa79861e3c0
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271199"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>使用 Azure 数据工厂中的 Hadoop 流式处理活动转换数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-hadoop-streaming-activity.md)
> * [当前版本](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

数据工厂[管道](concepts-pipelines-activities.md)中的 HDInsight 流式处理活动会在[自己的](compute-linked-services.md#azure-hdinsight-linked-service)或[按需](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 Hadoop 流式处理程序。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。

如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习[教程：转换数据](tutorial-transform-data-spark-powershell.md)。 

## <a name="json-sample"></a>JSON 示例
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>语法详细信息

| 属性          | 说明                              | 必需 |
| ----------------- | ---------------------------------------- | -------- |
| name              | 活动名称                     | 是      |
| description       | 描述活动用途的文本 | 否       |
| type              | 对于 Hadoop 流式处理活动，活动类型是 HDInsightStreaming | 是      |
| linkedServiceName | 引用在数据工厂中注册为链接服务的 HDInsight 群集。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| mapper            | 指定映射器可执行文件的名称 | 是      |
| reducer           | 指定化简器可执行文件的名称 | 是      |
| combiner          | 指定合并器可执行文件的名称 | 否       |
| fileLinkedService | 对 Azure 存储链接服务的引用，该服务用于存储要执行的映射器、合并器和化简器程序。 此处仅支持 [Azure Blob 存储](./connector-azure-blob-storage.md)和 [ADLS Gen2](./connector-azure-data-lake-storage.md) 链接服务 。 如果未指定此链接服务，则使用 HDInsight 链接服务中定义的 Azure 存储链接服务。 | 否       |
| filePath          | 提供由 fileLinkedService 引用的 Azure 存储中存储的映射器、合并器和化简器程序的路径数组。 此路径区分大小写。 | 是      |
| input             | 指定映射器输入文件的 WASB 路径。 | 是      |
| output            | 指定化简器输出文件的 WASB 路径。 | 是      |
| getDebugInfo      | 指定何时将日志文件复制到 HDInsight 群集使用的（或者）scriptLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：无。 | 否       |
| 参数         | 指定 Hadoop 作业的参数数组。 参数以命令行参数的形式传递到每个任务。 | 否       |
| 定义           | 在 Hive 脚本中指定参数作为键/值对，以供引用。 | 否       | 

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [ML 工作室（经典）批处理执行活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)