---
title: JSON 格式
titleSuffix: Azure Data Factory & Azure Synapse
description: 本主题介绍了如何处理 Azure 数据工厂和 Azure Synapse Analytics 管道中的 JSON 格式。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: jianleishen
ms.openlocfilehash: 09bb503be37df8afe54f6dcc8c427bf12f9e7dc1
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252985"
---
# <a name="json-format-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的 JSON 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要 **分析 JSON 文件或以 JSON 格式写入数据**，请遵循本文中的说明。 

以下连接器支持 JSON 格式：[Amazon S3](connector-amazon-simple-storage-service.md)、[Amazon S3 兼容存储](connector-amazon-s3-compatible-storage.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google 云存储](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[Oracle 云存储](connector-oracle-cloud-storage.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 JSON 数据集支持的属性列表。

| 属性         | 说明                                                  | 必需 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Json**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>可用的值如下："UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。| 否       |
| compression | 用来配置文件压缩的属性组。 如果需要在活动执行期间进行压缩/解压缩，请配置此部分。 | 否 |
| type<br/>（在 `compression` 下） | 用来读取/写入 JSON 文件的压缩编解码器。 <br>允许的值为 bzip2、gzip、deflate、ZipDeflate、TarGzip、Tar、snappy 或 lz4       。 默认设置是不压缩。<br>**注意** 当前，复制活动不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”、“TarGzip”和“Tar”。<br>注意，使用复制活动解压缩 ZipDeflate/TarGzip/Tar 文件并将其写入基于文件的接收器数据存储时，默认情况下文件将提取到 `<path specified in dataset>/<folder named as source compressed file>/` 文件夹，对[复制活动源](#json-as-source)使用 `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` 来控制是否以文件夹结构形式保留压缩文件名   。| 否。  |
| level<br/>（在 `compression` 下） | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](/dotnet/api/system.io.compression.compressionlevel)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上的 JSON 数据集的示例：

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 JSON 源和接收器支持的属性列表。

若要了解如何从 JSON 文件中提取数据并将其映射到接收器数据存储/格式（反之亦然），请参阅[架构映射](copy-activity-schema-and-type-mapping.md)。

### <a name="json-as-source"></a>以 JSON 作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **JSONSource**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“JSON 读取设置”表。 | 否       |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的“JSON 读取设置”：

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings 的 type 必须设置为“JsonReadSettings”。 | 是      |
| compressionProperties | 一组属性，指示如何为给定的压缩编解码器解压缩数据。 | 否       |
| preserveZipFileNameAsFolder<br>（在 `compressionProperties`->`type` 下为 `ZipDeflateReadSettings`）  | 当输入数据集配置了 ZipDeflate 压缩时适用。 指示是否在复制过程中以文件夹结构形式保留源 zip 文件名。<br>- 如果设置为“true(默认)”，服务会将解压缩的文件写入 `<path specified in dataset>/<folder named as source zip file>/`。<br>- 如果设置为“false”，服务会将解压缩的文件直接写入 `<path specified in dataset>`。 请确保不同的源 zip 文件中没有重复的文件名，以避免产生冲突或出现意外行为。  | 否 |
| preserveCompressionFileNameAsFolder<br>（在 `compressionProperties`->`type` 下为 `TarGZipReadSettings` 或 `TarReadSettings`） | 当输入数据集配置了 TarGzip/Tar 压缩时适用 。 指示是否在复制过程中以文件夹结构形式保留源压缩文件名。<br>- 如果设置为“true(默认)”，服务会将解压缩文件写入 `<path specified in dataset>/<folder named as source compressed file>/`。 <br>- 如果设置为“false”，服务会将解压文件直接写入 `<path specified in dataset>`。 请确保不同的源文件中没有重复的文件名，以避免产生冲突或出现意外行为。 | 否 |

### <a name="json-as-sink"></a>JSON 作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **JSONSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“JSON 写入设置”表。 | 否       |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的 **JSON 写入设置**：

| 属性      | 说明                                                  | 必需                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings 的类型必须设置为 **JsonWriteSettings**。 | 是                                                   |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：setOfObjects (JSON Lines) 和 arrayOfObjects。 **默认** 值为 **setOfObjects**。 请参阅 [JSON 文件模式](#json-file-patterns)部分，详细了解这些模式。 |否 |

### <a name="json-file-patterns"></a>JSON 文件模式

从 JSON 文件复制数据时，复制活动可自动检测并分析以下 JSON 文件模式。 将数据写入 JSON 文件时，可以在复制活动接收器上配置文件模式。

- **类型 I：setOfObjects**

    每个文件都包含单一对象、JSON Lines 或串联的对象。

    * **单一对象 JSON 示例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **JSON Lines（接收器的默认值）**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **串连的 JSON 示例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **类型 II：arrayOfObjects**

    每个文件包含对象的数组。

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中，你可以在以下数据存储中读取和写入 JSON 格式：[Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>源属性

下表列出了 json 源支持的属性。 你可以在“源选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 通配符路径 | 将处理与通配符路径匹配的所有文件。 重写数据集中设置的文件夹和文件路径。 | 否 | String[] | wildcardPaths |
| 分区根路径 | 对于已分区的文件数据，可以输入分区根路径，以便将已分区的文件夹读取为列 | 否 | 字符串 | partitionRootPath |
| 文件列表 | 源是否指向某个列出待处理文件的文本文件 | 否 | `true` 或 `false` | fileList |
| 用于存储文件名的列 | 使用源文件名称和路径创建新列 | 否 | 字符串 | rowUrlColumn |
| 完成后 | 在处理后删除或移动文件。 文件路径从容器根开始 | 否 | 删除：`true` 或 `false` <br> Move：`['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 按上次修改时间筛选 | 选择根据上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore |
| 单个文档 | 映射数据流从每个文件中读取一个 JSON 文档 | 否 | `true` 或 `false` | singleDocument |
| 不带引号的列名称 | 如果选择了“不带引号的列名称”，则映射数据流会读取不在引号中的 JSON 列。 | 否 | `true` 或 `false` |  unquotedColumnNames |
| 具有注释 | 如果 JSON 数据具有 C 或 C++ 样式的注释，请选择“具有注释” | 否 | `true` 或 `false` | asComments |
| 带单引号 | 读取不在引号中的 JSON 列 | 否 | `true` 或 `false` | singleQuoted |
| 使用反斜杠转义 | 如果使用反斜杠对 JSON 数据中的字符进行转义，请选择“使用反斜杠转义” | 否 | `true` 或 `false` | backslashEscape |
| 允许找不到文件 | 如果为 true，则找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>源格式选项

在数据流中使用 JSON 数据集作为源时，你可以设置五个额外的设置。 可以在“源选项”选项卡的“JSON 设置”可折叠列表下找到这些设置。 。对于“文档窗体”设置，你可以选择“单个文档”、“每行一个文档”和“文档数组”类型之一。

![JSON 设置](media/data-flow/json-settings.png "JSON 设置")

#### <a name="default"></a>默认

默认情况下，将使用以下格式读取 JSON 数据。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>单个文档

如果选择了“单个文档”，则映射数据流会从每个文件中读取一个 JSON 文档。 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
如果选择了“每行一个文档”，则映射数据流会从文件中的每行读取一个 JSON 文档。 

``` json
File1.json
{"json": "record 1"}

File2.json
 {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
 {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}

File3.json
 {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
 {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
 {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
```
如果选择了“文档数组”，则映射数据流会从文件中读取一个文档数组。 

``` json
File.json
[
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
```
> [!NOTE]
> 如果预览你的 JSON 数据时数据流引发了“corrupt_record”错误，则你的数据可能在 JSON 文件中包含单个文档。 设置“单个文档”应当能够清除该错误。

#### <a name="unquoted-column-names"></a>不带引号的列名称

如果选择了“不带引号的列名称”，则映射数据流会读取不在引号中的 JSON 列。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>具有注释

如果 JSON 数据具有 C 或 C++ 样式的注释，请选择“具有注释”。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>带单引号

如果 JSON 字段和值使用单引号而非双引号，请选择“带单引号”。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>使用反斜杠转义

如果使用反斜杠对 JSON 数据中的字符进行转义，请选择“使用反斜杠转义”。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>接收器属性

下表列出了 json 接收器支持的属性。 可以在“设置”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除文件夹 | 如果在写入前目标文件夹已被清除 | 否 | `true` 或 `false` | truncate |
| 文件名选项 | 写入的数据的命名格式。 默认情况下，每个分区有一个 `part-#####-tid-<guid>` 格式的文件 | 否 | 模式：String <br> 按分区：String[] <br> 作为列中的数据：String <br> 输出到单个文件：`['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>在派生列中创建 JSON 结构

你可以通过派生列表达式生成器将复杂的列添加到数据流中。 在派生列转换中，添加一个新列并通过单击蓝色框打开表达式生成器。 若要使列成为复杂列，可以手动输入 JSON 结构，或者使用 UX 以交互方式添加子列。

#### <a name="using-the-expression-builder-ux"></a>使用表达式生成器 UX

在输出架构侧窗格中，将鼠标指针悬停在某个列上，并单击加号图标。 选择“添加子列”，使列成为复杂类型。

![添加子列](media/data-flow/derive-add-subcolumn.png "添加子列")

你可以通过相同的方式添加更多的列和子列。 对于每个非复杂字段，可以在右侧的表达式编辑器中添加表达式。

![添加复杂列](media/data-flow/derive-complex-column.png "添加列")

#### <a name="entering-the-json-structure-manually"></a>手动输入 JSON 结构

若要手动添加 JSON 结构，请添加一个新列，然后在编辑器中输入表达式。 表达式采用以下常规格式：

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

如果为名为“complexColumn”的列输入了此表达式，则会将其作为以下 JSON 写入到接收器：

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整分层定义的示例手动脚本
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
