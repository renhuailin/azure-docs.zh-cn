---
title: 使用 Azure 数据工厂从 Google 云存储复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂将数据从 Google 云存储复制到受支持的接收器数据存储。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: 6dc99ca4c6d7b004bd8994c89a3e79e1f2d817d4
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123312746"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Google 云存储复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何从 Google Cloud Storage (GCS) 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Google 云存储连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

具体而言，此 Google 云存储连接器支持按原样复制文件，或者使用[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析文件。 它利用 GCS 的 S3 兼容互操作性。

## <a name="prerequisites"></a>先决条件

Google 云存储帐户需要进行以下设置：

1. 为 Google 云存储帐户启用互操作性
2. 设置默认项目，使其包含要从目标 GCS Bucket 复制的数据。
3. 使用 GCP 上的云标识和访问管理创建服务帐户并定义适当的权限级别。 
4. 为此服务帐户生成访问密钥。

![检索 Google 云存储的访问密钥](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>所需的权限

若要从 Google 云存储复制数据，请确保你已被授予以下对象操作权限：` storage.objects.get` 和 ` storage.objects.list`。

如果使用数据工厂 UI 进行创作，则需要额外的 ` storage.buckets.list` 权限，才能执行诸如测试与链接服务的连接、从根目录浏览之类的操作。 如果不想授予此权限，则可以选择 UI 中的“测试与文件路径的连接”或“从指定路径浏览”选项。

有关 Google 云存储角色和相关权限的完整列表，请参阅 Google 云站点上的[云存储的 IAM 角色](https://cloud.google.com/storage/docs/access-control/iam-roles)。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)] 

## <a name="create-a-linked-service-to-google-cloud-storage-using-ui"></a>使用 UI 创建到 Google Cloud Storage 的链接服务

使用以下步骤在 Azure 门户 UI 中创建到 Google Cloud Storage 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“Google”并选择“Google Cloud Storage (S3 API)连接器”。

    :::image type="content" source="media/connector-google-cloud-storage/google-cloud-storage-connector.png" alt-text="选择“Google Cloud Storage (S3 API)连接器”。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-google-cloud-storage/configure-google-cloud-storage-linked-service.png" alt-text="配置到 Google Cloud Storage 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

以下各部分详细介绍了定义特定于 Google 云存储的数据工厂实体时使用的属性。

## <a name="linked-service-properties"></a>链接服务属性

Google 云存储链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 GoogleCloudStorage。 | 是 |
| accessKeyId | 机密访问键 ID。 若要查找访问密钥和机密，请参阅[先决条件](#prerequisites)。 |是 |
| secretAccessKey | 机密访问键本身。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| serviceUrl | 将自定义 GCS 终结点指定为 `https://storage.googleapis.com`。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 |否 |

下面是一个示例：

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Google 云存储支持基于格式的数据集中 `location` 设置下的以下属性：

| 属性   | 说明                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 GoogleCloudStorageLocation。 | 是      |
| bucketName | GCS 桶名称。                                          | 是      |
| folderPath | 给定存储桶下的文件夹路径。 如果要使用通配符来筛选文件夹，请跳过此设置并在活动源设置中进行相应的指定。 | 否       |
| fileName   | 给定 Bucket 和文件夹路径下的文件名。 如果要使用通配符来筛选文件，请跳过此设置并在活动源设置中进行相应的指定。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 Google 云存储源支持的属性列表。

### <a name="google-cloud-storage-as-a-source-type"></a>Google 云存储作为源类型

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Google 云存储支持基于格式的复制源中 `storeSettings` 设置下的以下属性：

| 属性                 | 说明                                                  | 必需                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 GoogleCloudStorageReadSettings。 | 是                                                         |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定存储桶或文件夹/文件路径复制。 若要复制 Bucket 或文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：GCS 前缀<br>- prefix | 数据集中配置的给定 Bucket 下的 GCS 密钥名称的前缀，用于筛选源 GCS 文件。 选择名称以 `bucket_in_dataset/this_prefix` 开头的 GCS 密钥。 它利用 GCS 的服务端筛选器，相较于通配符筛选器，该筛选器可提供更好的性能。 | 否 |
| 选项 3：通配符<br>- wildcardFolderPath | 数据集中配置的给定 Bucket 下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件夹名内包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 3：通配符<br>- wildcardFileName | 给定 Bucket 和文件夹路径（或通配符文件夹路径）下包含通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件名包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 3：文件列表<br>- fileListPath | 指明复制给定文件集。 指向包含要复制的文件列表的文本文件，每行一个文件（即数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 |否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果配置 `fileListPath`，则此属性不适用。 |否 |
| deleteFilesAfterCompletion | 指示是否会在二进制文件成功移到目标存储后将其从源存储中删除。 文件删除按文件进行。因此，当复制活动失败时，你会看到一些文件已经复制到目标并从源中删除，而另一些文件仍保留在源存储中。 <br/>此属性仅在二进制文件复制方案中有效。 默认值：false。 |否 |
| modifiedDatetimeStart    | 文件根据“上次修改时间”属性进行筛选。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 **NULL**，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| enablePartitionDiscovery | 对于已分区的文件，请指定是否从文件路径分析分区，并将它们添加为附加的源列。<br/>允许的值为 false（默认）和 true 。 | 否                                            |
| partitionRootPath | 启用分区发现时，请指定绝对根路径，以便将已分区文件夹读取为数据列。<br/><br/>如果未指定，默认情况下，<br/>- 在数据集或源的文件列表中使用文件路径时，分区根路径是在数据集中配置的路径。<br/>- 使用通配符文件夹筛选器时，分区根路径是第一个通配符前的子路径。<br/><br/>例如，假设你将数据集中的路径配置为“root/folder/year=2020/month=08/day=27”：<br/>- 如果将分区根路径指定为“root/folder/year=2020”，则除了文件内的列外，复制活动还将生成另外两个列 `month` 和 `day`，其值分别为“08”和“27”。<br/>- 如果未指定分区根路径，则不会生成额外的列。 | 否                                            |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否                                                          |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| Bucket | key | recursive | 源文件夹结构和筛选器结果（用粗体表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | 是 | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时产生的行为。

假设有以下源文件夹结构，并且要复制以粗体显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | 数据工厂配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 桶：`bucket`<br>- 文件夹路径：`FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`bucket/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的一个文本文件，该文件包含要复制的文件列表（每行一个文件，使用数据集中所配置路径的相对路径）。 |

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要了解有关属性的详细信息，请查看[删除活动](delete-activity.md)。

## <a name="legacy-models"></a>旧模型

如果你过去使用 Amazon S3 连接器从 Google 云存储复制数据，为了后向兼容，我们现在仍按原样支持此操作。 建议使用前面提到的新模型。 数据工厂创作 UI 已切换为生成新模型。

## <a name="next-steps"></a>后续步骤
有关数据存储（Azure 数据工厂中的复制活动支持将其用作源和接收器）的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
