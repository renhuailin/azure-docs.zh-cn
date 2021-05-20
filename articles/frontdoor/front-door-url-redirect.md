---
title: Azure Front Door - URL 重定向 | Microsoft Docs
description: 本文可帮助你了解 Azure Front Door 如何支持 URL 重定向来实现其路由规则。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91442047"
---
# <a name="url-redirect"></a>URL 重定向
Azure Front Door 可在以下各级别重定向流量：协议、主机名、路径、查询字符串。 重定向是基于路径的，因此可为单个微服务配置这些功能。 这可通过优化资源使用情况来简化应用程序配置，并支持全局重定向和基于路径的重定向等新的重定向方案。
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure Front Door URL 重定向":::

## <a name="redirection-types"></a>重定向类型
重定向类型会设置响应状态代码，以便客户端理解重定向的目的。 支持以下类型的重定向：

- **301（永久移动）** ：指示已为目标资源分配了新的永久 URI。 以后对该资源的任何引用都将使用包含的 URI 之一。 将 301 状态代码用于 HTTP 到 HTTPS 重定向。 
- **302（找到）** ：指示目标资源暂时驻留在其他 URI 下。 由于重定向有时会发生更改，因此客户端应继续对将来的请求使用有效的请求 URI。
- **307（未找到）** ：指示目标资源暂时驻留在其他 URI 下。 如果用户代理自动重定向到该 URI，则绝不能更改该请求方法。 由于重定向会随着时间的推移而改变，因此客户端应继续对将来的请求使用原始的有效请求 URI。
- **308（永久重定向）** ：指示已为目标资源分配了新的永久 URI。 以后对该资源的任何引用都应使用包含的 URI 之一。

## <a name="redirection-protocol"></a>重定向协议
可设置将用于重定向的协议。 重定向功能最常见的用例是设置 HTTP 到 HTTPS 重定向。

- **仅 HTTPS**：如果你想要将流量从 HTTP 重定向到 HTTPS，请将协议设置为“仅 HTTPS”。 Azure Front Door 建议应始终将重定向设置为“仅 HTTPS”。
- **仅 HTTP**：将传入请求重定向到 HTTP。 仅当你希望保持 HTTP 流量（即非加密）时才使用此值。
- **匹配请求**：此选项保留传入请求使用的协议。 因此，HTTP 请求将保留 HTTP，HTTPS 请求将保留 HTTPS 后重定向。

## <a name="destination-host"></a>目标主机
在配置重定向路由的过程中，你还可以更改重定向请求的主机名或域。 你可以将此字段设置为更改重定向 URL 中的主机名，否则保留传入请求中的主机名。 因此通过此字段，可将发送自 `https://www.contoso.com/*` 的所有请求重定向到 `https://www.fabrikam.com/*`。

## <a name="destination-path"></a>目标路径
如果要将 URL 的路径段替换为重定向的一部分，则可以使用新的路径值来设置此字段。 否则，你可以选择保留路径值作为重定向的一部分。 因此通过此字段，可将发送到 `https://www.contoso.com/\*` 的所有请求重定向到 `https://www.contoso.com/redirected-site`。

## <a name="query-string-parameters"></a>查询字符串参数
你还可以替换重定向 URL 中的查询字符串参数。 若要替换传入请求 URL 中的现有查询字符串，请将此字段设置为“Replace”，然后设置适当的值。 否则，可以通过将此字段设置为“Preserve”来保留原始的查询字符串集。 例如，通过此字段，可将发送到 `https://www.contoso.com/foo/bar` 的所有流量重定向到 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`。 

## <a name="destination-fragment"></a>目标片段
目标片段是“#”之后的 URL 部分，浏览器使用该段登录到网页的特定部分。 可设置此字段，将片段添加到重定向 URL。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
