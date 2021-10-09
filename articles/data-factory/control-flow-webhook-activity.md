---
title: Webhook 活动
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure 数据工厂和 Synapse Analytics 的 Webhook 活动通过自定义代码控制管道的执行。
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 8b0443cd44dffeec1ea9a70e460ca0f72e05b24d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798819"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Webhook 活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Webhook 活动可通过自定义代码控制管道的执行。 使用 Webhook 活动，代码可以调用终结点并向其传递回叫 URL。 管道运行在继续下一个活动之前，会等待系统调用回调。

> [!IMPORTANT]
> WebHook 活动现在允许你将错误状态和自定义消息显示回活动和管道。 将“reportStatusOnCallBack”设置为 true，并在回调有效负载中包含“StatusCode”和“Error”。 有关详细信息，请参阅[其他说明](#additional-notes)部分。

## <a name="syntax"></a>语法

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必需
-------- | ----------- | -------------- | --------
**name** | Webhook 活动的名称。 | 字符串 | 是 |
type | 必须设置为“WebHook”。 | 字符串 | 是 |
**method** | 目标终结点的 REST API 方法。 | 字符串。 支持的类型为“POST”。 | 是 |
**url** | 目标终结点和路径。 | 具有字符串的 **resultType** 值的字符串或表达式。 | 是 |
**headers** | 发送到请求的标头。 下面是一个示例，用于在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 一个字符串或表达式，其 **resultType** 值为字符串。 | 是的。 需要 `Content-Type` 标题，如 `"headers":{ "Content-Type":"application/json"}`。 |
**body** | 表示要发送到终结点的有效负载。 | 有效的 JSON，或 **resultType** 值为 JSON 的表达式。 请参阅[请求有效负载架构](./control-flow-web-activity.md#request-payload-schema)，了解请求有效负载的架构。 | 是 |
**身份验证** | 用于调用该终结点的身份验证方法。 支持的类型为“Basic”和“ClientCertificate”。 有关详细信息，请参阅[身份验证](./control-flow-web-activity.md#authentication)。 如果不需要身份验证，则排除此属性。 | 一个字符串或表达式，其 **resultType** 值为字符串。 | 否 |
**timeout** | 活动会等待多长时间，以便系统调用 **callBackUri** 所指定的回调。 默认值为 10 分钟（“00:10:00”）。 值的 TimeSpan 格式为 *d*.*hh*:*mm*:*ss*。 | 字符串 | 否 |
**回调时报告状态** | 允许用户报告 Webhook 活动的失败状态。 | 布尔 | 否 |

## <a name="authentication"></a>身份验证

Webhook 活动支持以下身份验证类型。

### <a name="none"></a>无

如果不需要身份验证，请勿包括 **authentication** 属性。

### <a name="basic"></a>Basic

指定与基本身份验证配合使用的用户名和密码。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>客户端证书

指定 PFX 文件和密码的 Base64 编码内容。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>托管标识

使用数据工厂或 Synapse 工作区的托管标识来指定为其请求访问令牌的资源 URI。 若要调用 Azure 资源管理 API，请使用 `https://management.azure.com/`。 有关如何托管标识工作原理的详细信息，请参阅 [Azure 资源的托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果该服务配置了 Git 存储库，则必须将凭据存储在 Azure Key Vault 中，才能使用基本身份验证或客户端证书身份验证。 该服务不会在 Git 中存储密码。

## <a name="additional-notes"></a>附加说明

该服务传递发送到 URL 终结点的主体中的附加属性“callBackUri”。 该服务希望在指定的超时值之前调用此 URI。 如果未调用此 URI，则活动会失败，其状态为“超时”。

只有当无法调用自定义终结点时，Webhook 活动才会失败。 任何错误消息都可以添加到回调正文，并在后续活动中使用。

对于每个 REST API 调用，如果终结点在一分钟内未响应，则客户端会超时。 此行为是标准的 HTTP 最佳做法。 若要解决此问题，请实施 202 模式。 在当前情况下，终结点返回“202 (已接受)”和客户端轮询。

请求的一分钟超时与活动超时无关。 后者用于等待 **callbackUri** 指定的回调。

传递回到回调 URI 的正文必须是有效 JSON。 将 `Content-Type` 标头设置为 `application/json`。

使用 **Report status on callback** 属性时，必须在进行回调时将以下代码添加到正文中：

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>后续步骤

请参阅以下受支持的控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)