---
title: 获取元数据活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 Azure 数据工厂或 Azure Synapse Analytics 管道中使用“获取元数据”活动。
author: jianleishen
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.author: jianleishen
ms.openlocfilehash: 24b303cc93940ee7884a4766ce542aa543ea285e
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083973"
---
# <a name="get-metadata-activity-in-azure-data-factory-or-azure-synapse-analytics"></a>Azure 数据工厂或 Azure Synapse Analytics 中的“获取元数据”活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

可以使用“获取元数据”活动来检索 Azure 数据工厂或 Synapse 管道中任何数据的元数据。 你可以在条件表达式中使用“获取元数据”活动的输出来执行验证，也可以在后续活动中使用元数据。

## <a name="supported-capabilities"></a>支持的功能

“获取元数据”活动采用数据集作为输入，并返回元数据信息作为输出。 目前支持以下连接器和相应的可检索元数据。 返回的元数据不得超过 4 MB。

### <a name="supported-connectors"></a>受支持的连接器

**文件存储**

| 连接器/元数据 | itemName<br>（文件/文件夹） | itemType<br>（文件/文件夹） | 大小<br>（文件） | created<br>（文件/文件夹） | lastModified<sup>1</sup><br>（文件/文件夹） |childItems<br>（文件夹） |contentMD5<br>（文件） | structure<sup>2</sup><br/>（文件） | columnCount<sup>2</sup><br>（文件） | exists<sup>3</sup><br>（文件/文件夹） |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Amazon S3 兼容存储](connector-amazon-s3-compatible-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Oracle 云存储](connector-oracle-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Blob 存储](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure 文件](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [文件系统](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> 元数据 `lastModified`：
- 对于 Amazon S3、Amazon S3 兼容存储、Google Cloud Storage 和 Oracle 云存储，`lastModified` 适用于 bucket 和密钥，但不适用于虚拟文件夹，而 `exists` 适用于 bucket 和密钥，但不适用于前缀或虚拟文件夹。 
- 对于 Azure Blob 存储，`lastModified` 适用于容器和 Blob，但不适用于虚拟文件夹。

<sup>2</sup> 从二进制文件、JSON 文件或 XML 文件获取元数据时，不支持元数据 `structure` 和 `columnCount`。

<sup>3</sup> 元数据 `exists`：对于 Amazon S3、Amazon S3 兼容存储、Google Cloud Storage 和 Oracle 云存储，`exists` 适用于 bucket 和密钥，但不适用于前缀或虚拟文件夹。

注意以下事项：

- 对文件夹使用“获取元数据”活动时，请确保对给定文件夹具有“列出/执行”权限。
- “获取元数据”活动不支持文件夹/文件的通配符筛选器。
- 连接器上设置的 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 筛选器：

    - 这两个属性用于在从文件夹中获取元数据时筛选子项。 它不适用于从文件中获取元数据的情况。
    - 使用此类筛选器时，输出中的 `childItems` 仅包括在指定范围内修改的文件，而不包括文件夹。
    - 为了应用此类筛选器，GetMetadata 活动会枚举指定文件夹中的所有文件，并检查修改后的时间。 即使预期的符合条件的文件数很少，也要避免指向包含大量文件的文件夹。 

**关系数据库**

| 连接器/元数据 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Amazon RDS for SQL Server](connector-amazon-rds-for-sql-server.md) | √ | √ | √ |
| [Azure SQL 数据库](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>元数据选项

可以在“获取元数据”活动字段列表中指定以下元数据类型，以检索相应的信息：

| 元数据类型 | 说明 |
|:--- |:--- |
| itemName | 文件或文件夹的名称。 |
| itemType | 文件或文件夹的类型。 返回的值为 `File` 或 `Folder`。 |
| 大小 | 文件大小，以字节为单位。 仅适用于文件。 |
| created | 文件或文件夹的创建日期时间。 |
| lastModified | 文件或文件夹的上次修改日期时间。 |
| childItems | 给定文件夹中的子文件夹和文件列表。 仅适用于文件夹。 返回的值为每个子项的名称和类型列表。 |
| contentMD5 | 文件的 MD5。 仅适用于文件。 |
| structure | 文件或关系数据库表的数据结构。 返回的值为列名称和列类型列表。 |
| columnCount | 文件或关系表中的列数。 |
| exists| 是否存在某个文件、文件夹或表。 如果在“获取元数据”字段列表中指定了 `exists`，那么，即使不存在该文件、文件夹或表，该活动也不会失败， 而是在输出中返回 `exists: false`。 |

> [!TIP]
> 若要验证是否存在某个文件、文件夹或表，请在“获取元数据”活动字段列表中指定 `exists`。 然后可以检查活动输出中的 `exists: true/false` 结果。 如果未在该字段列表中指定 `exists`，那么，在找不到对象时，“获取元数据”活动将会失败。

> [!NOTE]
> 从文件存储获取元数据以及配置 `modifiedDatetimeStart` 或 `modifiedDatetimeEnd` 时，输出中的 `childItems` 只包含指定路径中其最近修改时间在指定范围内的文件。 不包括子文件夹中的项。

> [!NOTE]
> 要使 Structure 字段列表为分隔文本和 Excel 格式的数据集提供实际的数据结构，必须启用“`First Row as Header`”属性（只有这些数据源才支持该属性）。

## <a name="syntax"></a>语法

**获取元数据活动**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

数据集

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Type 属性

目前，“获取元数据”活动可以返回以下类型的元数据信息：

属性 | 说明 | 必须
-------- | ----------- | --------
fieldList | 所需元数据信息的类型。 有关支持的元数据的详细信息，请参阅本文的[元数据选项](#metadata-options)部分。 | 是 
dataset | 引用数据集，其元数据将由“获取元数据”活动检索。 有关支持的连接器的信息，请参阅[功能](#supported-capabilities)部分。 有关数据集语法详细信息，请参阅特定的连接器主题。 | 是
formatSettings | 使用格式类型数据集时适用。 | 否
storeSettings | 使用格式类型数据集时适用。 | 否

## <a name="sample-output"></a>示例输出

“获取元数据”的结果显示在活动输出中。 以下两个示例演示了大量的元数据选项。 若要在后续活动中使用这些结果，请使用以下模式：`@{activity('MyGetMetadataActivity').output.itemName}`。

### <a name="get-a-files-metadata"></a>获取文件的元数据

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>获取文件夹的元数据

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
了解支持的其他控制流活动：

- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [ForEach 活动](control-flow-for-each-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
