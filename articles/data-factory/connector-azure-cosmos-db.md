---
title: 在 Azure Cosmos DB (SQL API) 中复制和转换数据
description: 了解如何使用数据工厂向/从 Azure Cosmos DB (SQL API) 复制数据，以及如何在 Azure Cosmos DB (SQL API) 中转换数据。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/18/2021
ms.openlocfilehash: 36fae5b71e9aa5c2c6c252ad1aa306bb64d9aecb
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110480073"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure Cosmos DB (SQL API) 中复制和转换数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-azure-documentdb-connector.md)
> * [当前版本](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

这篇文章概述了如何使用 Azure 数据工厂中的“复制活动”功能从/向 Azure Cosmos DB (SQL API) 复制数据，并使用数据流转换 Azure Cosmos DB (SQL API) 中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。



>[!NOTE]
>此连接器仅支持 Cosmos DB SQL API。 有关 MongoDB API，请参阅[适用于 MongoDB 的 Auzre Cosmos DB API 的连接器](connector-azure-cosmos-db-mongodb-api.md)。 目前不支持其他 API 类型。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Cosmos DB (SQL API) 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

对于复制活动，此 Azure Cosmos DB(SQL API) 连接器支持：

- 使用密钥身份验证、服务主体身份验证或 Azure 资源的托管标识身份验证，将数据从 Azure Cosmos DB [SQL API](../cosmos-db/introduction.md) 进行复制以及复制到其中。
- 以 **insert** 或 **upsert** 的形式写入 Azure Cosmos DB。
- 按原样导入和导出 JSON 文档，或在表格数据集中复制或粘贴数据。 示例包括 SQL 数据库和 CSV 文件。 若要在 JSON 文件或另一个 Azure Cosmos DB 集合中按原样复制或粘贴文档，请参阅[导入和导出 JSON 文档](#import-and-export-json-documents)。

数据工厂与 [Azure Cosmos DB 批量执行程序库](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)集成，以便在写入 Azure Cosmos DB 时提供最佳性能。

> [!TIP]
> [数据迁移视频](https://youtu.be/5-SRNiC_qOU)将指导你完成将数据从 Azure Blob 存储复制到 Azure Cosmos DB 的步骤。 该视频还介绍了在一般情况下将数据引入 Azure Cosmos DB 的性能优化注意事项。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Cosmos DB (SQL API) 的数据工厂实体，以下部分提供有关可用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Cosmos DB (SQL API) 连接器支持以下身份验证类型。 请参阅相应部分的了解详细信息：

- [密钥身份验证](#key-authentication)
- [服务主体身份验证（预览版）](#service-principal-authentication)
- [Azure 资源的托管标识身份验证（预览版）](#managed-identity)

### <a name="key-authentication"></a>密钥身份验证

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **CosmosDb**。 | 是 |
| connectionString |指定连接 Azure Cosmos DB 数据库所需的信息。<br />**注意**：必须如以下示例所示，在连接字符串中指定数据库信息。 <br/> 还可以将帐户密钥放在 Azure 密钥保管库中，并从连接字符串中拉取 `accountKey` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

**示例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储帐户密钥**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication-preview"></a><a name="service-principal-authentication"></a> 服务主体身份验证（预览版）

>[!NOTE]
>目前，不支持在数据流中进行服务主体身份验证。

若要使用服务主体身份验证，请按照以下步骤操作。

1. 遵循[将应用程序注册到 Azure AD 租户](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，在 Azure Active Directory (Azure AD) 中注册一个应用程序实体。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当权限。 有关 Cosmos DB 中的权限工作原理的示例，请参阅[文件和目录上的访问控制列表](../cosmos-db/how-to-setup-rbac.md)。 更具体地说，请先创建角色定义，然后通过服务主体对象 ID 将角色分配给服务主体。 

链接服务支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **CosmosDb**。 |是 |
| accountEndpoint | 指定 Azure Cosmos DB 的帐户终结点 URL。 | 是 |
| database | 指定数据库的名称。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalCredentialType | 要用于服务主体身份验证的凭据类型。 允许的值为“ServicePrincipalKey”和“ServicePrincipalCert” 。 | 是 |
| servicePrincipalCredential | 服务主体凭据。 <br/> 使用“ServicePrincipalKey”作为凭据类型时，请指定应用程序的密钥。 将此字段标记为 SecureString 以将其安全地存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 <br/> 使用“ServicePrincipalCert”作为凭据时，请引用 Azure Key Vault 中的证书。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |
| azureCloudType | 对于服务主体身份验证，请指定 Azure Active Directory 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为“AzurePublic”、“AzureChina”、“AzureUsGovernment”和“AzureGermany”。 默认情况下，使用数据工厂的云环境。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：使用服务主体密钥身份验证**

也可以将服务主体密钥存储在 Azure Key Vault 中。

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：使用服务主体证书身份验证**
```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication-preview"></a><a name="managed-identity"></a> Azure 资源的托管标识身份验证（预览版）

>[!NOTE]
>目前，不支持在数据流中进行托管标识身份验证。

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用你自己的服务主体一样，直接使用此托管标识进行 Cosmos DB 身份验证。 此指定工厂可通过此方法访问以及向/从 Cosmos DB 复制数据。

若要使用 Azure 资源托管标识身份验证，请按照以下步骤操作。

1. 通过复制与工厂一起生成的 **托管标识对象 ID** 的值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 向托管标识授予适当权限。 有关 Cosmos DB 中的权限工作原理的示例，请参阅[文件和目录上的访问控制列表](../cosmos-db/how-to-setup-rbac.md)。 更具体地说，请先创建角色定义，然后将角色分配给托管标识。

链接服务支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **CosmosDb**。 |是 |
| accountEndpoint | 指定 Azure Cosmos DB 的帐户终结点 URL。 | 是 |
| database | 指定数据库的名称。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。

Azure Cosmos DB (SQL API) 数据集支持以下属性： 

| properties | 说明 | 必须 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **CosmosDbSqlApiCollection**。 |是 |
| collectionName |Azure Cosmos DB 文档集合的名称。 |是 |

如果你使用“DocumentDbCollection”类型的数据集，则复制和查找活动仍按原样支持它以提供后向兼容性，但数据流不支持它。 建议你今后使用新模型。

**示例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分列出了Azure Cosmos DB (SQL API) 源和接收器支持的属性。 有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) 用作源

若要从 Azure Cosmos DB (SQL API) 复制数据，请将复制活动中的 **source** 类型设置为 **DocumentDbCollectionSource**。 

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **CosmosDbSqlApiSource**。 |是 |
| query |指定要读取数据的 Azure Cosmos DB 查询。<br/><br/>示例：<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，则执行此 SQL 语句：`select <columns defined in structure> from mycollection` |
| preferredRegions | 从 Cosmos DB 检索数据时要连接到的区域的首选列表。 | 否 |
| pageSize | 查询结果的每页文档数。 默认值为“-1”，表示使用服务端动态页大小，最大为 1000。 | 否 |
| detectDatetime | 是否从文档中的字符串值中检测日期时间。 允许的值是：true（默认）、false。 | 否 |

如果使用“DocumentDbCollectionSource”类型的源，则仍按原样提供支持以实现后向兼容性。 建议今后使用新模型，新模型提供了更丰富的功能来从 Cosmos DB 复制数据。

**示例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

从 Cosmos DB 复制数据时，除非你想[原样导出 JSON 文档](#import-and-export-json-documents)，否则，最佳做法是在复制活动中指定映射。 数据工厂遵循你在活动上指定的映射 - 如果某个行的某个列中未包含值，则会为列值提供 null 值。 如果未指定映射，则数据工厂将使用数据中的第一行来推断架构。 如果第一行不包含完整架构，则活动操作的结果中将丢失部分列。

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) 用作接收器

若要将数据复制到 Azure Cosmos DB (SQL API)，请将复制活动中的 **sink** 类型设置为 **DocumentDbCollectionSink**。 

复制活动 **sink** 节支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **CosmosDbSqlApiSink**。 |是 |
| writeBehavior |描述如何将数据写入 Azure Cosmos DB。 允许的值为 **insert** 和 **upsert**。<br/><br/>**upsert** 的行为是，如果已存在具有相同 ID 的文档，则替换该文档；否则将插入该文档。<br /><br />**注意**：如果未在原始文档中指定 ID，或未通过列映射指定 ID，则数据工厂会自动为文档生成 ID。 这表示必须先确保文档有 ID，才能让 **upsert** 按预期工作。 |否<br />（默认值为 **insert**） |
| writeBatchSize | 数据工厂使用 [Azure Cosmos DB 批量执行程序库](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)将数据写入 Azure Cosmos DB。 **writeBatchSize** 属性控制 ADF 提供给库的文档的大小。 可尝试增加 writeBatchSize 的值以提高性能，并在文档大小较大时降低该值 - 请参阅下面的提示。 |否<br />（默认值为 **10,000**） |
| disableMetricsCollection | 数据工厂收集指标（如 Cosmos DB RU），以获取复制性能优化和建议。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |


>[!TIP]
>若要按原样导入 JSON 文档，请参阅[导入或导出 JSON 文档](#import-and-export-json-documents)部分；若要从表格形数据复制，请参阅[从关系数据库迁移到 Cosmos DB](#migrate-from-relational-database-to-cosmos-db)。

>[!TIP]
>Cosmos DB 将单个请求的大小限制为 2MB。 公式为请求大小 = 单个文档大小 * 写入批大小。 若出现“请求太大。”错误，请减少复制接收器配置中的 `writeBatchSize` 值。

如果使用“DocumentDbCollectionSink”类型的源，则仍按原样提供支持以实现后向兼容性。 建议今后使用新模型，新模型提供了更丰富的功能来从 Cosmos DB 复制数据。

**示例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>架构映射

要将数据从 Azure Cosmos DB 复制到表格接收器或进行反向复制，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以在 Cosmos DB 的集合中读取和写入数据。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

特定于 Azure Cosmos DB 的设置可在源转换的“源选项”选项卡中找到。 

包括系统列：如果为 true，则会将 ```id```、```_ts``` 和其他系统列包括在 CosmosDB 的数据流元数据中。 更新集合时，必须包括此项，以便能够获取现有行 ID。

页面大小：查询结果的每页文档数。 默认值为“-1”，表示使用服务动态页，最大为 1000。

吞吐量：为读取操作期间每次执行此数据流时要应用于 CosmosDB 集合的 RU 数设置一个可选值。 最小值为 400。

首选区域：选择此进程的首选读取区域。

#### <a name="json-settings"></a>JSON 设置

单个文档：如果 ADF 会将整个文件视为单个 JSON 文档，请选择此选项。

不带引号的列名称：如果 JSON 中的列名称未加引号，请选择此选项。

具有注释：如果 JSON 文档的数据中包含注释，请使用此选项。

带单引号：如果文档中的列和值加了单引号，则应选择此选项。

使用反斜杠转义：如果使用反斜杠对 JSON 中的字符进行转义，请选择此选项。

### <a name="sink-transformation"></a>接收器转换

特定于 Azure Cosmos DB 的设置可在接收器转换的“设置”选项卡中找到。

**更新方法：** 确定数据库目标上允许哪些操作。 默认设置为仅允许插入。 若要更新、更新插入或删除行，需要进行 alter-row 转换才能标记这些操作的行。 对于更新、更新插入和删除操作，必须设置一个或多个键列，以确定要更改的行。

集合操作：确定在写入之前是否重新创建目标集合。
* 无：不会对集合执行任何操作。
* 重新创建：将删除集合并重新创建集合

**批大小**：一个整数，表示每个批中有多少对象被写入 Cosmos DB 集合。 通常，从默认批大小开始就足够了。 若要进一步调整此值，请注意：

- Cosmos DB 将单个请求的大小限制为 2MB。 公式为“请求大小 = 单个文档大小 * 写入批大小”。 若出现“请求太大”错误，请减少批大小值。
- 批大小越大，ADF 可以实现的吞吐量就越好，同时请确保分配足够的 RU 来授权工作负载。

**分区键：** 输入表示集合的分区键的字符串。 示例： ```/movies/title```

吞吐量：为每次执行此数据流时要应用于 CosmosDB 集合的 RU 数设置一个可选值。 最小值为 400。

**写入吞吐量预算：** 一个整数，表示要分配给该集合的总吞吐量中要为此数据流写入操作分配的 RU。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="import-and-export-json-documents"></a>导入和导出 JSON 文档

使用此 Azure Cosmos DB(SQL API) 连接器，可以轻松地：

* 在两个 Azure Cosmos DB 集合之间按原样复制文档。
* 将各种源（包括 Azure Blob 存储、Azure Data Lake Store 或 Azure 数据工厂所支持的其他基于文件的存储）中的 JSON 文档导入 Azure Cosmos DB。
* 将 JSON 文档从 Azure Cosmos DB 集合导出到各种基于文件的存储。

若要实现“架构不可知”复制，请执行以下操作：

* 使用复制数据工具时，选择“原样导出到 JSON 文件或 Cosmos DB 集合”选项。
* 使用活动创作时，请为源或接收器选择 JSON 格式以及相应的文件存储。

## <a name="migrate-from-relational-database-to-cosmos-db"></a>从关系数据库迁移到 Cosmos DB

从关系数据库（例如 SQL Server）迁移到 Azure Cosmos DB 时，复制活动可以轻松地从源映射表格数据，以在 Cosmos DB 中平展 JSON 文档。 某些情况下，你可能希望根据 [Azure Cosmos DB 中的数据建模](../cosmos-db/modeling-data.md)重新设计数据模型，以便来针对 NoSQL 用例对其进行优化，例如，通过将所有相关子项嵌入到一个 JSON 文档中来使数据非规范化。 对于这种情况，请参阅[此文](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md)，其中演练了如何使用 Azure 数据工厂复制活动实现此目的。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。