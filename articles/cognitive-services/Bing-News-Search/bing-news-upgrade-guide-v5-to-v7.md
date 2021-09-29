---
title: 将必应新闻搜索 API v5 升级到 v7
titleSuffix: Azure Cognitive Services
description: 发现需要更新为使用版本 7 的“必应资讯搜索”应用程序部分。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 9f9688d0c952925fa17867ecd8599d84eee40e71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587036"
---
# <a name="news-search-api-upgrade-guide"></a>新闻搜索 API 升级指南

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

本升级指南确定了必应新闻搜索 API 版本 5 与版本 7 的不同之处。 本指南有助于发现需要更新为使用版本 7 的应用部分。

## <a name="breaking-changes"></a>中断性变更

### <a name="endpoints"></a>终结点

- 终结点的版本号已从 v5 更改为 v7。 例如，`https://api.cognitive.microsoft.com/bing/v7.0/news/search`。

### <a name="error-response-objects-and-error-codes"></a>错误响应对象和错误代码

- 现在，所有失败请求的响应正文都应包含 `ErrorResponse` 对象。

- 已将以下字段添加到 `Error` 对象。  
  - `subCode`&mdash;如果可能，将错误代码分入各个独立桶中
  - `moreDetails`&mdash;有关 `message` 字段中错误的更多信息

- 已将 v5 错误代码替换为以下可取的 `code` 和 `subCode` 值。

|代码|SubCode|说明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|只要出现任何子代码条件，必应就会返回 ServerError。 如果 HTTP 状态代码为 500，响应就会包含这些错误。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>已阻止|只要请求的任何部分无效，必应就会返回 InvalidRequest。 例如，缺少必需参数或参数值无效。<br/><br/>如果错误是 ParameterMissing 或 ParameterInvalidValue，HTTP 状态代码为 400。<br/><br/>如果错误是 HttpNotAllowed，则 HTTP 状态代码为 410。
|RateLimitExceeded||只要超过每秒查询数 (QPS) 或每月查询数 (QPM) 配额，必应就会返回 RateLimitExceeded。<br/><br/>如果超过 QPS，必应返回 HTTP 状态代码 429；如果超过 QPM，必应返回 HTTP 状态代码 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|当必应无法验证调用方身份时，必应会返回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 头或订阅密钥无效。<br/><br/>如果指定多个身份验证方法，便会发生冗余。<br/><br/>如果错误是 InvalidAuthorization，HTTP 状态代码为 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|当调用方无权访问资源时，必应会返回 InsufficientAuthorization。 如果订阅密钥已遭禁用或到期，就会发生此类错误。 <br/><br/>如果错误是 InsufficientAuthorization，HTTP 状态代码为 403。

- 下表列出了新旧错误代码的对应关系。 如果之前依赖 v5 错误代码，请相应地更新代码。

|版本 5 code|版本 7 代码.子代码
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
已禁用|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
已阻止|InvalidRequest.Blocked

### <a name="object-changes"></a>对象变化

- 已将 `contractualRules` 字段添加到 [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) 对象。 `contractualRules` 字段包含必须遵循的规则列表（例如，文章归属）。 必须应用 `contractualRules` 中提供的归属，而不是使用 `provider`。 仅当 [Web 搜索 API](../bing-web-search/overview.md) 响应包含新闻答案时，文章才包含 `contractualRules`。

## <a name="non-breaking-changes"></a>非重大变化

### <a name="query-parameters"></a>查询参数

- 已将 Products 添加为可以将 [category](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) 查询参数设置为的可取值。 请参阅[按市场划分的类别](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)。

- 已添加 [SortBy](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) 查询参数，它返回按日期排序的热门主题（最新主题排在最前面）。

- 已添加 [Since](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) 查询参数，它返回必应在指定 Unix 时间戳期间或之后发现的热门主题。

### <a name="object-changes"></a>对象变化

- 已将 `mentions` 字段添加到 [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) 对象。 `mentions` 字段包含在文章中找到的实体（人员或位置）列表。

- 已将 `video` 字段添加到 [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) 对象。 `video` 字段包含与新闻文章相关的视频。 视频要么是可以嵌入的 \<iframe\>，要么是动作缩略图。

- 已将 `sort` 字段添加到 [News](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) 对象。 `sort` 字段显示文章排序顺序。 例如，文章按相关性（默认设置）或日期排序。

- 已添加 [SortValue](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) 对象，用于定义排序顺序。 `isSelected` 字段指明响应是否使用了排序顺序。 如果为 true，表明响应使用了排序顺序。 如果 `isSelected` 为 false，可以使用 `url` 字段中的 URL 来请求获取其他排序顺序。