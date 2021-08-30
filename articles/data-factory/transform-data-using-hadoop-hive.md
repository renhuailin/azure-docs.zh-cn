---
title: 使用 Hadoop Hive 活动转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂中的 Hive 活动在按需/自己的 HDInsight 群集上运行 Hive 查询。
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 05/08/2019
ms.openlocfilehash: 337c7d15ba042a49031697e2bc6ea6a54101dbf3
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122270980"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-hive-activity.md)
> * [当前版本](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

数据工厂[管道](concepts-pipelines-activities.md)中的 HDInsight Hive 活动会在[自己的](compute-linked-services.md#azure-hdinsight-linked-service)或[按需](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 Hive 查询。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。

如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习[教程：转换数据](tutorial-transform-data-spark-powershell.md)。 

## <a name="syntax"></a>语法

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| 属性            | 说明                                                  | 必需 |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | 活动名称                                         | 是      |
| description         | 描述活动用途的文本                | 否       |
| type                | 对于 Hive 活动，活动类型是 HDinsightHive        | 是      |
| linkedServiceName   | 引用在数据工厂中注册为链接服务的 HDInsight 群集。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| scriptLinkedService | 对 Azure 存储链接服务的引用，该服务用于存储要执行的 Hive 脚本。 此处仅支持 [Azure Blob 存储](./connector-azure-blob-storage.md)和 [ADLS Gen2](./connector-azure-data-lake-storage.md) 链接服务 。 如果未指定此链接服务，则使用 HDInsight 链接服务中定义的 Azure 存储链接服务。  | 否       |
| scriptPath          | 提供由 scriptLinkedService 引用的 Azure 存储中存储的脚本文件的路径。 文件名称需区分大小写。 | 是      |
| getDebugInfo        | 指定何时将日志文件复制到 HDInsight 群集使用的（或者）scriptLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：无。 | 否       |
| 参数           | 指定 Hadoop 作业的参数数组。 参数以命令行参数的形式传递到每个任务。 | 否       |
| 定义             | 在 Hive 脚本中指定参数作为键/值对，以供引用。 | 否       |
| queryTimeout        | 查询超时值（以分钟为单位）。 适用于 HDInsight 群集已启用企业安全性套餐的情况。 | 否       |

>[!NOTE]
>查询超时默认值为 120 分钟。 

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [ML 工作室（经典）批处理执行活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
