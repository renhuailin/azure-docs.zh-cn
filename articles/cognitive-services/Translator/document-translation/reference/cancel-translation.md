---
title: 取消翻译方法
titleSuffix: Azure Cognitive Services
description: 取消操作方法可取消当前正在处理或加入队列的操作。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/20/2021
ms.author: v-jansk
ms.openlocfilehash: eb8411717d0229b13571284176da7ccc113cf828
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540733"
---
# <a name="cancel-translation"></a>取消翻译

取消当前正在处理或加入队列的操作。 如果某个操作已完成、已失败或正在取消，则该操作不会被取消， 而是返回错误的请求。 所有已完成翻译的文档都不会被取消，并且需要付费。 如果可以，所有待处理的文件都将取消。

## <a name="request-url"></a>请求 URL

将 `DELETE` 请求发送到：

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}
```

了解如何查找[自定义域名](../get-started-with-document-translation.md#find-your-custom-domain-name)。

> [!IMPORTANT]
>
> * **向文档翻译服务发出的所有 API 请求都需要一个自定义域终结点**。
> * 不能使用 Azure 门户资源的“密钥和终结点”页上的终结点或者全局翻译器终结点 (`api.cognitive.microsofttranslator.com`) 来向文档翻译发出 HTTP 请求。

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

|查询参数|必需|说明|
|-----|-----|-----|
|id|True|操作 ID。|

## <a name="request-headers"></a>请求标头

请求标头为：

|头文件|说明|
|-----|-----|
|Ocp-Apim-Subscription-Key|必需的请求标头|

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。

| 状态代码| 说明|
|-----|-----|
|200|没问题。 取消已提交的请求|
|401|未授权。 检查凭据。|
|404|未找到。 找不到资源。 
|500|内部服务器错误。
|其他状态代码|<ul><li>请求过多</li><li>服务器暂不可用</li></ul>|

## <a name="cancel-translation-response"></a>取消翻译响应

### <a name="successful-response"></a>成功的响应

在成功的响应中返回以下信息。

|名称|类型|说明|
|--- |--- |--- |
|id|字符串|操作的 ID。|
|createdDateTimeUtc|字符串|操作创建的日期时间。|
|lastActionDateTimeUtc|字符串|操作的状态已更新的日期时间。|
|状态|字符串|作业或文档的可能状态的列表： <ul><li>已取消</li><li>Cancelling</li><li>失败</li><li>NotStarted</li><li>正在运行</li><li>已成功</li><li>ValidationFailed</li></ul>|
|摘要|StatusSummary|摘要包含下面列出的详细信息。|
|summary.total|integer|文档总数。|
|summary.failed|integer|失败的文档数。|
|summary.success|integer|成功翻译的文档数。|
|summary.inProgress|integer|正在进行处理的文档数。|
|summary.notYetStarted|integer|尚未开始处理的文档数。|
|summary.cancelled|integer|已取消的作业计数。|
|summary.totalCharacterCharged|integer|API 计费的总字符数。|

### <a name="error-response"></a>错误响应

|名称|类型|说明|
|--- |--- |--- |
|code|string|包含错误代码概要的枚举。 可能的值：<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>未授权</li></ul>|
|message|字符串|获取概要错误消息。|
|目标|string|获取错误的源。 例如，对于无效的文档，应为“文档”或“文档 ID”。|
|innerError|InnerTranslationError|新内部错误格式，符合认知服务 API 准则。 这包含必需的属性 ErrorCode、消息和可选属性目标、详细信息（键值对）、内部错误（可以嵌套）。|
|innerError.code|字符串|获取代码错误字符串。|
|innerError.message|字符串|获取概要错误消息。|
|innerError.target|string|获取错误的源。 例如，如果存在无效的文档，则为“文档”或“文档 ID”。|

## <a name="examples"></a>示例

### <a name="example-successful-response"></a>成功响应示例

以下 JSON 对象是成功响应的示例。

状态代码：200

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>错误响应示例

以下 JSON 对象是错误响应的示例。 其他错误代码的架构相同。

状态代码：500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

遵循快速入门，详细了解如何使用文档翻译和客户端库。

> [!div class="nextstepaction"]
> [文档翻译入门](../get-started-with-document-translation.md)
