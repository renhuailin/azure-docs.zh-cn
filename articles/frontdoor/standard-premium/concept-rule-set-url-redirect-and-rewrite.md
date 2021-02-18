---
title: 'URL 重定向和 URL 重写和 Azure 前门标准/高级 (预览版) '
description: 本文将帮助你了解 Azure 前门如何支持使用 Azure 前门规则集的 URL 重定向和 URL 重写。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098721"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>URL 重定向和 URL 重写和 Azure 前门标准/高级 (预览版) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本文可帮助你了解 Azure 前门标准/高级版如何支持在规则集中使用 URL 重定向和 URL 重写。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="url-redirect"></a>URL 重定向

Azure 前门可以在以下各级别重定向流量：协议、主机名、路径、查询字符串和片段。 可以为各个微服务配置这些功能，因为重定向是基于路径的。 使用 URL 重定向，可以通过优化资源使用来简化应用程序配置，并支持新的重定向方案，包括全局重定向和基于路径的重定向。

可以通过规则集配置 URL 重定向。

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="创建与规则集的 url 重定向的屏幕截图。" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>重定向类型
重定向类型为客户端设置响应状态代码，以了解重定向的目的。 支持以下类型的重定向：

* **301 (永久移动)**：指示目标资源已分配新的永久 URI。 以后对此资源的任何引用都将使用其中一个包含的 Uri。 将301状态代码用于 HTTP 到 HTTPS 的重定向。
* **302 (找到)**：指示目标资源暂时在不同的 URI 下。 由于重定向可能会发生变化，因此客户端应继续使用有效的请求 URI 来处理将来的请求。
* **307 (临时重定向)**：指示目标资源暂时在不同的 URI 下。 如果请求方法自动重定向到该 URI，则不能更改该方法。 由于重定向可能会随时间而变化，因此客户端应继续使用原始的有效请求 URI 来处理将来的请求。
* **308 (永久重定向)**：指示已为目标资源分配新的永久 URI。 以后对此资源的任何引用都应使用其中一个包含的 Uri。

### <a name="redirection-protocol"></a>重定向协议
你可以设置将用于重定向的协议。 重定向功能最常见的用例是将 HTTP 设置为 HTTPS 重定向。

* **仅限 https**：如果你想要将流量从 HTTP 重定向到 https，请将协议设置为 "仅限 https"。 Azure 前门建议你始终将重定向设置为 "仅限 HTTPS"。
* **仅限 http**：将传入请求重定向到 http。 仅当你想要将流量 HTTP 保持为非加密时，才使用此值。
* **匹配请求**：此选项保留传入请求所使用的协议。 因此，HTTP 请求将保持 HTTP，HTTPS 请求将保持 HTTPS post 重定向。

### <a name="destination-host"></a>目标主机
在配置重定向路由的过程中，还可以更改重定向请求的主机名或域。 你可以设置此字段以更改重定向的 URL 中的主机名，或通过其他方式保留传入请求中的主机名。 因此，使用此字段可以将发送到的所有请求重定向 `https://www.contoso.com/*` 到 `https://www.fabrikam.com/*` 。

### <a name="destination-path"></a>目标路径
如果要将 URL 的路径段替换为重定向的一部分，则可以使用新的 "路径" 值来设置此字段。 否则，你可以选择保留路径值作为重定向的一部分。 因此，使用此字段可以将发送到的所有请求重定向 `https://www.contoso.com/\*` 到  `https://www.contoso.com/redirected-site` 。

### <a name="query-string-parameters"></a>查询字符串参数
还可以替换重定向 URL 中的查询字符串参数。 若要替换传入请求 URL 中的任何现有查询字符串，请将此字段设置为 "Replace"，然后设置相应的值。 否则，可以通过将字段设置为 "保留" 来保留原始查询字符串集。 例如，使用此字段可以将发送到的所有流量重定向 `https://www.contoso.com/foo/bar` 到 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` 。 

### <a name="destination-fragment"></a>目标片段
目标片段是 "#" 之后的 URL 部分，浏览器使用该部分在网页的特定部分上进行陆地。 您可以设置此字段，以便将片段添加到重定向 URL。

## <a name="url-rewrite"></a>URL 重写

Azure 前门支持 URL 重写，以重写发送到源的请求的路径。 URL 重写允许您添加条件，以确保仅当满足某些条件时才重写 URL 或指定的标头。 这些条件基于请求和响应信息。

利用此功能，你可以根据方案、设备类型和请求的文件类型将用户重定向到不同来源。

可以通过规则集配置 URL 重定向。

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="用规则集创建 url 重写的屏幕截图。" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>源模式

源模式是源请求中要替换的 URL 路径。 当前，源模式使用基于前缀的匹配项。 若要匹配所有 URL 路径，请使用正斜杠 (/) 作为源模式值。

### <a name="destination"></a>目标

您可以定义要在重写中使用的目标路径。 目标路径将覆盖源模式。

### <a name="preserve-unmatched-path"></a>保留不匹配的路径

保留不匹配路径允许您将源模式后面的剩余路径追加到新路径。

例如，如果将不 **匹配的路径设置为 "是"**。
* 如果传入请求为 `www.contoso.com/sub/1.jpg` ，则会将源模式设置为 `/` ，将目标设置为， `/foo/` 并将内容从 `/foo/sub/1` 原点提供。

* 如果传入请求为，则会将 `www.contoso.com/sub/image/1.jpg` 源模式设置为 `/sub/` ，将目标设置为，将从 `/foo/` 源提供内容 `/foo/image/1.jpg` 。

例如，如果将 **不匹配的路径设置为 "否**"。
* 如果传入请求为，则将 `www.contoso.com/sub/image/1.jpg` 源模式设置为 `/sub/` ，将目标设置为，无论 `/foo/2.jpg` `/foo/2.jpg` 后跟哪个路径，都将始终从源提供内容 `wwww.contoso.com/sub/` 。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 前门标准/高级规则集](concept-rule-set.md)。
