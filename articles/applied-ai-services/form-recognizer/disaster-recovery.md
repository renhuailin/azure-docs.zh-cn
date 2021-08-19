---
title: Azure 表单识别器灾难恢复指南
titleSuffix: Azure Applied AI Services
description: 了解如何使用复制模型 API 来备份表单识别器资源。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 832ac0aac916c50bd21ae0cf7290e3f4920b756f
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326293"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>备份和恢复表单识别器模型

在 Azure 门户中创建表单识别器资源时，需指定一个区域。 然后，资源及其所有操作都将与该特定的 Azure 服务器区域保持关联。 有时我们会遇到影响整个区域的网络问题，这种情况比较罕见，但也不是没有可能。 如果解决方案需要始终保持可用，则应将其设计为可故障转移到另一区域，或者将工作负荷分散到两个或更多个区域。 这两种方法都要求不同的区域中至少有两个表单识别器资源，并且能够跨区域同步自定义模型。

复制 API 能够实现这种方案，因为它能让你在不同的表单识别器帐户（可位于任何受支持的地理区域中）之间复制自定义模型。 本指南介绍如何将复制 REST API 与 cURL 配合使用。 还可以使用 HTTP 请求服务（如 Postman）来发出请求。

## <a name="business-scenarios"></a>业务场景

如果你的应用或业务依赖于使用某个表单识别器自定义模型，我们建议将该模型复制到另一区域中的另一表单识别器帐户。 如果发生区域服务中断，你仍可以在该模型所复制到的区域中访问该模型。

##  <a name="prerequisites"></a>先决条件

1. 不同 Azure 区域中的两个表单识别器 Azure 资源。 如果没有这些资源，请转到 Azure 门户并<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="创建新的表单识别器资源" target="_blank">创建新的表单识别器资源 </a>。
1. 表单识别器资源的订阅密钥、终结点 URL 和订阅 ID。 可以在 Azure 门户上资源的“概述”选项卡中找到这些值。


## <a name="copy-api-overview"></a>复制 API 概述

复制自定义模型的过程包括以下步骤：

1. 首先，向目标资源（即接收所复制的模型的资源）发出复制授权请求。 你将获得新建目标模型（将接收复制的数据）的 URL。
1. 接下来，将复制请求发送到源资源（即包含要复制的模型的资源）。 你将获得一个 URL，查询该 URL 可跟踪操作的进度。
1. 你将使用源资源凭据来查询进度 URL，直到操作成功。 还可以在目标资源中查询新模型 ID 以获取新模型的状态。

## <a name="generate-copy-authorization-request"></a>生成复制授权请求

以下 HTTP 请求从目标资源获取复制授权。 需要输入目标资源的终结点和密钥作为头。

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/copyAuthorization
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

你将获得一个 `201\Created` 响应，其正文中包含 `modelId` 值。 此字符串是新建（空白）模型的 ID。 需要提供 `accessToken` 才能让 API 将数据复制到此资源，`expirationDateTimeTicks` 值是令牌的过期时间。 将这所有三个值保存到安全位置。

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>启动复制操作

以下 HTTP 请求对源资源启动复制操作。 需要输入源资源的终结点和密钥作为头。 请注意，请求 URL 包含所要复制的源模型的模型 ID。

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

请求正文需采用以下格式。 需要输入目标资源的资源 ID 和区域名称。 可以在 Azure 门户中资源的“属性”选项卡上找到资源 ID，并且可以在“密钥和终结点”选项卡上找到区域名称。 还需要提供在上一步骤中复制的模型 ID、访问令牌和过期时间值。

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> 复制 API 以透明方式支持 [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) 功能。 这不需要进行任何特殊处理，但请注意，如果在未加密的资源与已加密的资源之间进行复制，则需要包含请求头 `x-ms-forms-copy-degrade: true`。 如果不包含此头，复制操作将会失败并返回 `DataProtectionTransformServiceError`。

你将收到包含 Operation-Location 头的 `202\Accepted` 响应。 此值是用于跟踪操作进度的 URL。 请将其复制到某个临时位置，以便在下一步骤中使用。

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>常见错误

|错误|解决方法|
|:--|:--|
| 400/错误的请求，`"code:" "1002"` | 表示发生验证错误，或复制请求的格式不正确。 常见问题包括：a) `copyAuthorization` 有效负载无效或已被修改。 b) 令牌的 `expirationDateTimeTicks` 值已过期（`copyAuhtorization` 有效负载的有效期为 24 小时）。 c) `targetResourceRegion` 无效或不受支持。 d) `targetResourceId` 字符串无效或格式不正确。
|

## <a name="track-copy-progress"></a>跟踪复制进度

通过针对源资源终结点查询“获取复制模型结果”API 来跟踪进度。

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

响应将根据操作状态而异。 查找 JSON 正文中的 `"status"` 字段。 如果你要在脚本中自动执行此 API 调用，我们建议每秒查询操作一次。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>常见错误

|错误|解决方法|
|:--|:--|
|"错误":[{"代码":"AuthorizationError"，<br>"消息":"由于授权声明缺失 <br>或无效，授权失败。"}]   | 当 `copyAuthorization` 有效负载或内容遭到修改，因而与 `copyAuthorization` API 返回的结果不同时，将会发生此错误。 确保有效负载与先前 `copyAuthorization` 调用返回的内容完全相同。|
|"错误":[{"代码":"AuthorizationError"，<br>"消息":"无法检索授权 <br>元数据。 如果此问题持续出现，请使用不同的 <br>复制目标模型。"}] | 表示 `copyAuthorization` 有效负载正重复用于某个复制请求。 成功的复制请求不允许存在任何使用相同 `copyAuthorization` 有效负载的其他请求。 如果引发了其他错误（如下所述），然后又使用同一个授权有效负载重试复制，则会引发此错误。 解决方法是生成新的 `copyAuthorization` 有效负载，然后重新发出复制请求。|
|"错误":[{"代码":"DataProtectionTransformServiceError"，<br>"消息":"不允许该数据传输请求， <br>因为它会降级到较不安全的数据保护方案。 请参阅文档或联系服务管理员 <br>以获取详细信息。"}]    | 在支持 `AEK` 的资源与不支持 `AEK` 的资源之间进行复制时会发生此错误。 若要允许将已加密的模型以未加密的形式复制到目标，请在复制请求中指定 `x-ms-forms-copy-degrade: true` 头。|
|"错误":[{"代码":"ResourceResolverError"，<br>"消息":"无法提取 ID 为 '...' 的认知资源的信息。 请确保该资源有效且位于指定的区域 'westus2' 中..."}] | 表示 `targetResourceId` 所指示的 Azure 资源不是有效的认知资源，或者不存在。 验证并重新发出复制请求即可解决此问题。|


### <a name="optional-track-the-target-model-id"></a>[可选] 跟踪目标模型 ID 

还可以使用“获取自定义模型”API，通过查询目标模型来跟踪操作的状态。 使用在第一步骤中复制的目标模型 ID 调用此 API。

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

在响应正文中，将会看到有关模型的信息。 检查 `"status"` 字段即可了解模型的状态。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>cURL 示例代码

以下代码片段使用 cURL 发出上面的步骤中所述的 API 调用。 你仍需填写特定于自己资源的模型 ID 和订阅信息。

### <a name="generate-copy-authorization-request"></a>生成复制授权请求

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>启动复制操作

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.1/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>跟踪复制进度

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.1/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>后续步骤

在本指南中，你已学习如何使用复制 API 将自定义模型备份到次要表单识别器资源。 接下来请浏览 API 参考文档，以了解表单识别器的其他作用。
* [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
