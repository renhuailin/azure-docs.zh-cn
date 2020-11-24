---
title: Azure 前门服务上的 azure Web 应用程序防火墙-常见问题
description: 本文提供有关 Azure 前门上的 Web 应用程序防火墙的常见问题的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545663"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure 前门服务上的 Azure Web 应用程序防火墙常见问题解答

本文解答了有关 Azure Web 应用程序防火墙的常见问题 (WAF) Azure 前门服务特性和功能。 

## <a name="what-is-azure-waf"></a>什么是 Azure WAF？

Azure WAF 是一个 Web 应用程序防火墙，可帮助保护 Web 应用程序免受常见威胁，例如 SQL 注入、跨站点脚本和其他 Web 攻击。 可以定义包含自定义和托管规则组合的 WAF 策略，以控制对 Web 应用程序的访问。

可以将 Azure WAF 策略应用于托管在应用程序网关或 Azure Front Door 上的 Web 应用程序。

## <a name="what-is-waf-on-azure-front-door"></a>什么是 Azure 前门上的 WAF？ 

Azure 前门是高度可缩放的全球分布式应用程序和内容交付网络。 Azure WAF 与前门集成后，可在 Azure 网络边缘停止拒绝服务和目标应用程序攻击，在进入虚拟网络之前，接近攻击源，提供保护，而不影响性能。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支持 HTTPS？

前门提供 TLS 卸载。 WAF 本机与前门集成，并可以在请求解密后检查请求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支持 IPv6？

是。 可为 IPv4 和 IPv6 配置 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>托管规则集的最新版本是什么？

我们尽最大努力维持不断变化的威胁。 更新新规则后，将使用新的版本号将其添加到默认规则集。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果更改 WAF 策略，会发生什么传播时间？

全局部署 WAF 策略通常花费大约5分钟的时间，并且通常会更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>不同区域的 WAF 策略是否可以不同？

与前门集成后，WAF 是全局资源。 同一配置适用于所有前门位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何实现仅限前门访问我的后端：

你可以在后端配置 IP 访问控制列表，使其仅允许前门出站 IP 地址范围，并拒绝来自 Internet 的任何直接访问。 支持在虚拟网络上使用服务标记。 此外，还可以验证 "X 转发的主机 HTTP 标头" 字段对你的 web 应用程序是否有效。

## <a name="which-azure-waf-options-should-i-choose"></a>我应该选择哪些 Azure WAF 选项？

在 Azure 中应用 WAF 策略时，有两个选项可供选择。 使用 Azure 前门的 WAF 是一种全球分布的边缘安全解决方案。 使用应用程序网关的 WAF 是一个区域专用解决方案。 建议选择一个基于整体性能和安全要求的解决方案。 有关详细信息，请参阅 [通过 Azure 的应用程序交付套件进行负载平衡](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md)。

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>在前门上启用 WAF 的建议方法是什么？

在现有应用程序上启用 WAF 时，通常会有误报检测，其中 WAF 规则会将合法流量检测为威胁。 若要最大程度地降低对用户的影响，我们建议执行以下过程：

* 启用 WAF [**检测** 模式](./waf-front-door-create-portal.md#change-mode) ，以确保在执行此过程时，WAF 不会阻止请求。
  > [!IMPORTANT]
  > 此过程介绍如何在新的或现有的解决方案中启用 WAF，以便将干扰最小化到应用程序的用户。 如果受到攻击或发生了威胁，你可能想要立即在 **防护** 模式下部署 WAF，并使用优化过程监视和优化 WAF 一段时间。 这可能会导致某些合法流量被阻止，这就是我们仅在威胁时才建议这样做的原因。
* 遵循我们 [的指导来优化 WAF](./waf-front-door-tuning.md)。 此过程要求您启用诊断日志记录、定期检查日志，并添加规则排除和其他缓解措施。
* 重复此过程，定期检查日志，直到你认为没有合法流量被阻止。 整个过程可能需要几周的时间。 理想情况下，每次进行优化更改后，会看到误报检测值越少。
* 最后，在 **防护模式下** 启用 WAF。
* 即使是在生产环境中运行 WAF，也应继续监视日志以识别任何其他误报检测。 定期查看日志还将帮助你确定已阻止的任何实际攻击尝试。

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>是否支持所有集成平台中的相同 WAF 功能？

目前，应用程序网关上的 WAF 只支持 ModSec CRS 2.2.9、CRS 3.0 和 CRS 3.1 规则。 只有 Azure 前门上的 WAF 支持速率限制、异地筛选和 Azure 托管默认规则集规则。

## <a name="is-ddos-protection-integrated-with-front-door"></a>是否与前门集成了 DDoS 防护？ 

在 Azure 网络边缘全球分布，Azure 前门可以吸收并在地理上隔离大容量攻击。 你可以创建自定义 WAF 策略，以自动阻止和速率限制具有已知签名) 攻击的 http (。 此外，还可以在部署后端的 VNet 中启用 DDoS 保护标准。 Azure DDoS 保护标准客户可获得更多好处，包括成本保护、SLA 保证，以及从 DDoS 快速响应团队访问专家，以在攻击期间立即获得帮助。 有关详细信息，请参阅 [前门上的 DDoS 防护](../../frontdoor/front-door-ddos.md)。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>为什么将超出阈值的其他请求传递到我的后端服务器？

速率限制规则可以限制来自任何客户端 IP 地址的异常高流量。 可以在一分钟或五分钟的持续时间内，配置从客户端 IP 地址允许的 web 请求数的阈值。 对于粒度速率控制，可以将速率限制与其他匹配条件（如 HTTP (S) 参数匹配）组合在一起。 

来自同一客户端的请求通常会到达同一前门服务器。 在这种情况下，会看到超出阈值的其他请求会立即被阻止。 

但是，来自同一客户端的请求可能会到达尚未刷新速率限制计数器的另一个前端服务器。 例如，客户端可能会为每个请求打开一个新连接，并且阈值较低。 在这种情况下，对新前门服务器的第一个请求将通过速率限制检查。 速率限制阈值通常设置为 "高" 以防止来自任何客户端 IP 地址的拒绝服务攻击。 对于极低的阈值，可能会看到超出阈值的其他请求通过。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Web 应用程序防火墙](../overview.md)。
- 了解 [Azure Front Door](../../frontdoor/front-door-overview.md)。