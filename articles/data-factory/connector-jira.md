---
title: 从 Jira 复制数据
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动，将数据从 Jira 复制到支持的接收器数据存储。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 55326ecbc0ad6283503358a0b34d2fba6d4bc2dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827303"
---
# <a name="copy-data-from-jira-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从 Jira 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂或 Synapse Analytics 管道中的复制活动从 Jira 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Jira 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Jira 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

该服务提供用于启用连接的内置驱动程序，因此使用此连接器无需手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-jira-using-ui"></a>使用 UI 创建到 Jira 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Jira 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡，并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“Jira”并选择 Jira 连接器。

   :::image type="content" source="media/connector-jira/jira-connector.png" alt-text="选择 Jira 连接器。":::    


1. 配置服务详细信息、测试连接并创建新的链接服务。

   :::image type="content" source="media/connector-jira/configure-jira-linked-service.png" alt-text="配置到 Jira 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Jira 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Jira 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Jira** | 是 |
| host | Jira 服务的 IP 地址或主机名。 （例如，jira.example.com）  | 是 |
| port | Jira 服务器用来侦听客户端连接的 TCP 端口。 默认值为 443（如果通过 HTTPS 进行连接）或 8080（如果通过 HTTP 进行连接）。  | 否 |
| username | 用于访问 Jira 服务的用户名。  | 是 |
| password | 在“用户名”字段中提供的用户名所对应的密码。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 TLS 进行连接时是否要求服务器证书中的主机名与服务器的主机名匹配。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 TLS 进行连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**示例：**

```json
{
    "name": "JiraLinkedService",
    "properties": {
        "type": "Jira",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Jira 数据集支持的属性列表。

要从 Jira 复制数据，请将数据集的 type 属性设置为“JiraObject”  。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**JiraObject** | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "JiraDataset",
    "properties": {
        "type": "JiraObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Jira linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Jira 数据源支持的属性列表。

### <a name="jirasource-as-source"></a>以 JiraSource 作为源

要从 Jira 复制数据，请将复制活动中的源类型设置为“JiraSource”  。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：**JiraSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromJira",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Jira input dataset name>",
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
                "type": "JiraSource",
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
