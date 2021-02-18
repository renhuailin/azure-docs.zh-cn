---
title: 什么是 Azure 前门标准/高级路由？
description: 本文可帮助你了解 Azure 前门标准/高级版如何匹配传入请求使用哪个路由规则。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098740"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>什么是 Azure 前门标准/高级 (预览版) 路线？

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门标准/高级路由定义传入请求到达 Azure 前门环境时如何处理流量。 通过路由设置，可在域和后端源组之间定义关联。 通过打开模式到 Mach-o 的高级功能，规则集可以实现对流量的更精细控制。

前门标准/高级路由配置由两个主要部分组成： "左侧" 和 "右侧"。 我们会将传入请求与路由的左侧匹配，右侧定义处理请求的方式。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="incoming-match-left-hand-side"></a>传入的匹配项（左侧）

以下属性确定传入的请求是否与传递规则（或左侧）匹配：

* HTTP 协议 (HTTP/HTTPS)
* **宿主** (例如 www \. foo.com \* bar.com) 
* 路径（例如，/\*、/users/\*/file.gif）

这些属性会在内部扩展，因此协议/主机/路径的每种组合都是潜在的匹配集。

### <a name="route-data-right-hand-side"></a>路由数据（右侧）

如何处理请求的决定取决于是否为路由启用了缓存。 如果缓存的响应不可用，则会将请求转发到相应的后端。

## <a name="route-matching"></a>路由匹配

本节将重点介绍我们如何匹配给定的 Front Door 传递规则。 基本概念是始终优先匹配最具体的匹配项，仅查看“左侧”。  首先基于 HTTP 协议匹配，接下来是前端主机，然后是路径。

### <a name="frontend-host-matching"></a>前端主机匹配

匹配前端主机时，使用以下定义的逻辑：

1. 查找主机上具有完全匹配项的任何路由。
2. 如果没有精确的前端主机匹配项，则拒绝该请求并发送“400 错误请求”错误。

为进一步解释此过程，让我们看看 Front Door 路由的示例配置（仅限左侧）：

| 路由规则 | 前端主机 | 路径 |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www \. fabrikam.com，foo.adventure-works.com  | /\*, /images/\* |

如果以下传入的请求已发送到 Front Door，则它们将与上面的传递规则匹配：

| 传入前端主机 | 匹配的传递规则 |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www \. fabrikam.com | C |
| images.fabrikam.com | 错误 400：错误请求 |
| foo.adventure-works.com | C |
| contoso.com | 错误 400：错误请求 |
| www \. adventure-works.com | 错误 400：错误请求 |
| www \. northwindtraders.com | 错误 400：错误请求 |

### <a name="path-matching"></a>路径匹配

在 Azure 前门标准/高级版中，将确定特定前端主机并筛选可能的路由规则，以便只包含该前端主机的路由。 然后，前门根据请求路径筛选路由规则。 使用与前端主机类似的逻辑：

1. 在路径上查找具有完全匹配项的任何传递规则
2. 如果没有完全匹配的路径，请查找具有匹配的通配符路径的传递规则
3. 如果找不到具有匹配路径的传递规则，则拒绝该请求并返回“400：错误请求”错误 HTTP 应答。

>[!NOTE]
> 任何没有通配符的路径都视作完全匹配的路径。 即使路径以斜线结尾，仍被视作完全匹配。

为了进一步说明，让我们看看另一组示例：

| 路由规则 | 前端主机    | 路径     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

对于该配置，将产生以下匹配表示例：

| 传入的请求    | 匹配的路由 |
|---------------------|---------------|
| www \. contoso.com/            | A             |
| www \. contoso.com/a           | B             |
| www \. contoso.com/ab          | C             |
| www \. contoso.com/abc         | D             |
| www \. contoso.com/abzzz       | B             |
| www \. contoso.com/abc/        | E             |
| www \. contoso.com/abc/d       | F             |
| www \. contoso.com/abc/def     | G             |
| www \. contoso.com/abc/defzzz  | F             |
| www \. contoso.com/abc/def/ghi | F             |
| www \. contoso.com/path        | B             |
| www \. contoso.com/path/       | H             |
| www \. contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> 如果完全匹配的前端主机没有包含全部路由路径 (`/*`) 的传递规则，则不会与任何传递规则匹配。
>
> 示例配置：
>
> | 路由 | 主机             | 路径    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> 匹配表：
>
> | 传入的请求       | 匹配的路由 |
> |------------------------|---------------|
> | profile.domain.com/other | 无。 错误 400：错误请求 |

### <a name="routing-decision"></a>路由决策

一旦 Azure 前门标准/高级版与单一路由规则匹配，则需要选择处理请求的方式。 如果 Azure 前门标准/高级版具有可用于匹配路由规则的缓存响应，则该请求将返回到客户端。 接下来，Azure 前门标准/高级评估是指是否已为匹配的路由规则设置规则。 如果未定义规则集，则请求会按原样转发到后端池。 否则，规则集将按配置的顺序执行。

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
