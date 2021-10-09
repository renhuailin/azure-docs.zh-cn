---
title: 从 Spark 复制数据
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动，将数据从 Spark 复制到支持的接收器数据存储。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 434dacd87eacf670753cff9145271d9e4770981d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763721"
---
# <a name="copy-data-from-spark-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从 Spark 复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂或 Synapse Analytics 管道中的复制活动从 Spark 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Spark 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Spark 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

该服务提供用于启用连接的内置驱动程序，因此使用此连接器无需手动安装任何驱动程序。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-spark-using-ui"></a>使用 UI 创建一个到 Spark 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Spark 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 Spark 并选择 Spark 连接器。

   :::image type="content" source="media/connector-spark/spark-connector.png" alt-text="Spark 连接器的屏幕截图。":::    


1. 配置服务详细信息，测试连接，然后创建新的链接服务。

   :::image type="content" source="media/connector-spark/configure-spark-linked-service.png" alt-text="Spark 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Spark 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Spark 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Spark** | 是 |
| host | Spark 服务器的 IP 地址或主机名  | 是 |
| port | Spark 服务器用来侦听客户端连接的 TCP 端口。 如果连接到 Azure HDInsights，请指定端口 443。 | 是 |
| serverType | Spark 服务器的类型。 <br/>允许值包括：SharkServer、SharkServer2、SparkThriftServer    | 否 |
| thriftTransportProtocol | Thrift 层中要使用的传输协议。 <br/>允许值包括：二进制、SASL、HTTP | 否 |
| authenticationType | 用于访问 Spark 服务器的身份验证方法。 <br/>允许值包括：Anonymous、Username、UsernameAndPassword、WindowsAzureHDInsightService     | 是 |
| username | 用于访问 Spark 服务器的用户名。  | 否 |
| password | 用户所对应的密码。 将此字段标记为 SecureString 以安全地存储它，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |
| httpPath | 对应于 Spark 服务器的部分 URL。  | 否 |
| enableSsl | 指定是否使用 TLS 加密到服务器的连接。 默认值为 false。  | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 TLS 进行连接时用来验证服务器）的 .pem 文件的完整路径。 只有在自承载 IR 上使用 TLS 时才能设置此属性。 默认值是随 IR 一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 默认值为 false。  | 否 |
| allowHostNameCNMismatch | 指定通过 TLS 进行连接时是否要求 CA 颁发的 TLS/SSL 证书名称与服务器的主机名相匹配。 默认值为 false。  | 否 |
| allowSelfSignedServerCert | 指定是否允许来自服务器的自签名证书。 默认值为 false。  | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 在[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Spark 数据集支持的属性列表。

要从 Spark 复制数据，请将数据集的 type 属性设置为“SparkObject”  。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：SparkObject  | 是 |
| schema | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表的名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Spark 数据源支持的属性列表。

### <a name="spark-as-source"></a>Spark 作为源

要从 Spark 复制数据，请将复制活动中的源类型设置为“SparkSource”  。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：SparkSource  | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
