---
title: 获取翻译状态
titleSuffix: Azure Cognitive Services
description: 获取翻译状态方法可返回提交的批处理请求列表和每个请求的状态。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.author: v-jansk
ms.openlocfilehash: 5ed4c565ad784bb50ebbc464d4229bfcabb7a5d7
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540684"
---
# <a name="get-translations-status"></a>获取翻译状态

获取翻译状态方法可返回提交的批处理请求列表和每个请求的状态。 此列表仅包含用户提交的批处理请求（基于资源）。

如果请求数超过了分页限制，则使用服务器端分页。 分页后的响应表示的是部分结果，且响应中包含延续令牌。 如果没有延续令牌，则表示没有其他可用页面。

$top、$skip 和 $maxpagesize 查询参数可用于指定要返回的结果数以及集合的偏移量。

$top 指示用户希望所有页面返回的记录总数。 $skip 指示根据指定的排序方法从批处理列表中跳过的记录数。 默认情况下，我们按开始时间降序排序。 $maxpagesize 是页面中返回的最大项数。 如果通过 $top 请求更多的项（或未指定 $top，并且会返回更多的项），则 @nextLink 将包含指向下一页的链接。

$orderBy 查询参数可用于对返回的列表进行排序（例如“$orderBy=createdDateTimeUtc asc”或“$orderBy=createdDateTimeUtc desc”）。 默认排序按 createdDateTimeUtc 降序排序。 一些可用于筛选返回列表的查询参数（例如：“status=Succeeded,Cancelled”）将仅返回成功和已取消的操作。 createdDateTimeUtcStart 和 createdDateTimeUtcEnd 可以组合使用，也可以单独使用，用于指定筛选返回列表所依据的日期时间范围。 支持的筛选查询参数包括：status、IDs、createdDateTimeUtcStart、createdDateTimeUtcEnd。

服务器以客户端指定的值为准。 但是，必须将客户端准备好以处理包含不同页面大小或包含延续令牌的响应。

同时包括 $top 和 $skip 时，服务器应先在集合上应用 $skip，然后再应用 $top。 

> [!NOTE]
> 如果服务器不能服从 $top 和/或 $skip，则服务器必须针对此状况向客户端返回一个错误通知，而不是简单地忽略查询选项。 这降低了客户端对返回的数据进行错误假设的风险。

## <a name="request-url"></a>请求 URL

