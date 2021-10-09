---
title: 从 OData 源复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动，将数据从 OData 源复制到支持的接收器数据存储。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 938693353505a7fb3d37c85234da429ed2367ad4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787881"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从 OData 源复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-odata-connector.md)
> * [当前版本](connector-odata.md)

本文概述如何在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动从 OData 源复制数据。 本文是基于概括性介绍复制活动的[复制活动](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 OData 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 OData 源复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具体而言，此 OData 连接器支持：

- OData 3.0 和 4.0 版。
- 使用以下某种身份验证复制数据：**匿名**、**基本**、**Windows** 和 **AAD 服务主体**。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-odata-store-using-ui"></a>使用 UI 创建到 OData 存储的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 OData 存储的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索“OData”，然后选择“OData”连接器。

    :::image type="content" source="media/connector-odata/odata-connector.png" alt-text="OData 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-odata/configure-odata-linked-service.png" alt-text="OData 存储的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 OData 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

OData 链接的服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 OData   。 |是 |
| url | OData 服务的根 URL。 |是 |
| authenticationType | 用于连接 OData 源的身份验证类型。 允许的值为“Anonymous”  、“Basic”  、“Windows”  和“AadServicePrincipal”  。 不支持基于用户的 OAuth。 此外，还可以在 `authHeader` 属性中配置身份验证标头。| 是 |
| authHeaders | 附加的用于身份验证的 HTTP 请求标头。<br/> 例如，若要使用 API 密钥身份验证，可以将身份验证类型选为“匿名”，然后在标头中指定 API 密钥。 | 否 |
| userName | 如果使用 Basic 或 Windows 身份验证，请指定用户名  。 | 否 |
| password | 指定为 userName 指定的用户帐户的密码   。 将此字段标记为 SecureString 类型以将其安全存储。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalId | 指定 Azure Active Directory 应用程序的客户端 ID。 | 否 |
| aadServicePrincipalCredentialType | 指定要用于服务主体身份验证的凭据类型。 允许值为：`ServicePrincipalKey` 或 `ServicePrincipalCert`。 | 否 |
| servicePrincipalKey | 指定 Azure Active Directory 应用程序的密钥。 将此字段标记为 SecureString 以安全地存储它，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalEmbeddedCert | 指定 Azure Active Directory 中注册的应用程序的 base64 编码证书。 请将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalEmbeddedCertPassword | 如果使用密码保护证书，请指定证书的密码。 请将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。  | 否|
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 否 |
| aadResourceId | 指定你请求授权的 AAD 资源。| 否 |
| azureCloudType | 对于服务主体身份验证，请指定 AAD 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为 AzurePublic、AzureChina、AzureUsGovernment 和 AzureGermany   。 默认情况下，使用服务的云环境。 | 否 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例 1：使用匿名身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：使用基本身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**示例 3：使用 Windows 身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**示例 4：使用服务主体密钥身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**示例 5：使用服务主体证书身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**示例 6：使用 API 密钥身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
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

本部分提供 OData 数据集支持的属性列表。

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 

要从 OData 复制数据，请将数据集的 type 属性设置为“ODataResource”   。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 ODataResource   。 | 是 |
| path | OData 资源的路径。 | 是 |

**示例**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 OData 源支持的属性列表。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="odata-as-source"></a>以 OData 作为源

从 OData 复制数据时，复制活动的 **source** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 ODataSource   。 | 是 |
| query | 用于筛选数据的 OData 查询选项。 示例：`"$select=Name,Description&$top=5"`。<br/><br/>**注意**：OData 连接器会从以下组合 URL 复制数据：`[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`。 有关详细信息，请参阅 [OData URL 组件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 （TimeSpan 值）  。 该值是获取响应而不是读取响应数据的超时。 如果未指定，默认值为“00:30:00”（30 分钟）。 | 否 |

**示例**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用 `RelationalSource` 类型源，该源仍按原样受支持，但我们建议今后使用新源。

## <a name="data-type-mapping-for-odata"></a>OData 的数据类型映射

从 OData 复制数据时，会在 OData 数据类型和服务内部使用的临时数据类型之间使用以下映射。 要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| OData 数据类型 | 临时服务数据类型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | 小数 |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | 字符串 |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> 不支持 OData 复杂数据类型，例如对象。

## <a name="copy-data-from-project-online"></a>从 Project Online 复制数据

若要从 Project Online 复制数据，可以使用 OData 连接器以及从 Postman 之类的工具获取的访问令牌。

> [!CAUTION]
> 默认情况下，访问令牌将在 1 小时后过期，需要在过期后获取新的访问令牌。

1. 使用 Postman 获取访问令牌：

   1. 导航到 Postman 网站上的“授权”选项卡。
   1.    在“类型”框中选择“OAuth 2.0”，并在“将授权数据添加到”框中选择“请求标头”。
   1. 在“配置新令牌”页中填写以下信息以获取新的访问令牌： 
      - **授权类型**：选择“授权代码”。
      - **回调 URL**：输入 `https://www.localhost.com/`。 
      - **身份验证 URL**：输入 `https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com`。 将 `<your tenant name>` 替换为自己的租户名称。 
      - **访问令牌 URL**：输入 `https://login.microsoftonline.com/common/oauth2/token`。
      - **客户端 ID**：输入你的 AAD 服务主体 ID。
      - **客户端密码**：输入服务主体机密。
      - **客户端身份验证**：选择“作为‘基本身份验证标头’发送”。
     
   1. 系统会要求你用用户名和密码进行登录。
   1. 获取访问令牌后，请复制并保存它以供下一步使用。
   
    [:::image type="content" source="./media/connector-odata/odata-project-online-postman-access-token-expanded.png#lightbox" alt-text="使用 Postman 获取访问令牌](./media/connector-odata/odata-project-online-postman-access-token-inline.png)":::

1. 创建 OData 链接服务：
    - **服务 URL**：输入 `https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata`。 将 `<your tenant name>` 替换为自己的租户名称。 
    - **身份验证类型**：选择“匿名”。
    - **身份验证头**：
        - **属性名称**：选择“授权”。
        - **值**：输入 `Bearer <access token from step 1>`。
    - 测试链接服务。

    :::image type="content" source="./media/connector-odata/odata-project-online-linked-service.png" alt-text="创建 OData 链接服务":::

1. 创建 OData 数据集：
    1. 使用步骤 2 中创建的 OData 链接服务来创建数据集。
    1. 预览数据。
 
    :::image type="content" source="./media/connector-odata/odata-project-online-preview-data.png" alt-text="预览数据":::
 


## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。