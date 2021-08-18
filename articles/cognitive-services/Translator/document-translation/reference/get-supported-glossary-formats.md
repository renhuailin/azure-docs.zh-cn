---
title: 获取支持的术语表格式方法
titleSuffix: Azure Cognitive Services
description: 获取支持的术语表格式方法可返回受支持术语表格式的列表。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: ae21b4e510dc7e1f95303c562f58032758717d56
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540685"
---
# <a name="get-supported-glossary-formats"></a>获取支持的词汇表格式

“获取支持的术语表格式”方法会返回文档翻译服务支持的术语表格式的列表。 此列表包括常用文件扩展名。

## <a name="request-url"></a>请求 URL

将 `GET` 请求发送到：
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/glossaries/formats
```

了解如何查找[自定义域名](../get-started-with-document-translation.md#find-your-custom-domain-name)。

> [!IMPORTANT]
>
> * **向文档翻译服务发出的所有 API 请求都需要一个自定义域终结点**。
> * 不能使用 Azure 门户资源的“密钥和终结点”页上的终结点或者全局翻译器终结点 (`api.cognitive.microsofttranslator.com`) 来向文档翻译发出 HTTP 请求。

## <a name="request-headers"></a>请求标头

请求标头为：

|头文件|说明|
|--- |--- |
|Ocp-Apim-Subscription-Key|必需的请求标头|

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。

|状态代码|说明|
|--- |--- |
|200|没问题。 返回受支持术语表文件格式的列表。|
|500|内部服务器错误。|
|其他状态代码|<ul><li>请求过多</li><li>服务器暂不可用</li></ul>|


## <a name="get-supported-glossary-formats-response"></a>获取支持的术语表格式响应

获取支持的术语表格式 API 中将返回基类型列表。

### <a name="successful-get-supported-glossary-formats-response"></a>成功获取支持的术语表格式响应

获取支持的术语表格式 API 中将返回基类型列表。

|名称|类型|说明|
|--- |--- |--- |
|值|FileFormat []|FileFormat[] 包含下面列出的详细信息。|
|value.contentTypes|string []|此格式受支持的内容类型。|
|value.defaultVersion|字符串|如果未指定，则为默认版本|
|value.fileExtensions|string []| 此格式支受持的文件扩展名。|
|value.format|字符串|格式名称。|
|value.versions|string []| 受支持的版本。|

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

下面是成功响应的示例。

```JSON
{
    "value": [
        {
            "format": "XLIFF",
            "fileExtensions": [
                ".xlf"
            ],
            "contentTypes": [
                "application/xliff+xml"
            ],
            "defaultVersion": "1.2",
            "versions": [
                "1.0",
                "1.1",
                "1.2"
            ]
        },
        {
            "format": "TSV",
            "fileExtensions": [
                ".tsv",
                ".tab"
            ],
            "contentTypes": [
                "text/tab-separated-values"
            ]
        },
        {
            "format": "CSV",
            "fileExtensions": [
                ".csv"
            ],
            "contentTypes": [
                "text/csv"
            ]
        }
    ]
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
