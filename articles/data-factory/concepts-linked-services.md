---
title: 链接服务
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解 Azure 数据工厂和 Azure Synapse Analytics 中的链接服务。 链接服务可将计算和数据存储链接到服务。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 96d7792ee9c867263b7ab7f21cea652414f28478
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822389"
---
# <a name="linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的链接服务

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-create-datasets.md)
> * [当前版本](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了链接服务的涵义、采用 JSON 格式定义链接服务的方式，以及链接服务在 Azure 数据工厂和 Azure Synapse Analytics 中的用法。

有关详细信息，请阅读 [Azure 数据工厂](introduction.md)或 [Azure Synapse](../synapse-analytics/overview-what-is.md) 的简介文章。

## <a name="overview"></a>概述

Azure 数据工厂和 Azure Synapse Analytics 可以有一个或多个管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从 SQL Server 复制到 Azure Blob 存储。 然后，可使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理为生成输出数据。 最后，可再使用一个复制活动将输出数据复制到 Azure Synapse Analytics，在此基础上构建商业智能 (BI) 报告解决方案。 有关管道和活动的详细信息，请参阅[管道和活动](concepts-pipelines-activities.md)。

现在，数据集这一名称的意义已经变为看待数据的一种方式，就是以输入和输出的形式指向或引用活动中要使用的数据 。

在创建数据集之前，必须创建 **链接服务** 以将你的数据存储链接到数据工厂或 Synapse 工作区。 链接的服务类似于连接字符串，它定义服务连接到外部资源时所需的连接信息。 不妨这样考虑：数据集代表链接的数据存储中的数据结构，而链接服务则定义到数据源的连接。 例如，Azure 存储链接服务可将存储帐户链接到该服务。 Azure Blob 数据集表示该 Azure 存储帐户中包含要处理的输入 Blob 的 Blob 容器和文件夹。

下面是一个示例方案。 要将数据从 Blob 存储复制到 SQL 数据库，请创建以下两个链接服务：Azure 存储和 Azure SQL 数据库。 然后创建两个数据集：Azure Blob 数据集（即 Azure 存储链接服务）和 Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure 存储和 Azure SQL 数据库链接服务分别包含服务在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 Azure Blob 数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Blob 存储中的输入 blob。 Azure SQL 表数据集指定你的 SQL 数据库中要将数据复制到其中的 SQL 表。

下图显示了该服务中管道、活动、数据集和链接服务之间的关系：

![管道、活动、数据集和链接服务之间的关系](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>链接服务 JSON

链接服务采用 JSON 格式定义，如下所示：

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

下表描述了上述 JSON 中的属性：

属性 | 说明 | 必需 |
-------- | ----------- | -------- |
name | 链接服务的名称。 请参见[命名规则](naming-rules.md)。 |  是 |
type | 链接服务的类型。 例如：AzureBlobStorage（数据存储）或 AzureBatch（计算）。 请参阅 typeProperties 说明。 | 是 |
typeProperties | 每个数据存储或计算的类型属性各不相同。 <br/><br/> 有关支持的数据存储类型及其类型属性，请参阅[连接器概述](copy-activity-overview.md#supported-data-stores-and-formats)一文。 导航到数据存储连接器一文，了解特定于数据存储的类型属性。 <br/><br/> 有关支持的计算类型及其类型属性，请参阅[计算链接服务](compute-linked-services.md)。 | 是 |
connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 | 否

## <a name="linked-service-example"></a>链接服务示例

以下链接服务是 Azure Blob 存储链接服务。 请注意，类型设置为“Azure Blob 存储”。 Azure Blob 存储链接服务的类型属性包含连接字符串。 该服务使用此连接字符串在运行时连接到数据存储。

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>创建链接服务

在 Azure 数据工厂 UX 中通过[管理中心](author-management-hub.md)以及引用链接服务的任何活动、数据集或数据流创建链接服务。

你可以使用以下任一工具或 SDK 创建链接服务：[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、[Azure 资源管理器模板](quickstart-create-data-factory-resource-manager-template.md)和 [Azure 门户](quickstart-create-data-factory-portal.md)。


## <a name="data-store-linked-services"></a>数据存储链接的服务

有关支持的数据存储列表，可参阅[连接器概述](copy-activity-overview.md#supported-data-stores-and-formats)一文。 单击数据存储可了解支持的连接属性。

## <a name="compute-linked-services"></a>计算链接服务

要详细了解不同的配置以及可从该服务连接到的不同的计算环境，请参阅[支持的计算环境](compute-linked-services.md)。

## <a name="next-steps"></a>后续步骤

请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明。

- [快速入门：使用 .NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [快速入门：使用 PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)
- [快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)
- [快速入门：使用 Azure 门户创建数据工厂](quickstart-create-data-factory-portal.md)
