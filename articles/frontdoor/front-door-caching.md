---
title: Azure Front Door - 缓存 | Microsoft Docs
description: 本文可帮助你了解在具有已启用缓存的路由规则时 Front Door 的行为。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/03/2021
ms.author: duau
ms.openlocfilehash: d21066563f3b3e2e27b5e1a2b2b96b520bd62311
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542064"
---
# <a name="caching-with-azure-front-door"></a>Azure Front Door 的缓存
下列文档详细说明了在具有已启用缓存的路由规则时 Front Door 的行为。 Front Door 是一种具有动态站点加速和负载平衡功能的现代化内容交付网络 (CDN)，就像其他任何 CDN 一样，也支持缓存行为。

## <a name="delivery-of-large-files"></a>大型文件交付
Azure Front Door 服务可交付大型文件，不限制文件大小。 Front Door 使用一种被称作对象区块的技术。 请求大型文件时，Front Door 会从后端检索文件的较小部分。 收到完整或字节范围的文件请求后，Front Door 环境以 8 MB 的区块为单位从后端请求文件。

区块到达 Front Door 环境后，会将区块缓存并立即提供给用户。 然后，Front Door 会并行预提取下一个区块。 此预提取可确保内容始终先于用户一个区块，从而减少延迟。 该过程将一直持续到下载完整个文件（如果需要）或客户端关闭连接为止。

有关字节范围请求的详细信息，请阅读 [RFC 7233](https://www.rfc-editor.org/info/rfc7233)。
Front Door 会在收到任何区块后将区块缓存，因此整个文件无需在 Front Door 缓存中进行缓存。 随后从缓存中请求文件或字节范围的请求。 如果区块未全部缓存，将采用预提取从后端请求区块。 这种优化依赖于后端支持字节范围请求的能力。 如果后端不支持字节范围请求，则此优化不会生效。

## <a name="file-compression"></a>文件压缩
Front Door 可动态压缩边缘内容，从而更快响应客户端。 为了让文件符合压缩条件，必须启用缓存，并且文件必须是 MIME 类型才能压缩。 目前，Front Door 不允许更改此列表。 当前列表为：
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

此外，文件大小必须介于 1 KB 与 8 MB 之间（不含首尾）。

这些配置文件支持以下压缩编码：
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

如果请求同时支持 gzip 和 Brotli 压缩，则 Brotli 压缩优先。</br>
如果对资产的请求指定进行压缩，但该请求导致缓存缺失，则 Front Door 将直接在 POP 服务器上压缩资产。 此后，将从缓存提供压缩的文件。 通过 transfer-encoding: chunked 返回所生成的项。

## <a name="query-string-behavior"></a>查询字符串行为
借助 Front Door，可控制如何对包含查询字符串的 Web 请求缓存文件。 在包含查询字符串的 Web 请求中，查询字符串是问号 (?) 后出现的请求部分。 查询字符串可以包含一个或多个键值对，其中字段名称和其值由等号 (=) 分隔。 每个键值对由与号 (&) 分隔。 例如，`http://www.contoso.com/content.mov?field1=value1&field2=value2`。 如果请求的查询字符串中有多个键值对，其顺序并不重要。
- 忽略查询字符串：在此模式下，Front Door 将来自请求者的查询字符串传递到第一个请求上的后端并缓存该资产。 由 Front Door 环境处理的资产的所有后续请求都将忽略查询字符串，直到所缓存的资产过期。

- **缓存每个唯一的 URL**：在此模式下，包含唯一 URL 的每个请求（包括查询字符串）将视为具有其自己的缓存的唯一资产。 例如，后端对 `www.example.ashx?q=test1` 的请求做出的响应将缓存在 Front Door 环境中，并为具有同一查询字符串的后续缓存返回该响应。 `www.example.ashx?q=test2` 的请求将作为具有其自己的生存时间设置的单独资产来缓存。

## <a name="cache-purge"></a>缓存清除

直到资产的生存时间 (TTL) 到期之前，Front Door 将一直对资产进行缓存。 每当客户端请求的资产的 TTL 已过期时，Front Door 环境会检索新的更新后资产副本以提供请求，然后存储已刷新的缓存。

确保用户始终获取资产的最新副本的最佳做法是针对每次更新将资产版本化，并将其发布为新 URL。 Front Door 将立即检索用于下一个客户端请求的新资产。 有时候可能希望从所有边缘节点清除缓存的内容，并强制其全部检索新的已更新资产。 原因可能是 Web 应用程序的更新，也可能是快速更新包含错误信息的资产。

选择要从边缘节点清除的资产。 要清除所有资产，请选择“全部清除”。 否则，在“路径”中，输入要清除的每个资产的路径。

要清除的路径列表中支持以下格式：

- 单一路径清除：通过指定资产的完整路径（不带协议和域）并包含文件扩展名（例如 /pictures/strasbourg.png）来清除单个资产；
- **通配符清除**：星号 (\*) 可用作通配符。 清除路径中带有 /\* 的终结点下的所有文件夹、子文件夹和文件，或者通过指定后跟 /\* 的文件夹（例如 /pictures/\*），清除特定文件夹下的所有子文件夹和文件。
- **根域清除**：清除路径中具有“/”的终结点的根。

> [!NOTE]
> 清除通配符域：按照本部分中所述指定用于清除的缓存路径并不适用于与 Front Door 关联的任何通配符域。 目前，我们不支持直接清除通配符域。 可以通过指定特定子域和清除路径来清除特定子域中的路径。 例如，如果我的 Front Door 有 `*.contoso.com`，我可以输入 `foo.contoso.com/path/*` 来清除子域 `foo.contoso.com` 的资产。 目前，要在清除内容路径中指定主机名，则仅限于通配符域的子域（如果适用）。
>

Front Door 的缓存清除不区分大小写。 此外，它们不区分查询字符串，这表示清除 URL 时将一并清除其查询字符串的所有变体。 

## <a name="cache-expiration"></a>缓存到期
按下列标题顺序来确定项目在缓存中的存储时间：</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expires: \<http-date>

优选可指示不缓存响应的 Cache-Control 响应标头，例如 Cache-Control: private，Cache-Control: no-cache 和 Cache-Control: no-store。  如果没有 Cache-Control，则默认行为是 Front Door 缓存资源的时间长度为 X，其中 X 随机选取时间为 1 到 3 天。

## <a name="request-headers"></a>请求标头

使用缓存时，以下请求标头将不会被转发到后端。
- Content-Length
- Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>缓存行为和持续时间

可以在 Front Door 设计器传递规则和规则引擎中配置缓存行为和持续时间。 规则引擎缓存配置将始终替代 Front Door 设计器传递规则配置。

* 禁用缓存后，无论源响应指令是什么，Front Door 都不会缓存响应内容。

* 启用缓存后，对于“使用缓存默认持续时间”的不同值，缓存行为会有所不同。
    * 当“使用缓存默认持续时间”设置为“是”时，Front Door 始终采用源响应标头指令。 如果缺少源指令，Front Door 将缓存 1 到 3 天内任何位置的内容。
    * 当“使用缓存默认持续时间”设置为“否”时，Front Door 将始终替代“缓存持续时间”（必填字段），这意味着它将缓存缓存持续时间的内容，而忽略源响应指令中的值。 

> [!NOTE]
> 在 Front Door 设计器传递规则中设置的“缓存持续时间”是“最小缓存持续时间”。 如果源的缓存控制标头的 TTL 大于替代值，则此替代将不起作用。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
