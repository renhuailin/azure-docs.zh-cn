---
title: 从/向文件系统复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂或 Azure Synapse Analytics 管道将数据从文件系统复制到支持的接收器数据存储，或者从支持的源数据存储复制到文件系统。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: 8366afa9c992e891589a8db2e9990992c2b98754
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123313038"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory-or-azure-synapse-analytics"></a>使用 Azure 数据工厂或 Azure Synapse Analytics 向/从文件系统复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-onprem-file-system-connector.md)
> * [当前版本](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何向/从文件系统复制数据。 有关详细信息，请阅读 [Azure 数据工厂](introduction.md)或 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 的简介文章。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此文件系统连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

具体而言，此文件系统连接器支持：

- 从/向本地计算机或网络文件共享复制文件。 若要使用 Linux 文件共享，请在 Linux 服务器上安装 [Samba](https://www.samba.org/)。
- 使用 Windows 身份验证复制文件。
- 按原样复制文件，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

> [!NOTE]
> 从网络文件共享加载数据时，不支持映射的网络驱动器。 请改用实际路径，例如 ` \\server\share`。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-file-system-linked-service-using-ui"></a>使用 UI 创建文件系统链接服务

使用以下步骤在 Azure 门户 UI 中创建文件系统链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索“文件”并选择“文件系统连接器”。

    :::image type="content" source="media/connector-file-system/file-system-connector.png" alt-text="文件系统连接器的屏幕截图。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-file-system/configure-file-system-linked-service.png" alt-text="文件系统链接服务的配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于文件系统的数据工厂和 Synapse 管道实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

文件系统链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**FileServer**。 | 是 |
| host | 指定要复制的文件夹的根路径。 请对字符串中的特殊字符使用转义符“\"”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。 | 是 |
| userId | 指定有权访问服务器的用户的 ID。 | 是 |
| password | 指定用户的密码 (userId)。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 在[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

### <a name="sample-linked-service-and-dataset-definitions"></a>链接服务和数据集定义示例

| 方案 | 链接服务定义中的“主机” | 数据集定义中的“folderPath” |
|:--- |:--- |:--- |
| 集成运行时计算机上的本地文件夹： <br/><br/>示例:D:\\\* 或 D:\folder\subfolder\\* |在 JSON 中：`D:\\`<br/>在 UI 上：`D:\` |在 JSON 中：`.\\` 或 `folder\\subfolder`<br>在 UI 上：`.\` 或 `folder\subfolder` |
| 远程共享文件夹： <br/><br/>示例：\\\\myserver\\share\\\* 或 \\\\myserver\\share\\folder\\subfolder\\\* |在 JSON 中：`\\\\myserver\\share`<br/>在 UI 上：`\\myserver\share` |在 JSON 中：`.\\` 或 `folder\\subfolder`<br/>在 UI 上：`.\` 或 `folder\subfolder` |

>[!NOTE]
>通过 UI 创作时，无需输入双反斜杠 (`\\`) 即可转义就像通过 JSON 操作一样，请指定单个反斜杠。

**示例：**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userId": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

在基于格式的数据集中的 `location` 设置下，文件系统支持以下属性：

| 属性   | 说明                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 **FileServerLocation**。 | 是      |
| folderPath | 文件夹的路径。 如果要使用通配符筛选文件夹，请跳过此设置并在活动源设置中指定。 | 否       |
| fileName   | 给定 folderPath 下的文件名。 如果要使用通配符筛选文件，请跳过此设置并在活动源设置中指定。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供文件系统源和接收器支持的属性列表。

### <a name="file-system-as-source"></a>文件系统作为源

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

在基于格式的复制源中的 `storeSettings` 设置下，文件系统支持以下属性：

| 属性                 | 说明                                                  | 必需                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **FileServerReadSettings**。 | 是                                           |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定文件夹/文件路径复制。 若要复制文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：服务器端筛选器<br>- fileFilter  | 文件服务器端原生筛选器，与选项 3 通配符筛选器相比，该筛选器可提供更好的性能。 使用 `*` 匹配零个或零个以上的字符，使用 `?` 匹配零个或单个字符。 若要详细了解语法和说明，请参阅[此部分](/dotnet/api/system.io.directory.getfiles#System_IO_Directory_GetFiles_System_String_System_String_System_IO_SearchOption_)下的“备注”。 | 否                                                          |
| 选项 3：客户端筛选器<br>- wildcardFolderPath | 带有通配符的文件夹路径，用于筛选源文件夹。 此类筛选在服务内部进行，此操作会枚举给定路径下的文件夹/文件，然后应用通配符筛选器。<br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 3：客户端筛选器<br>- wildcardFileName | 给定 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 此类筛选在服务内部进行，此操作会枚举给定路径下的文件，然后应用通配符筛选器。<br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件名中包含通配符或此转义字符，请使用 `^` 进行转义。<br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 3：文件列表<br>- fileListPath | 指明复制给定文件集。 指向包含要复制的文件列表的文本文件，每行一个文件（即数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 |否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果配置 `fileListPath`，则此属性不适用。 |否 |
| deleteFilesAfterCompletion | 指示是否会在二进制文件成功移到目标存储后将其从源存储中删除。 文件删除按文件进行。因此，当复制活动失败时，你会看到一些文件已经复制到目标并从源中删除，而另一些文件仍保留在源存储中。 <br/>此属性仅在二进制文件复制方案中有效。 默认值：false。 |否 |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| enablePartitionDiscovery | 对于已分区的文件，请指定是否从文件路径分析分区，并将它们添加为附加的源列。<br/>允许的值为 false（默认）和 true 。 | 否                                            |
| partitionRootPath | 启用分区发现时，请指定绝对根路径，以便将已分区文件夹读取为数据列。<br/><br/>如果未指定，默认情况下，<br/>- 在数据集或源的文件列表中使用文件路径时，分区根路径是在数据集中配置的路径。<br/>- 使用通配符文件夹筛选器时，分区根路径是第一个通配符前的子路径。<br/><br/>例如，假设你将数据集中的路径配置为“root/folder/year=2020/month=08/day=27”：<br/>- 如果将分区根路径指定为“root/folder/year=2020”，则除了文件内的列外，复制活动还将生成另外两个列 `month` 和 `day`，其值分别为“08”和“27”。<br/>- 如果未指定分区根路径，则不会生成额外的列。 | 否                                            |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否                                            |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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
                    "type": "FileServerReadSettings",
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

### <a name="file-system-as-sink"></a>文件系统作为接收器

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)]

在基于格式的复制接收器中的 `storeSettings` 设置下，文件系统支持以下属性：

| 属性                 | 说明                                                  | 必需 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **FileServerWriteSettings**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否       |

**示例：**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用 **粗体** 表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | 是 | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | 是 | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时的结果行为。

假设有以下源文件夹结构，并且要复制加粗显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | 管道配置 |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 文件夹路径：`root/FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`root/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的文本文件，其中包含要复制的文件列表，每行一个文件，以及在数据集中配置的路径的相对路径。 |

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例

本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| 是 |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| 是 |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| 是 |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要详细了解这些属性，请查看 [Delete 活动](delete-activity.md)

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍按原样支持以下模型，以实现向后兼容性。 建议使用上面几个部分中提到的新模型，并且创作 UI 已切换为生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**FileShare** |是 |
| folderPath | 文件夹路径。 支持通配符筛选器，允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名中包含通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>示例：“rootfolder/subfolder/”，请参阅[示例链接服务和数据集定义](#sample-linked-service-and-dataset-definitions)和[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内具有通配符或此转义符，请使用 `^` 进行转义。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 preserveHierarchy，则复制活动会自动生成采用以下模式的文件名称：“Data.[活动运行 ID GUID].[GUID (如果为 FlattenHierarchy)].[格式(如果已配置)].[压缩(如果已配置)]”，例如“Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”；如果使用表名（而不是查询）从表格源进行复制，则名称模式为“[表名].[格式].[压缩(如果已配置)]”，例如“MyTable.csv”。 |否 |
| modifiedDatetimeStart | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat、ParquetFormat    。 请将格式中的“type”属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型包括：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：“最佳”和“最快” 。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>如需复制具有给定名称的单个文件，请使用文件夹部分指定 **folderPath** 并使用文件名指定 **fileName**。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。

>[!NOTE]
>如果文件筛选器使用“fileFilter”属性，则在建议你今后使用添加到“fileName”的新筛选器功能时，仍按原样支持该属性。

**示例：**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>旧复制活动源模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：FileSystemSource |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制/创建空的文件夹/子文件夹。<br/>允许的值为：true（默认）、false  | 否 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>旧复制活动接收器模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**FileSystemSink** |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 合并过程中不执行记录重复数据删除。 如果指定文件名，则合并的文件名将为指定的名称；否则，会自动生成文件名。 | 否 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
