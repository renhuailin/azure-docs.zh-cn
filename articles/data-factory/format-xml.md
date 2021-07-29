---
title: Azure 数据工厂中的 XML 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 XML 格式。
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: jianleishen
ms.openlocfilehash: c054c789dd7c26e57a4f393863711d34665854ee
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097617"
---
# <a name="xml-format-in-azure-data-factory"></a>Azure 数据工厂中的 XML 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要 **分析 XML 文件**，请按此文的要求操作。 

以下连接器支持 XML 格式：[Amazon S3](connector-amazon-simple-storage-service.md)、[Amazon S3 兼容存储](connector-amazon-s3-compatible-storage.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google 云存储](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[Oracle 云存储](connector-oracle-cloud-storage.md) 和 [SFTP](connector-sftp.md)。 它可以作为源，但不可作为接收器。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 XML 数据集支持的属性列表。

| 属性         | 描述                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 Xml。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>可用的值如下："UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。| 否       |
| nullValue | 指定 null 值的字符串表示形式。<br/>默认值为 **空字符串**。 | 否 |
| compression | 用来配置文件压缩的属性组。 如果需要在活动执行期间进行压缩/解压缩，请配置此部分。 | 否 |
| type<br>（在 `compression` 下） | 用来读取/写入 XML 文件的压缩编解码器。 <br>允许的值为 bzip2、gzip、deflate、ZipDeflate、TarGzip、Tar、snappy 或 lz4       。 默认设置是不压缩。<br>注意：目前，复制活动不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”、“TarGzip”和“Tar”。<br>注意，使用复制活动解压缩 ZipDeflate/TarGzip/Tar 文件并将其写入基于文件的接收器数据存储时，默认情况下文件将提取到 `<path specified in dataset>/<folder named as source compressed file>/` 文件夹，对[复制活动源](#xml-as-source)使用 `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` 来控制是否以文件夹结构形式保留压缩文件名   。 | 否。  |
| level<br/>（在 `compression` 下） | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](/dotnet/api/system.io.compression.compressionlevel)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上的 XML 数据集的示例：

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 XML 源支持的属性列表。

了解如何通过[架构映射](copy-activity-schema-and-type-mapping.md)来映射 XML 数据和接收器数据存储/格式。 预览 XML 文件时，使用 JSON 层次结构显示数据，并使用 JSON 路径指向字段。

### <a name="xml-as-source"></a>XML 作为源

复制活动的 ***\*source\**** 节支持以下属性。 详细了解 [XML 连接器行为](#xml-connector-behavior)。

| 属性      | 描述                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动的 type 属性必须设置为 XmlSource。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“XML 读取设置”表。 | 否       |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的“XML 读取设置”：

| 属性      | 描述                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings 的 type 必须设置为 XmlReadSettings。 | 是      |
| validationMode | 指定是否要验证 XML 架构。<br>允许的值为 none（默认值、无验证）、xsd（使用 XSD 验证）以及 dtd （使用 DTD 验证）  。 | 否 |
| namespaces | 分析 XML 文件时是否启用命名空间。 允许的值是：true（默认）、false。 | 否 |
| namespacePrefixes | 命名空间 URI 到前缀的映射，用于在分析 xml 文件时为字段命名。<br/>如果 XML 文件具有命名空间，且已启用命名空间，则默认情况下，字段名称与 XML 文档中的名称相同。<br>如果在此映射中为命名空间 URI 定义了一个项，则字段名称为 `prefix:fieldName`。 | 否 |
| detectDataType | 是否检测整数、双精度和布尔数据类型。 允许的值是：true（默认）、false。| 否 |
| compressionProperties | 一组属性，指示如何为给定的压缩编解码器解压缩数据。 | 否       |
| preserveZipFileNameAsFolder<br>（在 `compressionProperties`->`type` 下为 `ZipDeflateReadSettings`）  | 当输入数据集配置了 ZipDeflate 压缩时适用。 指示是否在复制过程中以文件夹结构形式保留源 zip 文件名。<br>- 当设置为 true（默认值）时，数据工厂会将已解压缩的文件写入 `<path specified in dataset>/<folder named as source zip file>/`。<br>- 当设置为 false 时，数据工厂会直接将未解压缩的文件写入 `<path specified in dataset>`。 请确保不同的源 zip 文件中没有重复的文件名，以避免产生冲突或出现意外行为。  | 否 |
| preserveCompressionFileNameAsFolder<br>（在 `compressionProperties`->`type` 下为 `TarGZipReadSettings` 或 `TarReadSettings`） | 当输入数据集配置了 TarGzip/Tar 压缩时适用 。 指示是否在复制过程中以文件夹结构形式保留源压缩文件名。<br>- 当设置为 true（默认值）时，数据工厂会将已解压缩的文件写入 `<path specified in dataset>/<folder named as source compressed file>/`。 <br>- 当设置为 false 时，数据工厂会直接将已解压缩的文件写入 `<path specified in dataset>`。 请确保不同的源文件中没有重复的文件名，以避免产生冲突或出现意外行为。 | 否 |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中，可以在以下数据存储中读取 XML 格式：[Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 可以使用 XML 数据集或使用[内联数据集](data-flow-source.md#inline-datasets)来指向 XML 文件。

### <a name="source-properties"></a>源属性

下表列出了 XML 源支持的属性。 可以在“源选项”选项卡中编辑这些属性。详细了解 [XML 连接器行为](#xml-connector-behavior)。 在使用内联数据集时，你将看到其他文件设置，这些设置与[数据集属性](#dataset-properties)部分中描述的属性相同。 

| 名称 | 描述 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 通配符路径 | 将处理与通配符路径匹配的所有文件。 重写数据集中设置的文件夹和文件路径。 | 否 | String[] | wildcardPaths |
| 分区根路径 | 对于已分区的文件数据，可以输入分区根路径，以便将已分区的文件夹读取为列 | 否 | 字符串 | partitionRootPath |
| 文件列表 | 源是否指向某个列出待处理文件的文本文件 | 否 | `true` 或 `false` | fileList |
| 用于存储文件名的列 | 使用源文件名称和路径创建新列 | 否 | 字符串 | rowUrlColumn |
| 完成后 | 在处理后删除或移动文件。 文件路径从容器根开始 | 否 | 删除：`true` 或 `false` <br> Move：`['<from>', '<to>']` | purgeFiles <br>moveFiles |
| 按上次修改时间筛选 | 选择根据上次更改文件的时间来筛选文件 | 否 | 时间戳 | ModifiedAfter <br>modifiedBefore |
| 验证模式 | 指定是否要验证 XML 架构。 | 否 | `None`（默认值，无验证）<br>`xsd`（使用 XSD 进行验证）<br>`dtd`（使用 DTD 进行验证）。 | validationMode |
| 命名空间 | 分析 XML 文件时是否启用命名空间。 | 否 | `true`（默认值）或 `false` | namespaces |
| 命名空间前缀对 | 命名空间 URI 到前缀的映射，用于在分析 xml 文件时为字段命名。<br/>如果 XML 文件具有命名空间，且已启用命名空间，则默认情况下，字段名称与 XML 文档中的名称相同。<br>如果在此映射中为命名空间 URI 定义了一个项，则字段名称为 `prefix:fieldName`。 | 否 | 使用模式 `['URI1'->'prefix1','URI2'->'prefix2']` 的数组 | namespacePrefixes |
| 允许找不到文件 | 如果为 true，则找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>XML 源脚本示例

下面的脚本是使用数据集模式的映射数据流中 XML 源配置的示例。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

下面的脚本是使用内联数据集模式的 XML 源配置的示例。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML 连接器行为

使用 XML 作为源时，请注意以下事项。

- XML 属性：

    - 元素的属性将被分析为层次结构中元素的子字段。
    - 属性字段的名称遵循模式 `@attributeName`。

- XML 架构验证：

    - 可以选择不验证架构，或者使用 XSD 或 DTD 验证架构。
    - 使用 XSD 或 DTD 验证 XML 文件时，必须通过相对路径在 XML 文件内部引用 XSD/DTD。

- 命名空间处理：

    - 在使用数据流时，可以禁用命名空间，在这种情况下，用于定义命名空间的属性将会被分析为普通属性。
    - 启用命名空间后，默认情况下，元素和属性的名称将遵循模式 `namespaceUri,elementName` 和 `namespaceUri,@attributeName`。 可以为源中的每个命名空间 URI 定义命名空间前缀，在此情况下，元素和属性的名称将遵循模式 `definedPrefix:elementName` 或 `definedPrefix:@attributeName`。

- 值列：

    - 如果 XML 元素同时具有简单文本值和属性/子元素，则简单文本值将被解析为“值列”的值，该列采用内置字段名称 `_value_`。 如果适用，它还会继承元素的命名空间。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
