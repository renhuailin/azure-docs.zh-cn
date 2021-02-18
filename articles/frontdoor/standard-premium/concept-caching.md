---
title: Azure 前门：缓存
description: 本文将帮助你了解 Azure 前门标准/高级版和已启用缓存的路由规则。
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 73b2e8e59774e12ddb9aa684382510d1f2c151b8
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098776"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>用 Azure 前门标准/高级 (预览版进行缓存) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

在本文中，你将了解如何在启用缓存的情况下使用前门标准/高级 (预览版) 路由和规则集的行为。 Azure 前门是 (CDN) 的新式内容交付网络，具有动态站点加速和负载平衡。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="delivery-of-large-files"></a>大型文件交付

前门标准/高级 (预览版) 提供大文件，但文件大小没有上限。 Front Door 使用一种被称作对象区块的技术。 请求大型文件时，前门会从源中检索文件的较小部分。 接收到完全或字节范围内的文件请求后，前门环境会按 8 MB 的块请求源中的文件。

区块到达前门环境后，将缓存并立即为用户提供服务。 然后，Front Door 会并行预提取下一个区块。 此预提取可确保内容始终先于用户一个区块，从而减少延迟。 此过程将继续，直到) 请求下载整个文件 (或客户端关闭连接。

有关字节范围请求的详细信息，请阅读 [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)。
前门会缓存接收的任何区块，因此不需要在前端缓存中缓存整个文件。 对文件或字节范围的后续请求是从缓存中提供的。 如果块并非全部缓存，预提取用于从后端请求区块。 此优化依赖于原点支持字节范围请求的能力。 如果原点不支持字节范围请求，则此优化不起作用。

## <a name="file-compression"></a>文件压缩

请参阅在 Azure 前门中压缩文件来提高性能。

## <a name="query-string-behavior"></a>查询字符串行为

借助 Front Door，可控制如何对包含查询字符串的 Web 请求缓存文件。 在包含查询字符串的 Web 请求中，查询字符串是问号 (?) 后出现的请求部分。 查询字符串可以包含一个或多个键值对，其中字段名称和其值由等号 (=) 分隔。 每个键值对由与号 (&) 分隔。 例如，`http://www.contoso.com/content.mov?field1=value1&field2=value2`。 如果请求的查询字符串中有多个键值对，则其顺序并不重要。

* **忽略查询字符串**：在此模式下，前门将查询字符串从请求方传递到第一个请求上的源并缓存该资产。 从前门环境提供的资产的所有后续请求将忽略查询字符串，直至缓存的资产到期。

* **缓存每个唯一的 URL**：在此模式下，包含唯一 URL 的每个请求（包括查询字符串）将视为具有其自己的缓存的唯一资产。 例如，来自请求源的响应 `www.example.ashx?q=test1` 将缓存在前门环境中，并为具有相同查询字符串的后续缓存返回该响应。 `www.example.ashx?q=test2` 的请求将作为具有其自己的生存时间设置的单独资产来缓存。
* 你还可以使用规则集来指定 **缓存密钥查询字符串** 行为，并在生成缓存键时包括或排除指定的参数。 例如，默认缓存键为：/foo/image/asset.html，示例请求为 `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` 。 有一个规则集规则排除查询字符串 "userid"。 然后，查询字符串缓存键将为 `/foo/image/asset.html?language=EN&sessionid=200` 。

## <a name="cache-purge"></a>缓存清除

请参阅缓存清除。

## <a name="cache-expiration"></a>缓存到期
以下标头顺序用于确定项在缓存中的存储时间：</br>
1. Cache-Control: s-maxage=\<seconds>
2. 缓存控制：最大期限 =\<seconds>
3. 完 \<http-date>

表明响应不会缓存的响应标头（如缓存控制：专用、缓存控制：非缓存和缓存控制：不会进行缓存） Cache-Control。  如果没有 Cache-Control，则默认行为是前端会缓存 X 个时间的资源。 其中，X 随机选取1到3天。

## <a name="request-headers"></a>请求标头

使用缓存时，以下请求标头不会转发到源。
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>缓存持续时间

可以在规则集中配置缓存持续时间。 通过规则集设置的缓存持续时间是真正的缓存重写。 这意味着无论源响应标头是什么，它都将使用替代值。

## <a name="next-steps"></a>后续步骤

* 了解有关[规则集匹配条件](concept-rule-set-match-conditions.md)的详细信息
* 详细了解 [规则集操作](concept-rule-set-actions.md)