将 `GET` 请求发送到：
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
```

了解如何查找[自定义域名](../get-started-with-document-translation.md#find-your-custom-domain-name)。

> [!IMPORTANT]
>
> * **向文档翻译服务发出的所有 API 请求都需要一个自定义域终结点**。
> * 不能使用 Azure 门户资源的“密钥和终结点”页上的终结点或者全局翻译器终结点 (`api.cognitive.microsofttranslator.com`) 来向文档翻译发出 HTTP 请求。

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

|查询参数|在|必需|类型|说明|
|--- |--- |--- |---|---|
|$maxpagesize|query|错误|整数 (int32)|$maxpagesize 是页面中返回的最大项数。 如果通过 $top 请求更多的项（或未指定 $top，并且会返回更多的项），则 @nextLink 将包含指向下一页的链接。 客户端可以通过指定 $maxpagesize 首选项来请求按特定页面大小进行服务器驱动的分页。 如果指定的页面大小小于服务器的默认页面大小，则服务器应遵循此首选项。|
|$orderBy|query|错误|array|集合的排序查询（例如：“CreatedDateTimeUtc asc”、“CreatedDateTimeUtc desc”）|
|$skip|query|错误|整数 (int32)|$skip 指示根据指定的排序方法从服务器保存的记录列表中跳过的记录数。 默认情况下，我们按开始时间降序排序。 客户端可以使用 $top 和 $skip 查询参数指定要返回的结果数以及集合的偏移量。 当客户端提供 $top 和 $skip 时，服务器应先后对集合应用 $skip 和 $top。注意：如果服务器不能遵循 $top 和/或 $skip，服务器必须向客户端返回一个错误通知，而不是只是忽略查询选项。|
|$top|query|错误|整数 (int32)|$top 指示用户希望所有页面返回的记录总数。 客户端可以使用 $top 和 $skip 查询参数指定要返回的结果数以及集合的偏移量。 当客户端提供 $top 和 $skip 时，服务器应先后对集合应用 $skip 和 $top。注意：如果服务器不能遵循 $top 和/或 $skip，服务器必须向客户端返回一个错误通知，而不是只是忽略查询选项。|
|createdDateTimeUtcEnd|query|错误|字符串（日期时间）|获取项的结束日期时间。|
|createdDateTimeUtcStart|query|错误|字符串（日期时间）|获取项的开始日期时间。|
|ids|query|错误|array|要在筛选中使用的 ID。|
|statuses|query|错误|array|要在筛选中使用的状态。|

## <a name="request-headers"></a>请求标头

请求标头为：

|头文件|说明|
|--- |--- |
|Ocp-Apim-Subscription-Key|必需的请求标头|

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。

|状态代码|说明|
|--- |--- |
|200|没问题。 成功请求并返回所有操作的状态。 HeadersRetry-After: integerETag: string|
|400|错误的请求。 请求无效。 检查输入参数。|
|401|未授权。 检查凭据。|
|500|内部服务器错误。|
|其他状态代码|<ul><li>请求过多</li><li>服务器暂不可用</li></ul>|

## <a name="get-translations-status-response"></a>获取翻译状态响应

### <a name="successful-get-translations-status-response"></a>成功获取翻译状态响应

成功的响应中会返回以下信息。

|名称|类型|说明|
|--- |--- |--- |
|@nextLink|字符串|下一页的 Url。 如果没有页，则为 NULL。|
|值|TranslationStatus[]|下面列出的 TranslationStatus[] 数组|
|value.id|字符串|操作的 ID。|
|value.createdDateTimeUtc|字符串|操作创建的日期时间。|
|value.lastActionDateTimeUtc|字符串|操作的状态已更新的日期时间。|
|value.status|字符串|作业或文档的可能状态的列表： <ul><li>已取消</li><li>Cancelling</li><li>失败</li><li>NotStarted</li><li>正在运行</li><li>已成功</li><li>ValidationFailed</li></ul>|
|value.summary|StatusSummary[]|摘要包含下面列出的详细信息。|
|value.summary.total|integer|文档总数。|
|value.summary.failed|integer|失败的文档数。|
|value.summary.success|integer|成功翻译的文档数。|
|value.summary.inProgress|integer|正在进行处理的文档数。|
|value.summary.notYetStarted|integer|尚未开始处理的文档数。|
|value.summary.cancelled|integer|已取消的文档数。|
|value.summary.totalCharacterCharged|integer|收取费用的字符总数。|

### <a name="error-response"></a>错误响应

|名称|类型|说明|
|--- |--- |--- |
|code|string|包含错误代码概要的枚举。 可能的值：<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>未授权</li></ul>|
|message|字符串|获取概要错误消息。|
|目标|string|获取错误的源。 例如，如果存在无效的文档，则应为“文档”或“文档 ID”。|
|innerError|InnerTranslationError|新内部错误格式，符合认知服务 API 准则。 这包含必需的属性 ErrorCode、消息和可选属性目标、详细信息（键值对）、内部错误（可以嵌套）。|
|innerError.code|字符串|获取代码错误字符串。|
|innerError.message|字符串|获取概要错误消息。|
|innerError.target|string|获取错误的源。 例如，如果存在无效的文档，则为“文档”或“文档 ID”。|

## <a name="examples"></a>示例

### <a name="example-successful-response"></a>成功响应示例

下面是成功响应的示例。

```JSON
{
    "value": [
        {
            "id": "36724748-f7a0-4db7-b7fd-f041ddc75033",
            "createdDateTimeUtc": "2021-06-18T03:35:30.153374Z",
            "lastActionDateTimeUtc": "2021-06-18T03:36:44.6155316Z",
            "status": "Succeeded",
            "summary": {
                "total": 3,
                "failed": 2,
                "success": 1,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "1c7399a7-6913-4f20-bb43-e2fe2ba1a67d",
            "createdDateTimeUtc": "2021-05-24T17:57:43.8356624Z",
            "lastActionDateTimeUtc": "2021-05-24T17:57:47.128391Z",
            "status": "Failed",
            "summary": {
                "total": 1,
                "failed": 1,
                "success": 0,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "daa2a646-4237-4f5f-9a48-d515c2d9af3c",
            "createdDateTimeUtc": "2021-04-14T19:49:26.988272Z",
            "lastActionDateTimeUtc": "2021-04-14T19:49:43.9818634Z",
            "status": "Succeeded",
            "summary": {
                "total": 2,
                "failed": 0,
                "success": 2,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 21899
            }
        }
    ],
    ""@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operations/727BF148-F327-47A0-9481-ABAE6362F11E/documents?$top=5&$skip=15"
}

```

### <a name="example-error-response"></a>错误响应示例

下面是错误响应的示例。 其他错误代码的架构相同。

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
