---
title: 使用 Azure 前门标准版/高级版和跨域资源共享
description: 了解如何使用 Azure 前门 (AFD) 通过跨域资源共享 (CORS) 。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098441"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>使用具有跨域资源共享的 Azure 前门标准/高级 (CORS) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

## <a name="what-is-cors"></a>什么是 CORS？

CORS（跨域资源共享）是一项 HTTP 功能，使在一个域中运行的 Web 应用程序能够访问另一个域中的资源。 为了降低跨站点脚本攻击的可能性，所有新式 web 浏览器都实现了称为 [相同源策略](https://www.w3.org/Security/wiki/Same_Origin_Policy)的安全限制。 这可以防止网页调用其他域中的 API。  CORS 提供了一种安全方式，允许一个源（源域）调用另一个源中的 API。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-it-works"></a>工作原理

CORS 请求有两种类型：简单请求和复杂请求。

### <a name="for-simple-requests"></a>简单请求：

1. 浏览器发送 CORS 请求和其他 **源** HTTP 请求标头。 此标头的值是为父页面提供服务的源，它被定义为协议 *、域* 和端口的组合。  当来自 https \: //www.contoso.com 的页面尝试访问 fabrikam.com 源中的用户数据时，以下请求标头将发送到 fabrikam.com：

   `Origin: https://www.contoso.com`

2. 服务器可能会响应以下内容：

   * 响应中含有 **Access-Control-Allow-Origin** 标头，指示允许使用哪个源站点。 例如：

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * 如果在检查源标头之后服务器不允许跨源请求，则为 HTTP 错误代码（如403）

   * 带有通配符的 **Access-Control-Allow-Origin** 标头，允许所有源：

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>复杂请求：

复杂请求是一个 CORS 请求，它要求浏览器在发送实际 CORS 请求之前发送预检请求（即初步探测）。 如果原始 CORS 请求可以继续并且是对同一 URL 的请求，则预检请求会询问服务器权限 `OPTIONS` 。

> [!TIP]
> 有关 CORS 流和常见问题的详细信息，请查看 [REST API 的 CORS 指南](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)。
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>通配符或单个源场景
当 **访问控制--源** 标头设置为通配符 ( * ) 或单个源时，Azure 前门上的 CORS 将自动使用，无需额外配置。  Azure 前门将缓存第一个响应，后续请求将使用同一标头。

如果在源上设置 CORS 之前已经对 Azure 前门发出请求，则需要清除终结点内容上的内容，以使用 **访问控制-源** 标头重新加载内容。

## <a name="multiple-origin-scenarios"></a>多个源场景
如果需要 CORS 允许一个特定的源列表，情况会稍微复杂一些。 当 CDN 缓存第一个 CORS 源的 **Access-Control-Allow-Origin** 标头时，会出现问题。  当不同的 CORS 源发出另一请求时，CDN 将为缓存的 **访问控制允许源** 标头提供服务，而不会匹配。 可以通过多种方法来更正此问题。

### <a name="azure-front-door-rule-set"></a>Azure 前门规则集

在 Azure 前门上，你可以在 Azure 前门 [规则设置](concept-rule-set.md) 中创建规则，以检查请求的 **源** 标头。 如果是有效来源，则规则将使用正确的值设置 **访问控制允许源** 标头。 在这种情况下，将忽略来自文件的源服务器的 **访问控制允许的** 标头，并且 AFD 的规则引擎会完全管理允许的 CORS 来源。

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="规则集的规则示例屏幕截图。":::

> [!TIP]
> 你可以向规则添加其他操作，以修改其他响应标头，如 **访问控制-允许方法**。
> 

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 Front Door](create-front-door-portal.md)。
