---
title: 将 Azure Front Door 标准版/高级版与跨源资源共享配合使用
description: 了解如何将 Azure Front Door (AFD) 与跨源资源共享 (CORS) 配合使用。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098441"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>将 Azure Front Door 标准版/高级版与跨域资源共享 (CORS) 配合使用

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

## <a name="what-is-cors"></a>什么是 CORS？

CORS（跨域资源共享）是一项 HTTP 功能，使在一个域中运行的 Web 应用程序能够访问另一个域中的资源。 要减少跨站点脚本攻击的可能性，所有现代 Web 浏览器都实现了称为[同源策略](https://www.w3.org/Security/wiki/Same_Origin_Policy)的安全限制。 这可以防止网页调用其他域中的 API。  CORS 提供了一种安全方式，允许一个源（源域）调用另一个源中的 API。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-it-works"></a>工作原理

CORS 请求有两种类型：简单请求和复杂请求。

### <a name="for-simple-requests"></a>简单请求：

1. 浏览器发送带有其他源 HTTP 请求标头的 CORS 请求。 此标头的值是为父页面提供服务的源，它被定义为协议 *、域* 和端口的组合。  当来自 \://https://www.contoso.com 的页面尝试访问 fabrikam.com 源中的用户数据时，以下请求标头将发送到 fabrikam.com：

   `Origin: https://www.contoso.com`

2. 服务器可能会响应以下内容：

   * 响应中含有 **Access-Control-Allow-Origin** 标头，指示允许使用哪个源站点。 例如：

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * 如果服务器在检查源标头之后不允许跨源请求，则会显示 HTTP 错误代码（如 403）

   * 带有通配符的 **Access-Control-Allow-Origin** 标头，允许所有源：

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>复杂请求：

复杂请求是一个 CORS 请求，它要求浏览器在发送实际 CORS 请求之前发送预检请求（即初步探测）。 如果原始 CORS 请求可以继续并且是对同一 URL 的 `OPTIONS` 请求，则预检请求会向服务器请求权限。

> [!TIP]
> 有关 CORS 流和常见问题的详细信息，请查看 [REST API 的 CORS 指南](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)。
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>通配符或单个源场景
当 Access-Control-Allow-Origin 标头设置为通配符 (*) 或单个源时，Azure Front Door 上的 CORS 会自动工作，无需其他配置。  Azure Front Door 将缓存第一个响应，确保请求将使用相同的标头。

如果在源上设置 CORS 之前已对 Azure Front Door 发出请求，则需要清除终结点内容上的内容，以使用 Access-Control-Allow-Origin 标头重新加载内容。

## <a name="multiple-origin-scenarios"></a>多个源场景
如果需要 CORS 允许一个特定的源列表，情况会稍微复杂一些。 当 CDN 缓存第一个 CORS 源的 **Access-Control-Allow-Origin** 标头时，会出现问题。  当不同的 CORS 源发出其他请求时，CDN 将为缓存的 Access-Control-Allow-Origin 标头提供服务，但不匹配。 有多种方法可纠正此问题。

### <a name="azure-front-door-rule-set"></a>Azure Front Door 规则集

在 Azure Front Door 上，可以在 Azure Front Door [规则集](concept-rule-set.md)中创建规则，以检查请求的 Origin 标头。 如果是有效的源，规则将使用正确的值来设置 Access-Control-Allow-Origin 标头。 在这种情况下，来自文件的源服务器的 Access-Control-Allow-Origin 标头都会被忽略，并且 AFD 的规则引擎会完全管理允许的 CORS 源。

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="具有规则集的规则示例的屏幕截图。":::

> [!TIP]
> 你可以向规则添加其他操作，以修改其他响应标头，如 **Access-Control-Allow-Methods**。
> 

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 Front Door](create-front-door-portal.md)。
