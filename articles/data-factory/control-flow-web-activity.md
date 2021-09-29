---
title: Web 活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Web 活动（Azure 数据工厂和 Azure Synapse Analytics 支持的控制流活动之一）从管道调用 REST 终结点。
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: c5656ffb4d8e129e334efc11e9a8e00be7227493
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798838"
---
# <a name="web-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的 Web 活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Web 活动可用于从 Azure 数据工厂或 Synapse 管道调用自定义的 REST 终结点。 可以传递数据集和链接服务以供活动使用和访问。

> [!NOTE]
> 使用自承载集成运行时，还支持 Web 活动调用专用虚拟网络中托管的 URL。 该集成运行时应可以看到 URL 终结点。 

> [!NOTE]
> 支持的最大输出响应负载大小为 4 MB。  

## <a name="syntax"></a>语法

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必需
-------- | ----------- | -------------- | --------
name | Web 活动的名称 | String | 是
type | 必须设置为 **WebActivity**。 | String | 是
method | 目标终结点的 Rest API 方法。 | 字符串。 <br/><br/>支持的类型：“GET”、“POST”、“PUT” | 是
url | 目标终结点和路径 | 字符串（或带有 resultType 字符串的表达式）。 如果活动在 1 分钟内未收到终结点的响应，则会超时并显示错误。 | 是
headers | 发送到请求的标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 字符串（或带有 resultType 字符串的表达式） | 是，需要内容类型标头。 `"headers":{ "Content-Type":"application/json"}`
body | 表示要发送到终结点的有效负载。  | 字符串（或带有 resultType 字符串的表达式）。 <br/><br/>请参阅[请求有效负载架构](#request-payload-schema)部分中的请求有效负载架构。 | POST/PUT 方法所必需。
authentication | 用于调用该终结点的身份验证方法。 支持的类型为“Basic”或“ClientCertificate”。 有关详细信息，请参阅[身份验证](#authentication)部分。 如果不需要身份验证，则排除此属性。 | 字符串（或带有 resultType 字符串的表达式） | 否
datasets | 传递给终结点的数据集列表。 | 数据集引用数组。 可以是空数组。 | 是
linkedServices | 传递给终结点的链接服务列表。 | 链接服务引用数组。 可以是空数组。 | 是
connectVia | 用于连接到数据存储的[集成运行时](./concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 | 集成运行时引用。 | 否 

> [!NOTE]
> Web 活动调用的 REST 终结点必须返回 JSON 类型的响应。 如果活动在 1 分钟内未收到终结点的响应，则会超时并显示错误。

下表显示了 JSON 上下文的要求：

| 值类型 | 请求正文 | 响应正文 |
|---|---|---|
|JSON 对象 | 支持 | 支持 |
|JSON 数组 | 支持 <br/>（目前，JSON 数组不作为错误结果使用。 正在运行修补程序。） | 不支持 |
| JSON 值 | 支持 | 不支持 |
| 非 JSON 类型 | 不支持 | 不支持 |
||||

## <a name="authentication"></a>身份验证

下面是 Web 活动中支持的身份验证类型。

### <a name="none"></a>无

如果不需要身份验证，请排除“身份验证”属性。

### <a name="basic"></a>基本

指定用户名和密码以用于基本身份验证。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>客户端证书

指定 base64 编码的 PFX 文件内容和密码。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>托管标识

使用数据工厂或 Synapse 工作区实例的托管标识指定要为其请求访问令牌的资源 URI。 若要调用 Azure 资源管理 API，请使用 `https://management.azure.com/`。 有关如何托管标识工作原理的详细信息，请参阅 [Azure 资源概述页面的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果数据工厂或 Synapse 工作区配置了 Git 存储库，则必须将凭据存储在 Azure Key Vault 中，才能使用基本身份验证或客户端证书身份验证。 该服务不会在 Git 中存储密码。

## <a name="request-payload-schema"></a>请求有效负载架构
当使用 POST/PUT 方法时，正文属性表示发送到终结点的有效负载。 可以将链接服务和数据集作为有效负载的一部分进行传递。 下面是有效负载的架构：

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>示例
在此示例中，管道中的 Web 活动调用了 REST 终结点。 它将 Azure SQL 链接服务和 Azure SQL 数据集传递到该终结点。 REST 终结点使用 Azure SQL 连接字符串连接到逻辑 SQL Server，并返回 SQL Server 实例的名称。

### <a name="pipeline-definition"></a>管道定义

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>管道参数值

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Web 服务终结点代码

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>后续步骤
参阅支持的其他控制流活动：

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
