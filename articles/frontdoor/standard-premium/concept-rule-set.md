---
title: Azure Front Door：规则集
description: 本文提供 Azure Front Door 标准版/高级版规则集功能的概述。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 47f69c72fdd7b3890d22d56de3a530135cf6fcc3
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437623"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>什么是 Azure Front Door 标准版/高级版（预览版）的规则集？

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

规则集是一个自定义规则引擎，它将各种规则组合成一个单一的集。 你可以将规则集与多个路由关联。 通过规则集可自定义在边缘处理请求的方式，以及 Azure Front Door 处理这些请求的方式。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="common-supported-scenarios"></a>常见的支持方案

* 实施安全性标头，以防止基于浏览器的漏洞攻击，例如 HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy、X-Frame-Options 以及用于跨域资源共享 (CORS) 方案的 Access-Control-Allow-Origin 标头。 基于安全性的属性也可以用 Cookie 来定义。

* 基于客户端设备类型，将请求路由到应用程序的移动或桌面版本。

* 使用重定向功能将 301、302、307 和 308 重定向返回到客户端，以定向到新的主机名、路径、查询字符串或协议。

* 基于传入请求动态修改路由的缓存配置。

* 重写请求 URL 路径，并将请求转发到配置的源组中的相应源。

* 添加、修改或删除请求/响应标头以隐藏敏感信息或通过标头捕获重要信息。

* 支持服务器变量来动态更改请求/响应标头或 URL 重写路径/查询字符串（例如，在新页面加载或发布窗体时）。 服务器变量当前仅在[规则集操作](concept-rule-set-actions.md)上受支持。

## <a name="architecture"></a>体系结构

规则集在边缘处理请求。 当请求到达 Azure Front Door 标准版/高级版终结点时，首先执行 WAF，然后执行路由中配置的设置。 这些设置包括与路由关联的规则集。 规则集在路由中的处理顺序为从上到下。 这同样适用于规则集中的规则。 要执行每个规则中的所有操作，必须满足规则中的所有匹配条件。 如果请求与规则集配置中的任何条件都不匹配，则只会执行路由中的配置。

如果“停止评估剩余规则”被选中，则与路由关联的所有剩余规则集都不会执行。  

### <a name="example"></a>示例

在下图中，首先执行 WAF 策略。 规则集将配置为追加响应头。 如果满足匹配条件，则标头将更改缓存控件的最大期限。

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="显示规则集体系结构的示意图。" lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>术语

使用 Azure Front Door 规则集，可以创建规则集配置的组合，每个配置由一组规则组成。 下面概述了在配置规则集时会遇到的一些有用的术语。

有关详细配额限制，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

* 规则集：与一个或多个[路由](concept-route.md)关联的一组规则。

* 规则集规则：由最多 10 个匹配条件和 5 个操作组成的规则。 规则对规则集而言是本地的，无法将其导出以跨规则集使用。 用户可以在多个规则集内创建相同的规则。

* 匹配条件：可以利用许多匹配条件来分析传入请求。 一个规则最多可包含 10 个匹配条件。 将使用 AND 运算符来评估匹配条件。 条件中支持正则表达式。 可以在[规则集匹配条件](concept-rule-set-match-conditions.md)中找到匹配条件的完整列表。

* *操作*：操作指示 AFD 如何根据匹配条件处理传入请求。 可以修改缓存行为，修改请求头/响应头，执行 URL 重写和 URL 重定向。 操作支持服务器变量。 一个规则最多可包含 10 个匹配条件。 可以在[规则集操作](concept-rule-set-actions.md)中找到操作的完整列表。

## <a name="arm-template-support"></a>ARM 模板支持

规则集可以使用 Azure 资源管理器模板进行配置。 请[参阅示例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set)。 你可以通过使用[匹配条件](concept-rule-set-match-conditions.md)和[操作](concept-rule-set-actions.md)文档示例中包含的 JSON 或 Bicep 代码片段来自定义行为。

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 Front Door Standard/Premium](create-front-door-portal.md)。
* 了解如何配置你的第一个[规则集](how-to-configure-rule-set.md)。
