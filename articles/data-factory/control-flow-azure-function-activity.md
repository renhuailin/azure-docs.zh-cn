---
title: Azure 函数活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure Functions 活动在 Azure 数据工厂或 Azure Synapse Analytics 管道中运行 Azure Functions
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 4e62ea4c57e00695b2a2f969b9fd4f80f8298681
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824081"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Azure 函数活动
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
通过 Azure 函数活动可以在 Azure 数据工厂或 Synapse 管道中运行 [Azure Functions](../azure-functions/functions-overview.md)。 若要运行 Azure 函数，需要创建链接服务连接以及指定计划执行的 Azure 函数的活动。

有关此功能的 8 分钟简介和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函数链接服务


Azure 函数的返回类型必须是有效的 `JObject`。 （请记住：[JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) 不是 `JObject`。）除了 `JObject` 之外的任何返回类型都将失败，并且会引发用户错误 *响应内容不是有效的 JObject*。

函数密钥提供了对函数名称的安全访问，其中每个函数在函数应用中都有单独的唯一密钥或主密钥。 托管标识提供对整个函数应用的安全访问。 用户需要提供密钥才能访问函数名称。 有关[函数访问密钥](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#configuration)的更多详细信息，请参阅函数文档


| **属性** | **说明** | **必需** |
| --- | --- | --- |
| type   | type 属性必须设置为：**AzureFunction** | 是 |
| 函数应用 URL | Azure 函数应用的 URL。 格式为 `https://<accountname>.azurewebsites.net`。 在 Azure 门户中查看函数应用时，此 URL 是 URL 部分下的值  | 是 |
| 函数密钥 | Azure 函数的访问密钥。 单击相应函数的“管理”部分，并复制“函数密钥”或“主机密钥”。 在此处了解详细信息：[Azure Functions HTTP 触发器和绑定](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函数活动

| **属性**  | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| name  | 管道中活动的名称  | String | 是 |
| type  | 活动的类型为“AzureFunctionActivity” | String | 是 |
| 链接服务 | 相应 Azure 函数应用的 Azure 函数链接服务  | 链接服务引用 | 是 |
| 函数名称  | 此活动在 Azure 函数应用中调用的函数的名称 | String | 是 |
| method  | 函数调用的 Rest API 方法 | 字符串支持的类型：“GET”、“POST”、“PUT”   | 是 |
| 标头的值开始缓存响应  | 发送到请求的标头。 例如，若要在请求中设置语言和类型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字符串（或带有 resultType 字符串的表达式） | 否 |
| body  | 随请求一起发送到函数 API 方法的正文  | 字符串（或带有 resultType 字符串的表达式）或对象。   | PUT/POST 方法所必需 |
|   |   |   | |

请参阅[请求有效负载架构](control-flow-web-activity.md#request-payload-schema)部分中的请求有效负载架构。

## <a name="routing-and-queries"></a>路由和查询

Azure 函数活动支持 **路由**。 例如，如果你的 Azure Function 具有终结点 `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`，则要在 Azure 函数活动中使用的 `functionName` 是 `<functionName>/<value>`。 你可以将此函数参数化以在运行时提供所需的 `functionName`。

Azure 函数活动还支持 **查询**。 必须将查询包括为 `functionName` 的一部分。 例如，当函数名称为 `HttpTriggerCSharp` 并且要包括的查询为 `name=hello` 时，无法将 Azure 函数活动中的 `functionName` 构造为 `HttpTriggerCSharp?name=hello`。 可以将此函数参数化，以便在运行时确定值。

## <a name="timeout-and-long-running-functions"></a>超时和长时间运行的函数

Azure Functions 会在 230 秒之后超时，无论在设置中配置的 `functionTimeout` 设置如何。 有关详细信息，请参阅[此文章](../azure-functions/functions-versions.md#timeout)。 若要解决此行为，请遵循某个异步模式或使用 Durable Functions。 Durable Functions 的好处是，它们提供自己的状态跟踪机制，因此无需实现你自己的机制。

在[此文章](../azure-functions/durable/durable-functions-overview.md)中详细了解 Durable Functions。 你可以设置 Azure 函数活动来调用持久函数，这将返回具有不同 URI 的响应，例如[此示例](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)。 因为 `statusQueryGetUri` 在函数正在运行时返回 HTTP 状态 202，因此你可以使用 Web 活动轮询函数的状态。 简单地设置一个 Web 活动并将 `url` 字段设置为 `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`。 当持久函数完成时，该函数的输出将是 Web 活动的输出。


## <a name="sample"></a>示例

可以在[此处](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)找到使用 Azure Functions 提取 tar 文件内容的示例。

## <a name="next-steps"></a>后续步骤

可在[管道与活动](concepts-pipelines-activities.md)中详细了解受支持的活动。
