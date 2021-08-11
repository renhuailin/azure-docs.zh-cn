---
title: Azure Front Door：缓存
description: 本文可帮助你了解在具有已启用缓存的路由规则时 Azure Front Door 标准版/高级版的行为。
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 63ea252a4b4c673ae3028adb7ab793ac21fb2e99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564579"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>使用 Azure Front Door 标准版/高级版（预览版）进行缓存

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

本文介绍启用缓存时 Front Door 标准版/高级版（预览版）路由和规则集的行为。 Azure Front Door 是具有动态站点加速和负载均衡功能的新式内容分发网络 (CDN)。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="request-methods"></a>请求方法

只有 GET 请求方法可在 Azure Front Door 中生成缓存的内容。 所有其他请求方法始终通过网络进行代理。

## <a name="delivery-of-large-files"></a>大型文件交付

Front Door 标准版/高级版（预览版）可交付大型文件而不限制文件大小。 Front Door 使用一种被称作对象区块的技术。 请求大型文件时，Front Door 会从源检索文件的较小部分。 收到完整或字节范围的文件请求后，Front Door 环境就会以 8 MB 的区块为单位从源请求文件。

区块到达 Front Door 环境后，会将区块缓存并立即提供给用户。 然后，Front Door 会并行预提取下一个区块。 此预提取可确保内容始终先于用户一个区块，从而减少延迟。 该过程将一直持续到下载完整个文件（如果需要）或客户端关闭连接为止。

有关字节范围请求的详细信息，请阅读 [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)。
Front Door 会在收到任何区块后将区块缓存，因此整个文件无需在 Front Door 缓存中进行缓存。 随后从缓存中请求文件或字节范围的请求。 如果区块未全部缓存，将采用预提取从后端请求区块。 这种优化依赖于源支持字节范围请求的能力。 如果源不支持字节范围请求，则此优化不会生效。

## <a name="file-compression"></a>文件压缩

请参阅“通过在 Azure Front Door 中压缩文件来提高性能”。

## <a name="query-string-behavior"></a>查询字符串行为

借助 Front Door，可控制如何对包含查询字符串的 Web 请求缓存文件。 在包含查询字符串的 Web 请求中，查询字符串是问号 (?) 后出现的请求部分。 查询字符串可以包含一个或多个键值对，其中字段名称和其值由等号 (=) 分隔。 每个键值对由与号 (&) 分隔。 例如，`http://www.contoso.com/content.mov?field1=value1&field2=value2`。 如果请求的查询字符串中有多个键值对，其顺序并不重要。

* **忽略查询字符串**：在此模式下，Front Door 将来自请求者的查询字符串传递到第一个请求上的源并缓存该资产。 由 Front Door 环境处理的资产的所有后续请求都将忽略查询字符串，直到所缓存的资产过期。

* **缓存每个唯一的 URL**：在此模式下，包含唯一 URL 的每个请求（包括查询字符串）将视为具有其自己的缓存的唯一资产。 例如，源对 `www.example.ashx?q=test1` 的请求做出的响应将缓存在 Front Door 环境中，并为具有同一查询字符串的后续缓存返回该响应。 `www.example.ashx?q=test2` 的请求将作为具有其自己的生存时间设置的单独资产来缓存。
* 你还可使用规则集来指定“缓存键查询字符串”行为，在生成缓存键时包含或排除指定的参数。 例如，默认缓存键为：/foo/image/asset.html，示例请求为 `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200`。 有一个规则集规则可排除查询字符串“userid”。 那么查询字符串缓存键将为 `/foo/image/asset.html?language=EN&sessionid=200`。

## <a name="cache-purge"></a>缓存清除

请参阅“清除缓存”。

## <a name="cache-expiration"></a>缓存到期
按下列标题顺序来确定项目在缓存中的存储时间：</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expires: \<http-date>

优选可指示不缓存响应的 Cache-Control 响应头，例如 Cache-Control: private，Cache-Control: no-cache 和 Cache-Control: no-store。  如果没有 Cache-Control，则默认行为是 Front Door 缓存资源的时间长度为 X。 其中 X 随机选取 1 到 3 之间的天数。

## <a name="request-headers"></a>请求标头

使用缓存时，以下请求头将不会转发到源。
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>缓存持续时间

你可在规则集中配置缓存持续时间。 通过规则集设置的缓存持续时间是真正的缓存替代。 这意味着无论源响应头是什么，它都将使用替代值。

## <a name="next-steps"></a>后续步骤

* 详细了解[规则集匹配条件](concept-rule-set-match-conditions.md)
* 详细了解[规则集操作](concept-rule-set-actions.md)