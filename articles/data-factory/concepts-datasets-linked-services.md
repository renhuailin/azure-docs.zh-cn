---
title: 数据集
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解 Azure 数据工厂和 Azure Synapse Analytics 管道中的数据集。 数据集表示输入/输出数据。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: dff54916007046d3d0d8d6741ca6fce7409c5d99
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787975"
---
# <a name="datasets-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的数据集
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-create-datasets.md)
> * [当前版本](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


本文介绍了数据集的涵义，采用 JSON 格式定义数据集的方式以及数据集在 Azure Data Factory 和 Synapse 管道中的用法。

如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)了解相关概述。  有关 Azure Synapse 的详细信息，请参阅[什么是 Azure Synapse](../synapse-analytics/overview-what-is.md)

## <a name="overview"></a>概述
一个数据工厂或 Synapse 工作区可以有一个或多个管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 现在，数据集这一名称的意义已经变为看待数据的一种方式，就是以输入和输出的形式指向或引用活动中要使用的数据 。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 例如，Azure Blob 数据集可在 Blob 存储中指定供活动读取数据的 Blob 容器和文件夹。

创建数据集之前，必须创建[链接服务](concepts-linked-services.md)，将数据存储链接到服务。 链接的服务类似于连接字符串，它定义服务连接到外部资源时所需的连接信息。 不妨这样考虑：数据集代表链接的数据存储中的数据结构，而链接服务则定义到数据源的连接。 例如，Azure 存储链接服务可链接一个存储帐户。 Azure Blob 数据集表示该 Azure 存储帐户中包含要处理的输入 Blob 的 Blob 容器和文件夹。

下面是一个示例方案。 要将数据从 Blob 存储复制到 SQL 数据库，请创建以下两个链接服务：Azure Blob 存储和 Azure SQL 数据库。 然后创建两个数据集：带分隔符的文本数据集（假设将文本文件作为源，则它指的是 Azure Blob 存储链接服务）和 Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure Blob 存储和 Azure SQL 数据库链接服务分别包含服务在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 带分隔符的文本数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Blob 存储中的输入 blob 以及与格式相关的设置。 Azure SQL 表数据集指定你的 SQL 数据库中要将数据复制到其中的 SQL 表。

下图显示了管道、活动、数据集和链接服务之间的关系：

:::image type="content" source="media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png" alt-text="管道、活动、数据集和链接服务之间的关系":::


## <a name="dataset-json"></a>数据集 JSON
采用以下 JSON 格式定义数据集：

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
下表描述了上述 JSON 中的属性：

属性 | 描述 | 必需 |
-------- | ----------- | -------- |
name | 数据集名称。 请参见[命名规则](naming-rules.md)。 |  是 |
type | 数据集的类型。 指定数据工厂支持的类型之一（例如：DelimitedText、AzureSqlTable）。 <br/><br/>有关详细信息，请参阅[数据集类型](#dataset-type)。 | 是 |
架构 | 数据集的架构表示物理数据类型和形状。 | 否 |
typeProperties | 每种类型的类型属性各不相同。 若要详细了解受支持的类型及其属性，请参阅[数据集类型](#dataset-type)。 | 是 |

导入数据集的架构时，请选择“导入架构”按钮，然后选择从源或本地文件导入。 在大多数情况下，将直接从源导入架构。 但是，如果已有本地架构文件（Parquet 文件或带标头的 CSV），则可以指示服务根据该文件定义架构。

在复制活动中，数据集用于源和接收器。 数据集中定义的架构可选作引用。 如果要在源和接收器之间应用列/字段映射，请参阅[架构和类型映射](copy-activity-schema-and-type-mapping.md)。

在数据流中，数据集用于源和接收器转换。 数据集定义基本数据架构。 如果数据没有架构，则可以对源和接收器使用架构偏差。 来自数据集的元数据在源转换中显示为源投影。 源转换中的投影表示定义了名称和类型的数据流数据。

## <a name="dataset-type"></a>数据集类型

服务支持多种数据集类型，具体取决于使用的数据存储。 有关支持的数据存储列表，可参阅[连接器概述](connector-overview.md)一文。 单击数据存储，了解如何创建链接服务及其数据集。

例如，对于带分隔符的文本数据集，数据集类型设为“DelimitedText”，如下方的 JSON 示例所示：

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>创建数据集
可以使用以下任一工具或 SDK 创建数据集：[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure 资源管理器模板和 Azure 门户

## <a name="current-version-vs-version-1-datasets"></a>当前版本与版本 1 数据集的比较

以下是数据工厂当前版本（以及 Azure Synapse）中的数据集与旧版数据工厂版本 1 中的数据集之间的一些差异：

- 当前版本不支持外部属性。 它已被[触发器](concepts-pipeline-execution-triggers.md)取代。
- 当前版本不支持策略和可用性属性。 管道的开始时间取决于[触发器](concepts-pipeline-execution-triggers.md)。
- 当前版本不支持指定了作用域的数据集（管道中定义的数据集）。

## <a name="next-steps"></a>后续步骤
请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明。

- [快速入门：使用 .NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [快速入门：使用 PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)
- [快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)
- [快速入门：使用 Azure 门户创建数据工厂](quickstart-create-data-factory-portal.md)
