---
title: Azure 数据工厂中的 Parquet 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 Parquet 格式。
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jianleishen
ms.openlocfilehash: 8cd9259b92da17d2879fd8e4de31bca5c5346752
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097815"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure 数据工厂中的 Parquet 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要 **分析 Parquet 文件或以 Parquet 格式写入数据**，请遵循此文章中的说明。 

以下连接器支持 Parquet 格式：[Amazon S3](connector-amazon-simple-storage-service.md)、[Amazon S3 兼容存储](connector-amazon-s3-compatible-storage.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google 云存储](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[Oracle 云存储](connector-oracle-cloud-storage.md) 和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供了 Parquet 数据集支持的属性列表。

| 属性         | 描述                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Parquet**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| compressionCodec | 写入到 Parquet 文件时要使用的压缩编解码器。 从 Parquet 文件中读取时，数据工厂会基于文件元数据自动确定压缩编解码器。<br>支持的类型为“none”、“gzip”、“snappy”（默认值）和“lzo”   。 请注意，当前复制活动在读取/写入 Parquet 文件时不支持 LZO。 | 否       |

> [!NOTE]
> Parquet 文件不支持列名称中包含空格。

下面是 Azure Blob 存储上的 Parquet 数据集的示例：

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 Parquet 源和接收器支持的属性列表。

### <a name="parquet-as-source"></a>Parquet 作为源

复制活动的 ***\*source\**** 部分支持以下属性。

| 属性      | 描述                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **ParquetSource**。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="parquet-as-sink"></a>Parquet 作为接收器

复制活动的 ***\*sink\**** 部分支持以下属性。

| 属性      | 描述                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动接收器的 type 属性必须设置为“ParquetSink”。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“Parquet 写入设置”表。 |    否      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的 Parquet 写入设置：

| 属性      | 描述                                                  | 必须                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings 的类型必须设置为 ParquetWriteSettings。 | 是                                                   |
| maxRowsPerFile | 在将数据写入到文件夹时，可选择写入多个文件，并指定每个文件的最大行数。  | 否 |
| fileNamePrefix | 配置 `maxRowsPerFile` 时适用。<br> 在将数据写入多个文件时，指定文件名前缀，生成的模式为 `<fileNamePrefix>_00000.<fileExtension>`。 如果未指定，将自动生成文件名前缀。 如果源是基于文件的存储或[已启用分区选项的数据存储](copy-activity-performance-features.md)，则此属性不适用。  | 否 |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中，可以在以下数据存储中读取和写入 parquet 格式：[Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>源属性

下表列出了 parquet 源支持的属性。 可以在“源选项”选项卡中编辑这些属性。

| 名称 | 描述 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `parquet` | 是 | `parquet` | format |
| 通配符路径 | 将处理与通配符路径匹配的所有文件。 重写数据集中设置的文件夹和文件路径。 | 否 | String[] | wildcardPaths |
| 分区根路径 | 对于已分区的文件数据，可以输入分区根路径，以便将已分区的文件夹读取为列 | 否 | 字符串 | partitionRootPath |
| 文件列表 | 源是否指向某个列出待处理文件的文本文件 | 否 | `true` 或 `false` | fileList |
| 用于存储文件名的列 | 使用源文件名称和路径创建新列 | 否 | 字符串 | rowUrlColumn |
| 完成后 | 在处理后删除或移动文件。 文件路径从容器根开始 | 否 | 删除：`true` 或 `false` <br> Move：`[<from>, <to>]` | purgeFiles <br> moveFiles |
| 按上次修改时间筛选 | 选择根据上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore |
| 允许找不到文件 | 如果为 true，则找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>源示例

下图是映射数据流中 parquet 源配置的示例。

![Parquet 源](media/data-flow/parquet-source.png)

关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>接收器属性

下表列出了 parquet 接收器支持的属性。 你可以在“设置”选项卡中编辑这些属性。

| 名称 | 描述 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `parquet` | 是 | `parquet` | format |
| 清除文件夹 | 如果在写入前目标文件夹已被清除 | 否 | `true` 或 `false` | truncate |
| 文件名选项 | 写入的数据的命名格式。 默认情况下，每个分区有一个 `part-#####-tid-<guid>` 格式的文件 | 否 | 模式：String <br> 按分区：String[] <br> 作为列中的数据：String <br> 输出到单个文件：`['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>接收器示例

下图是映射数据流中 parquet 接收器配置的示例。

![Parquet 接收器](media/data-flow/parquet-sink.png)

关联的数据流脚本为：

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>数据类型支持

Parquet 复杂数据类型（如 MAP、LIST、STRUCT）目前仅在数据流中受支持，而在复制活动中不受支持。 若要在数据流中使用复杂类型，请不要在数据集中导入文件架构，而是在数据集中将架构留空。 然后，在源转换中导入投影。

## <a name="using-self-hosted-integration-runtime"></a>使用自承载集成运行时

> [!IMPORTANT]
> 对于由自承载集成运行时（例如，本地与云数据存储之间）提供支持的复制，如果不是 **按原样** 复制 Parquet 文件，则需要在 IR 计算机上安装 **64 位 JRE 8（Java 运行时环境）或 OpenJDK** 和 **Microsoft Visual C++ 2010 Redistributable Package**。 请查看以下段落以了解更多详细信息。

对于使用 Parquet 文件序列化/反序列化在自承载集成运行时上运行的复制，ADF 将通过首先检查 JRE 的注册表项 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 来查找 Java 运行时，如果未找到，则会检查系统变量 *`JAVA_HOME`* 来查找 OpenJDK。

- **若要使用 JRE**：64 位 IR 需要 64 位 JRE。 可在[此处](https://go.microsoft.com/fwlink/?LinkId=808605)找到它。
- **若要使用 OpenJDK**：从 IR 版本 3.13 开始受支持。 将 jvm.dll 以及所有其他必需的 OpenJDK 程序集打包到自承载 IR 计算机中，并相应地设置系统环境变量 JAVA_HOME。
- **若要安装 Visual C++ 2010 Redistributable Package**：安装自承载 IR 时未安装 Visual C++ 2010 Redistributable Package。 可在[此处](https://www.microsoft.com/download/details.aspx?id=26999)找到它。

> [!TIP]
> 如果使用自承载集成运行时将数据复制为 Parquet 格式或从 Parquet 格式复制数据，并遇到“调用 java 时发生错误，消息: java.lang.OutOfMemoryError:Java 堆空间”的错误，则可以在托管自承载 IR 的计算机上添加环境变量 `_JAVA_OPTIONS`，以便调整 JVM 的最小/最大堆大小，以支持此类复制，然后重新运行管道  。

![在自承载 IR 上设置 JVM 堆大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

示例：将变量 `_JAVA_OPTIONS` 的值设置为 `-Xms256m -Xmx16g`。 标志 `Xms` 指定 Java 虚拟机 (JVM) 的初始内存分配池，而 `Xmx` 指定最大内存分配池。 这意味着 JVM 初始内存为 `Xms`，并且能够使用的最多内存为 `Xmx`。 默认情况下，ADF 最少使用 64 MB 且最多使用 1G。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
