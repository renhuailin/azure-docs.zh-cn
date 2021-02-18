---
title: Azure 前门：规则集
description: 本文提供 Azure 前门标准/高级规则集功能的概述。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098708"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>什么是用于 Azure 前门标准/高级 (预览版) 的规则集？

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

规则集是一个自定义规则引擎，它将规则组合组合成单个集，您可以将这些规则与多个路由关联起来。 规则集允许自定义请求在边缘处理的方式，以及 Azure 前门如何处理这些请求。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="common-supported-scenarios"></a>常见的支持方案

* 实现安全标头可防止基于浏览器的漏洞（例如 HTTP 严格传输-Security (HSTS) 、X-XSS-保护、内容安全策略、X 框架选项和用于跨域资源共享的访问控制允许的标头） (CORS) 方案。 基于安全性的属性也可以用 Cookie 来定义。

* 根据客户端设备类型将请求路由到应用程序的移动或桌面版本。

* 使用重定向功能，可以将301、302、307和308重定向返回给客户端，以将它们定向到新的主机名、路径、查询字符串或协议。

* 基于传入请求动态修改路由的缓存配置。

* 重写请求 URL 路径，并将请求转发到配置的源组中的相应源。

* 添加、修改或删除请求/响应标头以隐藏敏感信息，或通过标头捕获重要的信息。

* 支持服务器变量以动态更改请求/响应标头或 URL 重写路径/查询字符串（例如，在新页面加载或发布窗体时）。 目前仅支持 **[规则集操作](concept-rule-set-actions.md)** 的服务器变量。

## <a name="architecture"></a>体系结构

规则集在边缘处理请求。 当请求到达 Azure 前门标准/高级终结点时，将首先执行 WAF，然后执行 "路由" 中配置的设置。 这些设置包括与路由关联的规则集。 规则集在路由中从上到下进行处理。 这同样适用于规则集中的规则。 要执行每个规则中的所有操作，必须满足规则中的所有匹配条件。 如果请求与规则集配置中的任何条件都不匹配，则只会执行路由中的配置。

如果选中了 " **停止计算剩余规则** "，则不会执行与该路由关联的所有剩余规则集。  

### <a name="example"></a>示例

在下图中，首先执行 WAF 策略。 规则集将配置为追加响应标头。 如果符合条件得到满足，则标头将更改缓存控件的最大期限。

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="显示规则集体系结构的关系图。" lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>术语

使用 Azure 前门规则集，可以创建规则集配置的组合，每个规则由一组规则组成。 以下行介绍了在配置规则集时将会遇到的一些有用术语。

有关配额限制的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

* *规则集*：与一个或多个 [路由](concept-route.md)关联的一组规则。 每个配置限制为 25 个规则。 最多可以创建 10 个配置。

* *规则集规则*：包含多达10个匹配条件和5个操作的规则。 规则是规则集的本地规则，因此无法导出到跨规则集使用。 用户可以在多个规则集中创建相同的规则。

* 匹配条件：可以利用许多匹配条件来分析传入请求。 一个规则最多可包含 10 个匹配条件。 将使用 AND 运算符来评估匹配条件。 *条件中支持正则表达式*。 可以在 [规则集条件](concept-rule-set-match-conditions.md)中找到匹配条件的完整列表。

* *操作*：操作规定 AFD 如何根据匹配条件处理传入请求。 您可以修改缓存行为、修改请求标头/响应标头、执行 URL 重写和 URL 重定向。 *支持对服务器变量执行操作*。 一个规则最多可包含 10 个匹配条件。 可以找到 [规则集操作](concept-rule-set-actions.md)的完整操作列表。

## <a name="next-steps"></a>后续步骤

* 了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
* 了解如何配置第一个 [规则集](how-to-configure-rule-set.md)。
 
