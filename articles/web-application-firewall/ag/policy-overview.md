---
title: Azure Web 应用程序防火墙 (WAF) 策略概述
description: 本文概述了 Web 应用程序防火墙 (WAF) 的全局、每站点和每 URI 策略。
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518730"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Azure Web 应用程序防火墙 (WAF) 策略概述

Web 应用程序防火墙策略包含所有 WAF 设置和配置。 包括排除、自定义规则、托管规则等。 然后，将这些策略关联到应用程序网关（全局）、侦听器（每站点）或基于路径的规则（每 URI），以使策略生效。

系统对于可以创建的策略数量没有限制。 创建策略后，必须将策略关联到应用程序网关才能生效。 策略可以与应用程序网关、侦听器和基于路径的规则的任意组合相关联。

## <a name="global-waf-policy"></a>全局 WAF 策略

全局关联 WAF 策略时，应用程序网关 WAF 后面的每个站点均使用相同的托管规则、自定义规则、排除项和任何其他已配置的设置进行保护。

如果希望将单个策略应用到所有站点，可以将该策略与应用程序网关相关联。 有关详细信息，请参阅[创建应用程序网关的 Web 应用程序防火墙策略](create-waf-policy-ag.md)以使用 Azure 门户创建和应用 WAF 策略。 

## <a name="per-site-waf-policy"></a>每站点 WAF 策略

有了每站点 WAF 策略后，你就可以使用每站点策略保护单个 WAF 后多个具有不同安全需求的站点。 例如，如果 WAF 后方有五个站点，你可以通过五个单独的 WAF 策略（每个侦听器一个）来为每个站点自行定义排除项、自定义规则、托管规则集以及其他所有 WAF 设置。

假如应用程序网关应用了一项全局策略。 然后，你对该应用程序网关上的某个侦听器应用一个不同的策略。 该侦听器上的策略现在只对该侦听器生效。 该应用程序网关的全局策略依然适用于所有其他侦听器和未分配有特定策略的基于路径的规则。

## <a name="per-uri-policy"></a>每 URI 策略

要深入到 URI 级别进行更多自定义，可以将 WAF 策略与基于路径的规则相关联。 如果单个站点中的某些页面需要不同的策略，则可以对 WAF 策略进行更改，这些更改仅影响给定的 URI。 这可能适用于支付页面或登录页面，或者满足以下条件的任何其他 URI：相比 WAF 后面的站点，需要更加具体的 WAF 策略。

与每站点 WAF 策略一样，策略越具体，优先级越高。 这意味着，URL 路径映射上的每个 URI 策略会替代任何按站点的策略，或者高于此级别的全局 WAF 策略。

### <a name="example"></a>示例

假设有三个站点：contoso.com、fabrikam.com 和 adatum.com 都位于同一应用程序网关后面。 你需要将某个 WAF 应用于所有三个站点，但你需要通过 adatum.com 增加安全性，因为客户在这里访问、浏览和购买产品。

如有必要，可将全局策略应用到 WAF，其中包含一些基本设置、排除项或自定义规则（如有必要），以免某些误报导致流量被阻止。 如果发生这种情况，无需运行全局 SQL 注入规则，因为 fabrikam.com 和 contoso.com 是不带 SQL 后端的静态页面。 因此，你可以在全局策略中禁用这些规则。

全局策略适用于 contoso.com 和 fabrikam.com，但需要更小心地对待 adatum.com，因为其中处理的是登录信息和支付。 可以将每个站点策略应用于 adatum 侦听器并使 SQL 规则保持运行。 另外，假设有一个 Cookie 阻止了某些流量，因此你可以为该 cookie 创建排除以阻止误报。 

需要注意的是 adatum.com/payments URI。 请在该 URI 上应用另一个策略并启用所有规则，同时取消所有排除项。

在这个示例中，有一个适用于两个站点的全局策略。 你有一个每站点策略适用于一个站点，还有一个每 URI 策略适用于一个基于路径的特定规则。 有关此示例，请参阅对应 PowerShell 的[使用 Azure PowerShell 配置每站点 WAF 策略](per-site-policies.md)。

## <a name="existing-waf-configurations"></a>现有的 WAF 配置

所有新的 Web 应用程序防火墙的 WAF 设置（自定义规则、托管规则集配置、排除项等等）都在 WAF 策略中。 如果你有现有的 WAF，则这些设置可能仍存在于你的 WAF 配置中。 有关移动到新 WAF 策略的详细信息，请参阅[将 WAF 配置迁移到 WAF 策略](./migrate-policy.md)。 


## <a name="next-steps"></a>后续步骤

- [使用 Azure PowerShell 创建每站点和每 URI 策略](per-site-policies.md)。
