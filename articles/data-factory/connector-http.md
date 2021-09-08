---
title: 从 HTTP 源复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Azure Synapse Analytics 管道中使用复制活动，将数据从云或本地 HTTP 源复制到支持的接收器数据存储。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: 4463c3ffb8deaf0a88b978b39f3a52c83f3f8304
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303856"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory-or-azure-synapse-analytics"></a>使用 Azure 数据工厂或 Azure Synapse Analytics 从 HTTP 终结点复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-http-connector.md)
> * [当前版本](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂和 Azure Synapse 中的复制活动从 HTTP 终结点复制数据。 本文是根据[复制活动](copy-activity-overview.md)一文（其中大致介绍了复制活动）编写的。

此 HTTP 连接器、[REST 连接器](connector-rest.md)和 [Web 表连接器](connector-web-table.md)之间的区别如下：

- **REST 连接器** 专门支持从 RESTful API 复制数据。 
- **HTTP 连接器** 是通用的，可从任何 HTTP 终结点检索数据，以执行文件下载等操作。 在 REST 连接器可用之前，你可能偶然使用 HTTP 连接器从 RESTful API 复制数据，这是受支持的，但与 REST 连接器相比功能较少。
- **Web 表连接器** 用于从 HTML 网页中提取表内容。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 HTTP 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 HTTP 源复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

可以使用此 HTTP 连接器：

- 通过 HTTP GET 或 POST 方法，从 HTTP/S 终结点检索数据   。
- 使用以下身份验证之一检索数据：**Anonymous**、**Basic**、**Digest**、**Windows** 或 **ClientCertificate**。
- 按原样复制 HTTP 响应，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析该响应。

> [!TIP]
> 若要先测试数据检索的 HTTP 请求，再配置 HTTP 连接器，请了解标头和正文的 API 规范要求。 可以使用 Postman 或 Web 浏览器等工具进行验证。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-http-source-using-ui"></a>使用 UI 创建指向 HTTP 源的链接服务

使用以下步骤在 Azure 门户 UI 中创建指向 HTTP 源的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 HTTP 并选择 HTTP 连接器。

    :::image type="content" source="media/connector-http/http-connector.png" alt-text="HTTP 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-http/configure-http-linked-service.png" alt-text="HTTP 链接服务的配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 HTTP 连接器的实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

HTTP 链接的服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 HttpServer   。 | 是 |
| url | Web 服务器的基 URL。 | 是 |
| enableServerCertificateValidation | 指定连接到 HTTP 终结点时，是否是启用服务器 TLS/SSL 证书验证。 HTTPS 服务器使用自签名证书时，将此属性设置为 false  。 | 否<br /> （默认值为 true）  |
| authenticationType | 指定身份验证类型。 允许的值为：Anonymous、Basic、Digest、Windows 和 ClientCertificate      。 不支持基于用户的 OAuth。 此外，还可以在 `authHeader` 属性中配置身份验证标头。 有关这些身份验证类型的更多属性和 JSON 示例，请参阅此表格下面的部分。 | 是 |
| authHeaders | 附加的用于身份验证的 HTTP 请求标头。<br/> 例如，若要使用 API 密钥身份验证，可以将身份验证类型选为“匿名”，然后在标头中指定 API 密钥。 | 否 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

### <a name="using-basic-digest-or-windows-authentication"></a>使用基本、摘要或 Windows 身份验证

将 authenticationType 属性设置为 Basic、Digest 或 Windows     。 除了前面部分所述的通用属性，还指定以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| userName | 用于访问 HTTP 终结点的用户名。 | 是 |
| password | 用户（userName 值）的密码  。 将此字段标记为 SecureString 类型以将其安全存储。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |

**示例**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>使用 ClientCertificate 身份验证

若要使用 ClientCertificate 身份验证，将 authenticationType 属性设置为ClientCertificate   。 除了前面部分所述的通用属性，还指定以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| embeddedCertData | Base64 编码的证书数据。 | 指定是 embeddedCertData，还是 certThumbprint   。 |
| certThumbprint | 自承载集成运行时计算机的证书存储中所安装证书的指纹。 仅当在 connectVia 属性中指定自承载类型的 Integration Runtime 时适用  。 | 指定是 embeddedCertData，还是 certThumbprint   。 |
| password | 与证书关联的密码。 将此字段标记为 SecureString 类型以将其安全存储。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |

如果使用 certThumbprint 进行身份验证，并在本地计算机的个人存储中安装了证书，则需要授予对自承载集成运行时的读取权限  ：

1. 打开 Microsoft 管理控制台 (MMC)。 添加面向“本地计算机”的“证书”管理单元。  
2. 展开“证书” > “个人”，然后选择“证书”    。
3. 右键单击个人存储中的证书，并选择“所有任务” > “管理私钥”   。
3. 在“安全性”选项卡上，添加运行 Integration Runtime 主机服务 (DIAHostService) 的、对证书具有读取访问权限的用户帐户  。

**示例 1：使用 certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：使用 embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-authentication-headers"></a>使用身份验证标头

此外，还可以配置身份验证请求标头，以及内置的身份验证类型。

示例：使用 API 密钥身份验证

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "url": "<HTTP endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

基于格式的数据集中 `location` 设置下的 HTTP 支持以下属性：

| 属性    | 说明                                                  | 必需 |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | 数据集中 `location` 下的 type 属性必须设置为 **HttpServerLocation**。 | 是      |
| relativeUrl | 包含数据的资源的相对 URL。 HTTP 连接器从以下组合 URL 复制数据：`[URL specified in linked service][relative URL specified in dataset]`。   | 否       |

> [!NOTE]
> 支持的 HTTP 请求有效负载大小约为 500 KB。 如果要传递给 Web 终结点的有效负载大小大于 500 KB，请考虑以更小的区块对该有效负载进行批处理。

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
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

本部分提供 HTTP 源支持的属性列表。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="http-as-source"></a>HTTP 作为源

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

基于格式的复制源中 `storeSettings` 设置下的 HTTP 支持以下属性：

| 属性                 | 说明                                                  | 必需 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **HttpReadSettings**。 | 是      |
| requestMethod            | HTTP 方法。 <br>允许的值为 Get（默认值）和 Post   。 | 否       |
| additionalHeaders         | 附加的 HTTP 请求标头。                             | 否       |
| requestBody              | HTTP 请求的正文。                               | 否       |
| httpRequestTimeout           | 用于获取响应的 HTTP 请求的超时 （TimeSpan 值）  。 该值是获取响应而不是读取响应数据的超时。 默认值为 00:01:40  。 | 否       |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否       |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
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
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍按原样支持以下模型，以实现向后兼容性。 建议使用上面几个部分中提到的新模型，并且创作 UI 已切换为生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 HttpFile   。 | 是 |
| relativeUrl | 包含数据的资源的相对 URL。 未指定此属性时，仅使用链接服务定义中指定的 URL。 | 否 |
| requestMethod | HTTP 方法。 允许的值为 Get（默认值）和 Post   。 | 否 |
| additionalHeaders | 附加的 HTTP 请求标头。 | 否 |
| requestBody | HTTP 请求的正文。 | 否 |
| format | 如果要在未经分析的情况下从 HTTP 终结点按原样检索数据，并将其复制到基于文件的存储，请跳过输入和输出数据集定义中的格式部分  。<br/><br/>如果要在复制期间分析 HTTP 响应内容，则支持以下文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat      。 请将格式中的“type”属性设置为上述值之一   。 有关详细信息，请参阅 [JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)。 |否 |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/><br/>支持的类型：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别：“最佳”和“最快”   。 |否 |

> [!NOTE]
> 支持的 HTTP 请求有效负载大小约为 500 KB。 如果要传递给 Web 终结点的有效负载大小大于 500 KB，请考虑以更小的区块对该有效负载进行批处理。

**示例 1：使用 Get 方法（默认）**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**示例 2：使用 Post 方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>旧复制活动源模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：HttpSource   。 | 是 |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 （TimeSpan 值）  。 该值是获取响应而不是读取响应数据的超时。 默认值为 00:01:40  。  | 否 |

**示例**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
