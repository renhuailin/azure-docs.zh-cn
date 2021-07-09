---
title: 获取文档状态方法
titleSuffix: Azure Cognitive Services
description: get document status 方法可返回特定文档的状态。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 69172956d36aa4b43c88858a65771fdb183a39f6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110453608"
---
# <a name="get-document-status"></a>获取文档状态

Get Document Status 方法可返回特定文档的状态。 该方法根据请求 ID 和文档 ID 返回特定文档的翻译状态。

## <a name="request-url"></a>请求 URL

将 `GET` 请求发送到：
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}/documents/{documentId}
```

了解如何查找[自定义域名](../get-started-with-document-translation.md#find-your-custom-domain-name)。

> [!IMPORTANT]
>
> * **向文档翻译服务发出的所有 API 请求都需要一个自定义域终结点**。
> * 不能使用 Azure 门户资源的“密钥和终结点”页上的终结点或者全局翻译器终结点 (`api.cognitive.microsofttranslator.com`) 来向文档翻译发出 HTTP 请求。

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

|查询参数|必需|说明|
|--- |--- |--- |
|documentId|True|文档 ID。|
|id|True|批 ID。|
## <a name="request-headers"></a>请求标头

请求标头为：

|头文件|说明|
|--- |--- |
|Ocp-Apim-Subscription-Key|必需的请求标头|

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。

|状态代码|说明|
|--- |--- |
|200|没问题。 成功的请求，服务已接受该请求。 操作详细信息是 returned.HeadersRetry-After: integerETag: string|
|401|未授权。 检查凭据。|
|404|找不到该加密提供程序。 找不到资源。|
|500|内部服务器错误。|
|其他状态代码|<ul><li>请求过多</li><li>服务器暂不可用</li></ul>|

## <a name="get-document-status-response"></a>获取文档状态响应

### <a name="successful-get-document-status-response"></a>成功获取文档状态响应

|名称|类型|描述|
|--- |--- |--- |
|path|string|文档或文件夹的位置。|
|sourcePath|字符串|源文档的位置。|
|createdDateTimeUtc|字符串|操作创建的日期时间。|
|lastActionDateTimeUtc|字符串|操作的状态已更新的日期时间。|
|状态|字符串|作业或文档的可能状态的列表： <ul><li>已取消</li><li>Cancelling</li><li>失败</li><li>NotStarted</li><li>正在运行</li><li>已成功</li><li>ValidationFailed</li></ul>|
|to|string|目标语言的两个字母的语言代码。 [查看语言列表](../../language-support.md)。|
|进度|数字|翻译进度（如果提供）|
|id|字符串|文档 ID。|
|characterCharged|整型|由 API 计费的字符。|

### <a name="error-response"></a>错误响应

|名称|类型|说明|
|--- |--- |--- |
|code|string|包含错误代码概要的枚举。 可能的值：<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>未授权</li></ul>|
|message|字符串|获取概要错误消息。|
|innerError|InnerTranslationError|新内部错误格式，符合认知服务 API 准则。 这包含必需的属性 ErrorCode、消息和可选属性目标、详细信息（键值对）、内部错误（可以嵌套）。|
|innerError.code|字符串|获取代码错误字符串。|
|innerError.message|字符串|获取概要错误消息。|
|innerError.target|string|获取错误的源。 例如，如果文档无效，应为“文档”或“文档 ID”。|

## <a name="examples"></a>示例

### <a name="example-successful-response"></a>成功响应示例
以下 JSON 对象是成功响应的示例。

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "sourcePath": "https://myblob.blob.core.windows.net/sourceContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>错误响应示例

以下 JSON 对象是错误响应的示例。 其他错误代码的架构相同。

状态代码：401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

遵循快速入门，详细了解如何使用文档翻译和客户端库。

> [!div class="nextstepaction"]
> [文档翻译入门](../get-started-with-document-translation.md)
