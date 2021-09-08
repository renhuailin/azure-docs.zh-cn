---
title: Azure 数据工厂中的 Excel 格式
titleSuffix: Azure Data Factory & Azure Synapse
description: 本主题介绍了如何处理 Azure 数据工厂和 Azure Synapse Analytics 中的 Excel 格式。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: jianleishen
ms.openlocfilehash: 7a1a0331276169bb81dcf86c74aab1a16855b25c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255843"
---
# <a name="excel-file-format-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的 Excel 文件格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要 **分析 Excel 文件**，请按此文的要求操作。 该服务支持“.xls”和“.xlsx”。

以下连接器支持 Excel 格式：[Amazon S3](connector-amazon-simple-storage-service.md)、[Amazon S3 兼容存储](connector-amazon-s3-compatible-storage.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google 云存储](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[Oracle 云存储](connector-oracle-cloud-storage.md)和 [SFTP](connector-sftp.md)。 它可以作为源，但不可作为接收器。 

>[!NOTE]
>使用 [HTTP](connector-http.md) 时不支持“.xls”格式。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Excel 数据集支持的属性列表。

| 属性         | 说明                                                  | 必需 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为“Excel”。   | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 | 是      |
| sheetName        | 要读取数据的 Excel 工作表名称。                       | 指定 `sheetName` 或 `sheetIndex` |
| sheetIndex | 用于读取数据的 Excel 工作表索引，从 0 开始。 | 指定 `sheetName` 或 `sheetIndex` |
| range            | 给定工作表中用于定位选择性数据的单元格范围，例如：<br>- 未指定：以表的形式从第一个非空行和列读取整个工作表<br>- `A3`：读取从给定单元格开始的表，动态检测下面的所有行和右侧的所有列<br>- `A3:H5`：以表的形式读取此固定范围<br>- `A3:A3`：读取此单个单元格 | 否       |
| firstRowAsHeader | 指定是否要将给定工作表/范围内的第一行视为带有列名的标题行。<br>允许的值为 **true** 和 **true**（默认值）。 | 否       |
| nullValue        | 指定 null 值的字符串表示形式。 <br>默认值为 **空字符串**。 | 否       |
| compression | 用来配置文件压缩的属性组。 如果需要在活动执行期间进行压缩/解压缩，请配置此部分。 | 否 |
| type<br/>（在 `compression` 下） | 用来读取/写入 JSON 文件的压缩编解码器。 <br>允许的值为 bzip2、gzip、deflate、ZipDeflate、TarGzip、Tar、snappy 或 lz4       。 默认设置是不压缩。<br>注意：目前，复制活动不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”、“TarGzip”和“Tar”。<br>**注意**，使用复制活动来解压缩 ZipDeflate 文件并写入到基于文件的接收器数据存储时，会将文件提取到以下文件夹：`<path specified in dataset>/<folder named as source zip file>/`。 | 否。  |
| level<br/>（在 `compression` 下） | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](/dotnet/api/system.io.compression.compressionlevel)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上 Excel 数据集的示例：

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Excel 源支持的属性列表。

### <a name="excel-as-source"></a>Excel 作为源 

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为“ExcelSource”。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 | 否       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中，可以在以下数据存储中读取 Excel 格式：[Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 可以使用 Excel 数据集或[内联数据集](data-flow-source.md#inline-datasets)来指向 Excel 文件。

### <a name="source-properties"></a>源属性

下表列出了 Excel 源支持的属性。 你可以在“源选项”选项卡中编辑这些属性。在使用内联数据集时，你会看到其他文件设置，这些设置与[数据集属性](#dataset-properties)部分描述的属性相同。

| 名称                      | 说明                                                  | 必需 | 允许的值                                            | 数据流脚本属性         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| 通配符路径           | 将处理与通配符路径匹配的所有文件。 重写数据集中设置的文件夹和文件路径。 | 否       | String[]                                                  | wildcardPaths                     |
| 分区根路径       | 对于已分区的文件数据，可以输入分区根路径，以便将已分区的文件夹读取为列 | 否       | 字符串                                                    | partitionRootPath                 |
| 文件列表             | 源是否指向某个列出待处理文件的文本文件 | 否       | `true` 或 `false`                                         | fileList                          |
| 用于存储文件名的列 | 使用源文件名称和路径创建新列       | 否       | 字符串                                                    | rowUrlColumn                      |
| 完成后          | 在处理后删除或移动文件。 文件路径从容器根开始 | 否       | 删除：`true` 或 `false` <br> Move：`['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| 按上次修改时间筛选   | 选择根据上次更改时间筛选文件 | 否       | 时间戳                                                 | ModifiedAfter <br> modifiedBefore |
| 允许找不到文件 | 如果为 true，则找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>源示例

下图是使用数据集模式的映射数据流中 Excel 源配置的示例。

![Excel 源](media/data-flow/excel-source.png)

关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

如果你使用内联数据集，则会在映射数据流中看到以下源选项。

![Excel 源内联数据集](media/data-flow/excel-source-inline-dataset.png)

关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
