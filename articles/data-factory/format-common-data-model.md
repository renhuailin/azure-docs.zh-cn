---
title: Common Data Model 格式
titleSuffix: Azure Data Factory & Azure Synapse
description: 使用 Common Data Model 元数据系统转换数据
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: 612faa714f6a31bb464d30cc5b30766734782d41
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637751"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure 数据工厂中的 Common Data Model 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Common Data Model (CDM) 元数据系统可以轻松地在应用程序和业务流程之间共享数据及其含义。 若要了解详细信息，请参阅 [Common Data Model](/common-data-model/) 概述。

在 Azure 数据工厂中，用户可以使用映射数据流，转换 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) 中存储的 model.json 和清单格式的 CDM 实体数据。 还可以使用 CDM 实体引用接收 CDM 格式数据，这些引用会以 CSV 或 Parquet 格式将数据保存在分区文件夹中。 

## <a name="mapping-data-flow-properties"></a>映射数据流属性

Common Data Model 作为映射数据流中的[内联数据集](data-flow-source.md#inline-datasets)提供，同时用作源和接收器。

> [!NOTE]
> 在编写 CDM 实体时，必须有一个已定义的现有 CDM 实体定义（元数据架构）作为参考。 ADF 数据流接收器将读取该 CDM 实体文件，并将架构导入到接收器中进行字段映射。

### <a name="source-properties"></a>源属性

下表列出了 CDM 源支持的属性。 你可以在“源选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `cdm` | 是 | `cdm` | format |
| 元数据格式 | 对数据的实体引用所在的位置。 如果使用的是 CDM 版本 1.0，请选择清单。 如果使用的是 1.0 之前的 CDM 版本，请选择 model.json。 | 是 | `'manifest'` 或 `'model'` | manifestType |
| 根位置：容器 | CDM 文件夹的容器名称 | 是 | 字符串 | fileSystem |
| 根位置：文件夹路径 | CDM 文件夹的根文件夹位置 | 是 | 字符串 | folderPath |
| 清单文件：实体路径 | 根文件夹中实体的文件夹路径 | 否 | 字符串 | entityPath |
| 清单文件：清单名称 | 清单文件名称。 默认值为“default”  | 否 | 字符串 | manifestName |
| 按上次修改时间筛选 | 选择根据上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore | 
| 架构链接服务 | 语料库所在的链接服务 | 是（如果使用清单） | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 实体引用容器 | 容器语料库位于其中 | 是（如果使用 ADLS Gen2 中的清单和语料库） | 字符串 | adlsgen2_fileSystem |
| 实体引用存储库 | GitHub 存储库名称 | 是（如果使用 GitHub 中的清单和语料库） | 字符串 | github_repository |
| 实体引用分支 | GitHub 存储库分支 | 是（如果使用 GitHub 中的清单和语料库） | 字符串 |  github_branch |
| 语料库文件夹 | 语料库的根位置 | 是（如果使用清单） | 字符串 | corpusPath |
| 语料库实体 | 实体引用的路径 | 是 | 字符串 | 实体 |
| 允许找不到文件 | 如果为 true，则找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

在源和接收器转换过程中选择“实体引用”时，可从以下三个选项中选择实体引用的位置：

* “本地”使用 ADF 已在使用的清单文件中定义的实体
* “自定义”要求指向的实体清单文件应与清单文件 ADF 所用的文件不同
* “标准”将使用 ```Github``` 中维护的 CDM 实体标准库中的实体引用。

### <a name="sink-settings"></a>接收器设置

* 指向包含要写入的实体定义的 CDM 实体引用文件。

![实体设置](media/data-flow/common-data-model-111.png "实体引用")

* 定义要让 ADF 用于写入实体的输出文件的分区路径和格式。

![实体格式](media/data-flow/common-data-model-222.png "实体格式")

* 设置输出文件位置以及清单文件的位置和名称。

![cdm 位置](media/data-flow/common-data-model-333.png "CDM 位置")


#### <a name="import-schema"></a>导入架构

CDM 仅作为内联数据集提供，且默认情况下没有关联架构。 若要获取列元数据，请单击“投影”选项卡中的“导入架构”按钮 。这样你可以引用语料库指定的列名称和数据类型。 若要导入架构，[数据流调试会话](concepts-data-flow-debug-mode.md)必须处于活动状态，还必须具有可以指向的现有 CDM 实体定义文件。

在接收器转换过程中将数据流列映射到实体属性时，请单击“映射”选项卡，然后选择“导入架构”。 ADF 将读取接收器选项中所指向的实体引用，使你能够映射到目标 CDM 架构。

![CDM 接收器设置](media/data-flow/common-data-model-444.png "CDM 映射")

> [!NOTE]
>  使用来自 Power BI 或 Power Platform 数据流的 model.json 源类型时，可能会遇到源转换过程中的“语料库路径为 NULL 或空”错误。 这很可能是因 model.json 文件中分区位置路径的格式问题所致。 若要解决此问题，请执行以下步骤： 

1. 在文本编辑器中打开 model.json 文件
2. 查找 partitions.Location 属性 
3. 将“blob.core.windows.net”更改为“dfs.core.windows.net”
4. 将 URL 中的任何“% 2F”编码修正为“/”
5. 如果使用 ADF 数据流，则必须将分区文件路径中的特殊字符替换为字母数字值，或切换到 Azure Synapse 数据流

### <a name="cdm-source-data-flow-script-example"></a>CDM 源数据流脚本示例

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>接收器属性

下表列出了 CDM 接收器支持的属性。 可以在“设置”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `cdm` | 是 | `cdm` | format |
| 根位置：容器 | CDM 文件夹的容器名称 | 是 | 字符串 | fileSystem |
| 根位置：文件夹路径 | CDM 文件夹的根文件夹位置 | 是 | 字符串 | folderPath |
| 清单文件：实体路径 | 根文件夹中实体的文件夹路径 | 否 | 字符串 | entityPath |
| 清单文件：清单名称 | 清单文件名称。 默认值为“default” | 否 | 字符串 | manifestName |
| 架构链接服务 | 语料库所在的链接服务 | 是 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 实体引用容器 | 容器语料库位于其中 | 是（如果语料库位于 ADLS Gen2） | 字符串 | adlsgen2_fileSystem |
| 实体引用存储库 | GitHub 存储库名称 | 是（如果语料库位于 GitHub） | 字符串 | github_repository |
| 实体引用分支 | GitHub 存储库分支 | 是（如果语料库位于 GitHub） | 字符串 |  github_branch |
| 语料库文件夹 | 语料库的根位置 | 是 | 字符串 | corpusPath |
| 语料库实体 | 实体引用的路径 | 是 | 字符串 | 实体 |
| 分区路径 | 分区的写入位置 | 否 | 字符串 | partitionPath |
| 清除文件夹 | 如果在写入前目标文件夹已被清除 | 否 | `true` 或 `false` | truncate |
| 格式类型 | 选择指定 parquet 格式 | 否 | `parquet`（如果指定） | subformat |
| 列分隔符 | 如果写入 DelimitedText，如何分隔列 | 是（如果写入 DelimitedText） | 字符串 | columnDelimiter |
| 将第一行用作标头 | 如果使用 DelimitedText，是否将列名称添加为标头 | 否 | `true` 或 `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM 接收器数据流脚本示例

关联的数据流脚本为：

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>后续步骤

在映射数据流中创建[源转换](data-flow-source.md)。
