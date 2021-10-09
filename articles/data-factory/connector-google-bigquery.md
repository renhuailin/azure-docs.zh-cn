---
title: 从 Google BigQuery 复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动，将数据从 Google BigQuery 复制到支持的接收器数据存储。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: aa59787675870483941f271778bbf5b907467668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815139"
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从 Google BigQuery 存储复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂和 Synapse Analytics 管道中的复制活动从 Google BigQuery 复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Google BigQuery 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Google BigQuery 复制到任何支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

服务提供了内置驱动程序，用来启用连接。 因此，无需要手动安装驱动程序即可使用此连接器。

>[!NOTE]
>此 Google BigQuery 连接器在 BigQuery API 的基础上构建。 请注意，BigQuery 会限制传入请求的最大速率并按项目强制实施适当的配额，请参阅[配额和限制 - API 请求](https://cloud.google.com/bigquery/quotas#api_requests)。 请确保不会触发过多的帐户并发请求。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-google-bigquery-using-ui"></a>使用 UI 创建一个到 Google BigQuery 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Google BigQuery 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 Google 并选择 Google BigQuery 连接器。

    :::image type="content" source="media/connector-google-bigquery/google-bigquery-connector.png" alt-text="Google BigQuery 连接器的屏幕截图。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-google-bigquery/configure-google-bigquery-linked-service.png" alt-text="Google BigQuery 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Google BigQuery 连接器的实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Google BigQuery 链接服务支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **GoogleBigQuery**。 | 是 |
| project | 针对其查询的默认 BigQuery 项目的项目 ID。  | 是 |
| additionalProjects | 要访问的公共 BigQuery 项目的项目 ID 逗号分隔列表。  | 否 |
| requestGoogleDriveScope | 是否要请求访问 Google Drive。 允许 Google Drive 访问可支持将 BigQuery 数据与 Google Drive 中的数据组合的联合表。 默认值是 **false** 秒。  | 否 |
| authenticationType | 用于身份验证的 OAuth 2.0 身份验证机制。 ServiceAuthentication 只能在自承载集成运行时上使用。 <br/>允许的值为 **UserAuthentication** 和 **ServiceAuthentication**。 有关这些身份验证类型的其他属性和 JSON 示例，请分别参阅此表格下面的部分。 | 是 |

### <a name="using-user-authentication"></a>使用用户身份验证

将“authenticationType”属性设置为“UserAuthentication”  ，并指定以下属性及上节所述的泛型属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| clientId | 应用程序的 ID，用于生成刷新令牌。 | 否 |
| clientSecret | 应用程序的机密，用于生成刷新令牌。 将此字段标记为 SecureString 以安全地存储它，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |
| refreshToken | 从 Google 获得的刷新令牌，用于授权访问 BigQuery。 从[获取 OAuth 2.0 访问令牌](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens)和[此社区博客](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59)了解如何获取刷新令牌。 将此字段标记为 SecureString 以安全地存储它，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |

**示例：**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>使用服务身份验证

将“authenticationType”属性设置为“ServiceAuthentication”  ，并指定以下属性及上节所述的泛型属性。 此身份验证类型只能在自承载 Integration Runtime 上使用。

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| 电子邮件 | 用于 ServiceAuthentication 的服务帐户电子邮件 ID。 它只能在自承载集成运行时上使用。  | 否 |
| keyFilePath | .p12 密钥文件的完整路径，该文件用于对服务帐户电子邮件地址进行身份验证。 | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 TLS 进行连接时用来验证服务器）的 .pem 文件的完整路径。 仅当在自承载集成运行时上使用 TLS 时，才能设置此属性。 默认值是随集成运行时一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 .pem 文件中的 CA 证书。 默认值是 **false** 秒。  | 否 |

**示例：**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Google BigQuery 数据集支持的属性列表。

要从 Google BigQuery 复制数据，请将数据集的 type 属性设置为 **GoogleBigQueryObject**。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**GoogleBigQueryObject** | 是 |
| dataset | Google BigQuery 数据集的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表的名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 表的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `dataset` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Google BigQuery 源类型支持的属性列表。

### <a name="googlebigquerysource-as-a-source-type"></a>以 GoogleBigQuerySource 作为源类型

要从 Google BigQuery 复制数据，请将复制活动中的源类型设置为“GoogleBigQuerySource”  。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **GoogleBigQuerySource**。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
