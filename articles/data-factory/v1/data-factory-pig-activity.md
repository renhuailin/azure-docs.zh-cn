---
title: 在 Azure 数据工厂中使用 Pig 活动转换数据
description: 了解如何使用 Azure 数据工厂 v1 中的 Pig 活动在按需/自己的 HDInsight 群集上运行 Pig 脚本。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 75ca22459a05ec1d3ccdb0846ecb0536e38f712a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665352"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Pig 活动转换数据
> [!div class="op_single_selector" title1="转换活动"]
> * [Hive 活动](data-factory-hive-activity.md) 
> * [Pig 活动](data-factory-pig-activity.md)
> * [MapReduce 活动](data-factory-map-reduce.md)
> * [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
> * [Spark 活动](data-factory-spark.md)
> * [机器学习工作室（经典）批处理执行活动](data-factory-azure-ml-batch-execution-activity.md)
> * [机器学习工作室（经典）更新资源活动](data-factory-azure-ml-update-resource-activity.md)
> * [存储过程活动](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)
> * [.NET 自定义活动](data-factory-use-custom-activities.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅[在数据工厂中使用 Pig 活动转换数据](../transform-data-using-hadoop-pig.md)。


数据工厂[管道](data-factory-create-pipelines.md)中的 HDInsight Pig 活动会在[自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或基于 Windows/Linux 的[按需](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 Pig 查询。 本文基于[数据转换活动](data-factory-data-transformation-activities.md)一文，它概述了数据转换和受支持的转换活动。

> [!NOTE] 
> 如果是刚开始接触 Azure 数据工厂，请仔细阅读 [Azure 数据工厂简介](data-factory-introduction.md)，并学习[教程：生成首个数据管道](data-factory-build-your-first-pipeline.md)，然后再阅读本文。 

## <a name="syntax"></a>语法

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>语法详细信息

| 属性 | 说明 | 必需 |
| --- | --- | --- |
| name |活动名称 |是 |
| description |描述活动用途的文本 |否 |
| type |HDinsightPig |是 |
| inputs |Pig 活动使用的一个或多个输入 |否 |
| outputs |Pig 活动生成的一个或多个输出 |是 |
| linkedServiceName |参考在数据工厂中注册为链接服务的 HDInsight 群集 |是 |
| 脚本 |指定 Pig 脚本内联 |否 |
| scriptPath |将 Pig 脚本存储在 Azure Blob 存储中，并提供该文件的路径。 使用“script”或“scriptPath”属性。 两者不能一起使用。 文件名称需区分大小写。 |否 |
| 定义 |在 Pig 脚本中指定参数作为键/值对，以供引用 |否 |

## <a name="example"></a>示例
请考虑游戏日志分析的示例，在此示例中，需确定玩家用在公司所发行游戏上的时间。

以下示例游戏日志是一个以逗号 (,) 分隔的文件。 其中包含以下字段：ProfileID、SessionStart、Duration、SrcIPAddress 和 GameType。

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

用于处理此数据的 **Pig 脚本**：

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

若要在数据工厂管道中执行此 Pig 脚本，请执行以下步骤：

1. 创建链接服务以注册[自己的 HDInsight 计算群集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或配置[按需 HDInsight 计算群集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 将此链接服务称作 **HDInsightLinkedService**。
2. 创建[链接服务](data-factory-azure-blob-connector.md)以配置托管数据的 Azure Blob 存储的连接。 将此链接服务称作 **StorageLinkedService**。
3. 创建指向输入和输出数据的[数据集](data-factory-create-datasets.md)。 将输入数据集称作 **PigSampleIn**，输出数据集称作 **PigSampleOut**。
4. 将文件中的 Pig 查询复制到步骤 #2 中配置的 Azure Blob 存储。 如果托管数据的 Azure 存储与托管查询文件的 Azure 存储不同，请创建单独的 Azure 存储链接服务。 请参考活动配置中的链接服务。 使用“scriptPath”指定 pig 脚本文件和“scriptLinkedService”的路径。 
   
   > [!NOTE]
   > 还可以使用 **script** 属性在活动定义中提供 Pig 脚本内联。 但不建议使用此方法，因为这需转义脚本中的所有特殊字符，并且可能导致调试问题。 最佳做法是遵循步骤 #4。
   >
   >
5. 创建包含 HDInsightPig 活动的管道。 此活动通过在 HDInsight 群集上运行 Pig 脚本来处理输入数据。

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. 部署管道。 有关详细信息，请参阅[创建管道](data-factory-create-pipelines.md)一文。 
7. 使用数据工厂监视和管理视图来监视管道。 有关详细信息，请参阅[监视和管理数据工厂管道](data-factory-monitor-manage-pipelines.md)一文。

## <a name="specifying-parameters-for-a-pig-script"></a>为 Pig 脚本指定参数
考虑以下示例：每天将游戏日志引入 Azure Blob 存储，并将其存储在根据日期和时间分区的文件夹中。 需要对 Pig 脚本进行参数化并在运行时动态传递输入文件夹位置，还需生成根据日期和时间进行分区的输出。

若要使用参数化的 Pig 脚本，请执行以下操作：

* 定义 **defines** 中的参数。

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* 在 Pig 脚本中，使用“**$parameterName**”引用参数，如下例所示：

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>另请参阅
* [Hive 活动](data-factory-hive-activity.md)
* [MapReduce 活动](data-factory-map-reduce.md)
* [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
* [调用 Spark 程序](data-factory-spark.md)
* [调用 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
