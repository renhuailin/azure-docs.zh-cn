---
title: 向/从 Azure 数据资源管理器复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动来向/从 Azure 数据资源管理器复制数据。
ms.author: susabat
author: ssabat
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: 4f3718699e7438b3b45c84eebebbbbf75126d793
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304520"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure 数据资源管理器复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍如何在 Azure 数据工厂中使用复制活动向/从 [Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview)复制数据。 本文是在总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文的基础之上编写的。

>[!TIP]
>通常，对于 Azure 数据工厂和 Azure 数据资源管理器的集成，请从[将 Azure 数据资源管理器与 Azure 数据工厂集成](/azure/data-explorer/data-factory-integration)了解更多信息。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure 数据资源管理器连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从任何受支持的源数据存储复制到 Azure 数据资源管理器。 可以将数据从 Azure 数据资源管理器复制到任何受支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

>[!NOTE]
>版本 3.14 和更高版本支持使用自承载集成运行时通过本地数据存储向/从 Azure 数据资源管理器复制数据。

使用 Azure 数据资源管理器连接器可执行以下操作：

* 将 Azure Active Directory (Azure AD) 应用程序令牌身份验证与 **服务主体** 配合使用来复制数据。
* 作为源时，请使用 KQL (Kusto) 查询来检索数据。
* 作为接收器时，请将数据追加到目标表。

## <a name="getting-started"></a>入门

>[!TIP]
>有关 Azure 数据资源管理器连接器的演练，请参阅[使用 Azure 数据工厂向/从 Azure 数据资源管理器复制数据](/azure/data-explorer/data-factory-load-data)和[从数据库大容量复制到 Azure 数据资源管理器](/azure/data-explorer/data-factory-template)。

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-data-explorer-using-ui"></a>使用 UI 创建指向 Azure 数据资源管理器的链接服务

使用以下步骤在 Azure 门户 UI 中创建指向 Azure 数据资源管理器的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索资源管理器并选择 Azure 数据资源管理器 (Kusto) 连接器。

    :::image type="content" source="media/connector-azure-data-explorer/azure-data-explorer-connector.png" alt-text="Azure 数据资源管理器 (Kusto) 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-azure-data-explorer/configure-azure-data-explorer-linked-service.png" alt-text="Azure 数据资源管理器的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Azure 数据资源管理器连接器的实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure 数据资源管理器连接器支持以下身份验证类型。 请参阅相应部分的了解详细信息：

