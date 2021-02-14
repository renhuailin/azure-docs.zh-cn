---
title: 映射 Azure 数据工厂中的数据集列
description: 了解如何将源列映射到目标列。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371118"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>将源数据集列映射到目标数据集列
> [!NOTE]
> 本文适用于数据工厂版本 1。 

列映射可用于指定如何将源表的 "结构" 中指定的列映射到接收器表 "结构" 中指定的列。 可以在复制活动的 **typeProperties** 节中使用 **ColumnMapping** 属性。

列映射支持以下方案：

* 将源数据集结构中的所有列映射到接收器数据集结构中的所有列。
* 将源数据集结构中的列子集映射到接收器数据集结构中的所有列。

以下是导致异常的错误条件：

* 接收器表的 "structure" 中的列或更多列比映射中指定的更少。
* 重复的映射。
* SQL 查询结果中没有映射中指定的列名。

> [!NOTE]
> 以下示例适用于 Azure SQL 和 Azure Blob，但也适用于任何支持矩形数据集的数据存储。 调整示例中的数据集与链接服务定义，使其指向相关数据源中的数据。

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>示例 1 – 从 Azure SQL 到 Azure Blob 的列映射
在此示例中，输入表有一个结构，并指向 Azure SQL 数据库中的 SQL 表。

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

在本示例中，输出表有一个结构，并指向 Azure Blob 存储中的 Blob。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

以下 JSON 定义管道中的复制活动。 使用 **Translator** 属性将源中的列映射到接收器中的列 (**columnMappings**)。

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**列映射流：**

![列映射流](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>示例 2 – 使用 SQL 查询从 Azure SQL 到 Azure Blob 的列映射
在此示例中，使用 SQL 查询从 Azure SQL 中提取数据，而不是简单地在 "结构" 部分中指定表名和列名。 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
在这种情况下，查询结果首先映射到源的 "结构" 中指定的列。 接下来，源 "structure" 中的列映射到带有 columnMappings 中指定的规则的接收器 "structure" 中的列。  假设查询返回5列，列比源的 "结构" 中指定的列多两个。

**列映射流**

![列映射流-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>后续步骤
有关使用复制活动的教程，请参阅以下文章： 

- [将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
