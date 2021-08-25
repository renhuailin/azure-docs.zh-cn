---
title: Azure 数据工厂中的 Execute Pipeline 活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Execute Pipeline 活动从一个数据工厂管道调用另一个数据工厂管道。
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b46fbddb2a6446eaa711a8ba2c548fff634eee48
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638922"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Execute Pipeline 活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Execute Pipeline 活动允许一个数据工厂管道调用另一个管道。



## <a name="syntax"></a>语法

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必需
-------- | ----------- | -------------- | --------
name | Execute Pipeline 活动的名称。 | String | 是
type | 必须设置为：**ExecutePipeline**。 | String | 是
管道 | 管道引用此管道调用的依赖管道。 管道引用对象具有两个属性：**referenceName** 和 **type**。 referenceName 属性指定引用管道的名称。 type 属性必须设置为 PipelineReference。 | PipelineReference | 是
parameters | 传递给已调用管道的参数 | 将参数名映射为自变量值的 JSON 对象 | 否
waitOnCompletion | 定义活动执行是否等待从属管道执行完成。 默认值为 false。 | 布尔 | 否

## <a name="sample"></a>示例
此方案具有两个管道：

- **主管道** - 此管道具有调用已调用管道的 Execute Pipeline 活动。 主管道采用两个参数：`masterSourceBlobContainer` 和 `masterSinkBlobContainer`。
- **已调用管道** - 此管道具有一个 Copy 活动，该活动将数据从 Azure Blob 源复制到 Azure Blob 接收器。 已调用管道采用两个参数：`sourceBlobContainer` 和 `sinkBlobContainer`。

### <a name="master-pipeline-definition"></a>主管道定义

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>已调用管道定义

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

链接服务

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**源数据集**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**接收器数据集**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>运行管道

若要运行该示例中的主管道，为 masterSourceBlobContainer 和 masterSinkBlobContainer 参数传递以下值： 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

主管道将这些值传递给已调用管道，如以下示例中所示： 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
