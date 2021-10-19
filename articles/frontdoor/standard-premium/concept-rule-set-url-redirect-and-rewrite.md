---
title: Azure Front Door 标准版/高级版（预览版）URL 重定向和 URL 重写
description: 本文可帮助你了解 Azure Front Door 如何使用 Azure Front Door 规则集支持 URL 重定向和 URL 重写。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 2e758560eed1ffb01117764f9399aa6f4f4b1395
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857543"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Azure Front Door 标准版/高级版（预览版）URL 重定向和 URL 重写

> [!Note]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

本文可帮助你了解 Azure Front Door 标准版/高级版如何支持规则集中使用的 URL 重定向和 URL 重写。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="url-redirect"></a>URL 重定向

Azure Front Door 可以在以下各级别重定向流量：协议、主机名、路径、查询字符串和片段。 由于重定向是基于路径的，因此可以为单个微服务配置这些功能。 借助 URL 重定向，可以通过优化资源使用情况来简化应用程序配置，并支持全局重定向和基于路径的重定向等新的重定向方案。

可以通过规则集配置 URL 重定向。

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="通过规则集创建 URL 重定向的屏幕截图。" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>重定向类型
重定向类型会设置响应状态代码，以便客户端理解重定向的目的。 支持以下类型的重定向：

* **301（永久移动）** ：指示已为目标资源分配了新的永久 URI。 以后对该资源的任何引用都将使用包含的 URI 之一。 将 301 状态代码用于 HTTP 到 HTTPS 重定向。
* **302（找到）** ：指示目标资源暂时驻留在其他 URI 下。 由于重定向有时会发生更改，因此客户端应继续对将来的请求使用有效的请求 URI。
* **307（未找到）** ：指示目标资源暂时驻留在其他 URI 下。 如果用户代理自动重定向到该 URI，则绝不能更改该请求方法。 由于重定向会随着时间的推移而改变，因此客户端应继续对将来的请求使用原始的有效请求 URI。
* **308（永久重定向）** ：指示已为目标资源分配了新的永久 URI。 以后对该资源的任何引用都应使用包含的 URI 之一。

### <a name="redirection-protocol"></a>重定向协议
可设置将用于重定向的协议。 重定向功能最常见的用例是设置 HTTP 到 HTTPS 重定向。

* **仅 HTTPS**：如果你想要将流量从 HTTP 重定向到 HTTPS，请将协议设置为“仅 HTTPS”。 Azure Front Door 建议应始终将重定向设置为“仅 HTTPS”。
* **仅 HTTP**：将传入请求重定向到 HTTP。 仅当你希望保持 HTTP 流量（即非加密）时才使用此值。
* **匹配请求**：此选项保留传入请求使用的协议。 因此，HTTP 请求将保留 HTTP，HTTPS 请求将保留 HTTPS 后重定向。

### <a name="destination-host"></a>目标主机
在配置重定向路由的过程中，你还可以更改重定向请求的主机名或域。 你可以将此字段设置为更改重定向 URL 中的主机名，否则保留传入请求中的主机名。 因此通过此字段，可将发送自 `https://www.contoso.com/*` 的所有请求重定向到 `https://www.fabrikam.com/*`。

### <a name="destination-path"></a>目标路径
如果要将 URL 的路径段替换为重定向的一部分，则可以使用新的路径值来设置此字段。 否则，你可以选择保留路径值作为重定向的一部分。 因此通过此字段，可将发送到 `https://www.contoso.com/\*` 的所有请求重定向到 `https://www.contoso.com/redirected-site`。

### <a name="query-string-parameters"></a>查询字符串参数
你还可以替换重定向 URL 中的查询字符串参数。 若要替换传入请求 URL 中的现有查询字符串，请将此字段设置为“Replace”，然后设置适当的值。 否则，可以通过将此字段设置为“Preserve”来保留原始的查询字符串集。 例如，通过此字段，可将发送到 `https://www.contoso.com/foo/bar` 的所有流量重定向到 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`。 

### <a name="destination-fragment"></a>目标片段
目标片段是“#”之后的 URL 部分，浏览器使用该段登录到网页的特定部分。 你可以设置此字段以将片段添加到重定向 URL。

## <a name="url-rewrite"></a>URL 重写

Azure Front Door 支持 URL 重写，以重写路由到原点的请求的路径。 URL 重写允许你添加条件，以便确保只有在满足特定条件的情况下才能重写 URL 或指定的标头。 这些条件基于请求和响应信息。

利用此功能，你可以根据方案、设备类型和请求的文件类型将用户重定向到不同源。

可以通过规则集配置 URL 重定向。

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="通过规则集创建 URL 重写的屏幕截图。" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>源模式

源模式是源请求中要替换的 URL 路径。 当前，源模式使用基于前缀的匹配。 若要匹配所有 URL 路径，请使用正斜杠 (/) 作为源模式值。

对于 URL 重写源模式，只考虑路由配置“要匹配的模式”后面的路径。 例如，如果你的传入 URL 格式为 `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-Source-pattern>`，则规则引擎只会将 `/<Rule-URL-Rewrite-Source-pattern>` 考虑为要重写的源模式。 因此，如果你的 URL 重写规则使用源模式匹配，则传出 URL 的格式将是 `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-destination>`。

对于必须删除 URL 路径的 `/<route-patterns-to-match-path` 段的方案，请在路由配置中将源组的源路径设置为 `/`。

### <a name="destination"></a>目标

你可以定义要在重写中使用的目标路径。 目标路径会覆盖源模式。

### <a name="preserve-unmatched-path"></a>暂留不匹配的路径

通过暂留不匹配的路径，可将源模式后面的剩余路径追加到新路径。

例如，如果将“暂留不匹配的路径”设置为“是”。
* 如果传入请求为 `www.contoso.com/sub/1.jpg`，则源模式被设置为 `/`，目标模式被设置为 `/foo/`，内容从源的 `/foo/sub/1`.jpg 提供。

* 如果传入请求为 `www.contoso.com/sub/image/1.jpg`，则源模式被设置为 `/sub/`，目标模式被设置为 `/foo/`，内容从源的 `/foo/image/1.jpg` 提供。

例如，如果将“暂留不匹配的路径”设置为“否”。
* 如果传入的请求为 `www.contoso.com/sub/image/1.jpg`，则源模式被设置为 `/sub/`，目标模式被设置为 `/foo/2.jpg`，无论 `wwww.contoso.com/sub/` 后跟什么路径，内容都将始终从源的 `/foo/2.jpg` 提供。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Front Door 标准/高级规则集](concept-rule-set.md)。
