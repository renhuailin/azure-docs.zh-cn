---
title: 开始翻译
titleSuffix: Azure Cognitive Services
description: 使用文档翻译服务发起文档翻译请求。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.author: v-jansk
ms.openlocfilehash: ae867a4383b5644eca2cabf2651c2c40797ed49a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540593"
---
# <a name="start-translation"></a>开始翻译

使用此 API，通过文档翻译服务发起翻译请求。 每个请求可包含多个文档，必须包含每个文档的源和目标容器。

前缀和后缀筛选器（如有提供）用于筛选文件夹。 前缀位于子路径中的容器名称后。

术语表/翻译记忆库可以包含在请求中，并由该服务应用于文档的翻译中。

如果术语表无效或在翻译过程中无法查看，则文档状态中将显示错误。 如果目标中已存在同名的文件，将覆盖该文件。 每个目标语言的 targetUrl 必须唯一。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
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

## <a name="request-body-batch-submission-request"></a>请求正文：批量提交请求

|名称|类型|说明|
|--- |--- |--- |
|inputs|BatchRequest[]|下面列出 BatchRequest。 包含文档的文档或文件夹的输入列表。 媒体类型：“application/json”、“text/json”、“application/*+json”。|

### <a name="inputs"></a>输入

输入批量翻译请求的定义。

|名称|类型|必需|说明|
|--- |--- |--- |--- |
|source|SourceInput[]|正确|下面列出 inputs.source。 输入文档的源。|
|storageType|StorageInputType[]|False|下面列出 inputs.storageType。 输入文档源字符串的存储类型。 仅需要进行单个文档翻译。|
|目标|TargetInput[]|正确|下面列出 inputs.target。 输出的目标位置。|

**inputs.source**

输入文档的源。

|名称|类型|必需|说明|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|错误|下面列出 DocumentFilter[]。|
|filter.prefix|string|错误|区分大小写的前缀字符串，用于筛选源路径中的文档以进行翻译。 例如，使用 Azure 存储 blob URI 时，请使用前缀限制要翻译的子文件夹。|
|filter.suffix|string|错误|区分大小写的后缀字符串，用于筛选源路径中的文档以进行翻译。 这最常用于文件扩展名。|
|语言|字符串|错误|语言代码如果未指定，则将对文档执行自动检测。|
|sourceUrl|string|True|包含文档的文件夹/容器或单个文件的位置。|
|storageSource|StorageSource|错误|下面列出 StorageSource。|
|storageSource.AzureBlob|string|错误||

**inputs.storageType**

输入文档源字符串的存储类型。

|名称|类型|
|--- |--- |
|文件|string|
|文件夹|string|

**inputs.target**

已完成翻译的文档的目标。

|名称|类型|必需|说明|
|--- |--- |--- |--- |
|category|string|错误|翻译请求的类别/自定义系统。|
|术语表|Glossary[]|错误|下面列出术语表。 术语表。|
|glossaries.format|string|错误|格式。|
|glossaries.glossaryUrl|string|True（如果使用术语表）|术语表的位置。 如果未提供格式参数，我们将使用文件扩展名来提取格式设置。 如果术语表中没有翻译语言对，则不会应用该术语表。|
|glossaries.storageSource|StorageSource|错误|上面列出 StorageSource。|
|glossaries.version|字符串|错误|可选版本。 如果未指定，则使用默认值。|
|targetUrl|字符串|True|包含文档的文件夹/容器的位置。|
|语言|字符串|True|目标语言代码（两个字母）。 请参阅[语言代码列表](../../language-support.md)。|
|storageSource|StorageSource []|错误|上面列出 StorageSource []。|

## <a name="example-request"></a>示例请求

下面是批处理请求的示例。

**翻译容器中的所有文档**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**翻译应用术语表的容器中的所有文档**

确保已为特定 Blob/文档（而非容器）创建了术语表 URL 和 SAS 令牌

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr",
                    "glossaries": [
                        {
                            "glossaryUrl": "https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D",
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**翻译容器中的特定文件夹**

请确保已将文件夹名称（区分大小写）指定为筛选器中的前缀（虽然 SAS 令牌仍用于容器）。

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D",
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**翻译容器中的特定文档**

* 确保指定了 "storageType": "File"
* 确保已为特定 Blob/文档（而非容器）创建了源 URL 和 SAS 令牌
* 确保已将目标文件名指定为目标 URL 的一部分 - 尽管 SAS 令牌仍适用于容器。
* 下面的示例请求显示了翻译成两种目标语言的单个文档

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。

|状态代码|说明|
|--- |--- |
|202|已接受。 成功的请求和批处理请求由服务创建。 标头 Operation-Location 将指示带有 operation ID.HeadersOperation-Location: 字符串的状态 URL|
|400|错误的请求。 请求无效。 检查输入参数。|
|401|未授权。 请检查凭据。|
|429|请求速率过高。|
|500|内部服务器错误。|
|503|服务当前不可用。  请稍后再试。|
|其他状态代码|<ul><li>请求过多</li><li>服务器暂不可用</li></ul>|

## <a name="error-response"></a>错误响应

|名称|类型|说明|
|--- |--- |--- |
|code|string|包含错误代码概要的枚举。 可能的值：<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>未授权</li></ul>|
|message|字符串|获取概要错误消息。|
|innerError|InnerTranslationError|新内部错误格式，符合认知服务 API 准则。 这包含必需的属性 ErrorCode、消息和可选属性目标、详细信息（键值对）、内部错误（可以嵌套）。|
|inner.Errorcode|字符串|获取代码错误字符串。|
|innerError.message|字符串|获取概要错误消息。|
|innerError.target|string|获取错误的源。 例如，如果文档无效，应为“文档”或“文档 ID”。|

## <a name="examples"></a>示例

### <a name="example-successful-response"></a>成功响应示例

成功的响应中会返回以下信息。

可以在 POST 方法的响应头 Operation-Location URL 值中找到作业 ID。 URL 的最后一个参数是操作的作业 ID，即“/operation/”之后的字符串。

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>错误响应示例

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

遵循快速入门，详细了解如何使用文档翻译和客户端库。

> [!div class="nextstepaction"]
> [文档翻译入门](../get-started-with-document-translation.md)