- [服务主体身份验证](#service-principal-authentication)
- [系统分配的托管标识身份验证](#managed-identity)
- [用户分配的托管标识身份验证](#user-assigned-managed-identity-authentication)

### <a name="service-principal-authentication"></a>服务主体身份验证

若要使用服务主体身份验证，请按照以下步骤获取服务主体并授予权限：

1. 遵循[将应用程序注册到 Azure AD 租户](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)中的步骤在 Azure Active Directory 中注册一个应用程序实体。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 在 Azure 数据资源管理器中为服务主体授予正确的权限。 有关角色和权限以及管理权限的详细信息，请参阅[管理 Azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)。 一般情况下，必须授予以下权限：

    - **作为源**：至少向数据库授予“数据库查看者”角色 
    - **作为接收器**：至少向数据库授予“数据库引入者”角色 

>[!NOTE]
>使用数据工厂 UI 创作时，登录用户帐户默认用于列出 Azure 数据资源管理器群集、数据库和表。 可以选择使用服务主体列出对象，方法是单击“刷新”按钮旁边的下拉列表，或者手动输入名称（如果没有这些操作的权限）。

Azure 数据资源管理器链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureDataExplorer**。 | 是 |
| endpoint | Azure 数据资源管理器群集的终结点 URL，格式为 `https://<clusterName>.<regionName>.kusto.windows.net`。 | 是 |
| database | 数据库的名称。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 此 ID 在 [Kusto 连接字符串](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中称为“颁发机构 ID”。 将鼠标指针悬停在 Azure 门户右上角进行检索。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 此 ID 在[Kusto 连接字符串](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中称为“AAD 应用程序客户端 ID”。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 此密钥在[Kusto 连接字符串](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中称为“AAD 应用程序密钥”。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中，或 [引用存储在 Azure Key Vault 中的安全数据](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：使用服务主体密钥身份验证**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a>系统分配的托管标识身份验证

若要详细了解 Azure 资源托管标识，请参阅 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

若要使用系统分配的托管标识身份验证，请按照以下步骤授予权限：

1. 通过复制与工厂一起生成的 **托管标识对象 ID** 的值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 在 Azure 数据资源管理器中向托管标识授予正确的权限。 有关角色和权限以及管理权限的详细信息，请参阅[管理 Azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)。 一般情况下，必须授予以下权限：

    - **作为源**：至少向数据库授予“数据库查看者”角色 
    - **作为接收器**：至少向数据库授予“数据库引入者”角色 

>[!NOTE]
>使用数据工厂 UI 创作时，登录用户帐户用于列出 Azure 数据资源管理器群集、数据库和表。 如果你没有权限执行这些操作，请手动输入名称。

Azure 数据资源管理器链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureDataExplorer**。 | 是 |
| endpoint | Azure 数据资源管理器群集的终结点 URL，格式为 `https://<clusterName>.<regionName>.kusto.windows.net`。 | 是 |
| database | 数据库的名称。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

示例：使用系统分配的托管标识身份验证

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>用户分配的托管标识身份验证
若要详细了解 Azure 资源托管标识，请参阅 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)

若要使用用户分配的托管标识身份验证，请按照以下步骤操作：

1. [创建一个或多个用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)，并在 Azure 数据资源管理器中授予权限。 有关角色和权限以及管理权限的详细信息，请参阅[管理 Azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)。 一般情况下，必须授予以下权限：

    - **作为源**：至少向数据库授予“数据库查看者”角色 
    - **作为接收器**：至少向数据库授予“数据库引入者”角色 
     
2. 为数据工厂分配一个或多个用户分配的托管标识，并为每个用户分配的托管标识[创建凭据](data-factory-service-identity.md#credentials)。

Azure 数据资源管理器链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureDataExplorer**。 | 是 |
| endpoint | Azure 数据资源管理器群集的终结点 URL，格式为 `https://<clusterName>.<regionName>.kusto.windows.net`。 | 是 |
| database | 数据库的名称。 | 是 |
| 凭据 | 将用户分配的托管标识指定为凭据对象。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

示例：使用用户分配的托管标识身份验证
```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅 [Azure 数据工厂中的数据集](concepts-datasets-linked-services.md)。 本部分列出了 Azure 数据资源管理器数据集支持的属性。

若要将数据复制到 Azure 数据资源管理器，请将数据集的 type 属性设置为 **AzureDataExplorerTable**。

支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureDataExplorerTable**。 | 是 |
| 表 | 链接服务引用的表的名称。 | 对于接收器为必需的，对于源不是必需的 |

**数据集属性示例：**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅 [Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)。 本部分提供了 Azure 数据资源管理器源和接收器支持的属性列表。

### <a name="azure-data-explorer-as-source"></a>Azure 数据资源管理器作为源

若要从 Azure 数据资源管理器复制数据，请将复制活动源中的 **type** 属性设置为 **AzureDataExplorerSource**。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为：**AzureDataExplorerSource** | 是 |
| 查询 | 以 [KQL 格式](/azure/kusto/query/)指定的只读请求。 使用自定义 KQL 查询作为参考。 | 是 |
| queryTimeout | 查询请求超时前的等待时间。默认值是 10 分钟 (00:10:00)；允许的最大值是 1 小时 (01:00:00)。 | 否 |
| noTruncation | 指示是否截断返回的结果集。 默认情况下，结果在出现 500,000 条记录或达到 64 MB 之后将被截断。 强烈建议将其截断，以确保活动的正确行为。 |否 |

>[!NOTE]
>默认情况下，Azure 数据资源管理器源的大小限制为 500,000 条记录或 64 MB。 若要检索所有记录而不截断，可以在查询的开头指定 `set notruncation;`。 有关详细信息，请参阅[查询限制](/azure/kusto/concepts/querylimits)。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure 数据资源管理器作为接收器

若要将数据复制到 Azure 数据资源管理器，请将复制活动接收器中的 type 属性设置为 **AzureDataExplorerSink**。 复制活动接收器部分中支持以下属性  ：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为：**AzureDataExplorerSink**。 | 是 |
| ingestionMappingName | 基于 Kusto 表预先创建的[映射](/azure/kusto/management/mappings#csv-mapping)的名称。 若要将源中的列映射到 Azure 数据资源管理器（适用于[所有支持的源存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)，包括 CSV/JSON/Avro 格式），可以使用复制活动[列映射](copy-activity-schema-and-type-mapping.md)（按名称隐式映射或按配置显式映射）和/或 Azure 数据资源管理器映射。 | 否 |
| additionalProperties | 一个属性包，可用于指定 Azure 数据资源管理器接收器尚未设置的任何引入属性。 具体来说，它可用于指定引入标记。 从 [Azure 数据资源管理器数据引入文档](/azure/data-explorer/ingestion-properties)了解更多信息。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>查找活动属性

有关属性的详细信息，请参阅[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

* 详细了解如何[将数据从 Azure 数据工厂复制到 Azure 数据资源管理器](/azure/data-explorer/data-factory-load-data)。