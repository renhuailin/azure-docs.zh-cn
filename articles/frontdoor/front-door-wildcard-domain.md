---
title: Azure Front Door - 对通配符域的支持
description: 本文有助于了解 Azure Front Door 如何支持在自定义域列表中映射和管理通配符域。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94425756"
---
# <a name="wildcard-domains"></a>通配符域

除了顶点域和子域，还可以将通配符域映射到 Azure Front Door 配置文件的前端主机或自定义域。 如果在 Azure Front Door 配置中拥有通配符域，则会为同一路由规则中的 API、应用程序或网站的多个子域简化流量路由行为。 无需修改配置即可单独添加或指定每个子域。 例如，你可以通过使用相同的路由规则并添加通配符域 `*.contoso.com` 来定义 `customer1.contoso.com``customer2.contoso.com` 和 `customerN.contoso.com` 的路由。

改进后支持通配符域的主要方案包括：

- 无需在 Azure Front Door 配置文件中加入每个子域，然后启用 HTTPS 为每个子域绑定证书。
- 如果应用程序添加新的子域，则不再需要更改生产 Azure Front Door 配置。 以前，必须添加子域并绑定证书，附加 Web 应用程序防火墙 (WAF) 策略，然后将该域添加到不同的路由规则。

> [!NOTE]
> 现在，仅支持通过 API、PowerShell 和 Azure CLI 通过 Azure DNS 添加通配符域。 不支持在 Azure 门户中添加和管理通配符域。

## <a name="adding-wildcard-domains"></a>添加通配符域

你可以在前端主机或域的部分下添加通配符域。 类似于子域，Azure Front Door 验证是否有通配符域的 CNAME 记录映射。 此 DNS 映射可以是类似于 `*.contoso.com` 映射到 `contoso.azurefd.net` 的直接 CNAME 记录映射。 或者，可以使用 afdverify 临时映射。 例如，`afdverify.contoso.com` 映射到 `afdverify.contoso.azurefd.net` 可验证通配符的 CNAME 记录映射。

> [!NOTE]
> Azure DNS 支持 通配符记录。

在前端主机中添加通配符域的单级子域数量，最多可达前端主机的限制。 对于以下情况，可能需要此功能：

- 为子域定义与其余域不同的路由（从通配符域）。

- 针对特定子域具有不同的 WAF 策略。 例如，`*.contoso.com` 允许添加 `foo.contoso.com`，而无需再次证明域所有权。 但不允许 `foo.bar.contoso.com`，因为它不是 `*.contoso.com` 的单级子域。 若要在无需验证其他域所有权的情况下添加 `foo.bar.contoso.com`，则需要添加 `*.bar.contosonews.com`。

你可以添加通配符域及其子域，但有一些限制：

- 如果将通配符域添加到 Azure Front Door 配置文件：
  - 不能将通配符域添加到任何其他 Azure Front Door 配置文件。
  - 不能将通配符域的第一级子域添加到另一个 Azure Front Door 配置文件或 Azure 内容分发网络配置文件。
- 如果已将通配符域的子域添加到 Azure Front Door 配置文件或 Azure 内容分发网络配置文件，则该通配符域不能用于其他 Azure Front Door 配置文件。
- 如果两个配置文件（Azure Front Door 或 Azure 内容分发网络）具有根域的不同子域，则不能将通配符域添加到任一配置文件。

## <a name="certificate-binding"></a>证书绑定

若要在通配符域上接受 HTTPS 流量，必须在通配符域上启用 HTTPS。 通配符域的证书绑定需要通配符证书。 也就是说，证书的使用者名称还应包含通配符域。

> [!NOTE]
> 目前，仅支持使用自己的自定义 SSL 证书为通配符域启用 HTTPS。 Azure Front Door 托管证书不能用于通配符域。

你可以选择从 Azure Key Vault 或 Azure Front Door 托管证书为子域使用相同的通配符证书。

如果为已具有关联证书的通配符域添加子域，则不能为该子域禁用 HTTPS。 子域使用通配符域的证书绑定，除非其他 Key Vault 或 Azure Front Door 托管证书覆盖它。

## <a name="waf-policies"></a>WAF 策略

可以将 WAF 策略附加到通配符域，类似于其他域。 可以将不同的 WAF 策略应用到通配符域的子域。 对于子域，必须指定要使用的 WAF 策略，即使该策略与通配符域相同。 注意：子域不会自动从通配符域继承 WAF 策略。

如果你不希望为子域运行 WAF 策略，则可以创建没有托管或自定义规则集的空 WAF 策略。

## <a name="routing-rules"></a>路由规则

配置路由规则时，可以选择通配符域作为前端主机。 对于通配符域和子域，还可以具有不同的路由行为。 如 [Azure Front Door的路由匹配方式](front-door-route-matching.md)中所述，将在运行时选择不同路由规则中最特定的路由匹配。

> [!IMPORTANT]
> 你必须在路由规则中具有匹配的路径模式，否则客户端将会看到失败。 例如，有两个路由规则，路由 1（`*.foo.com/*` 映射到后端池 A）和路由 2（`/bar.foo.com/somePath/*` 映射到后端池 B）。 然后，针对 `bar.foo.com/anotherPath/*` 的请求到达。 Azure Front Door 根据更具体的域匹配选择路由 2，结果发现路由之间没有匹配的路径模式。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure Front Door 配置文件](quickstart-create-front-door.md)。
- 了解如何[在 Azure Front Door 上添加自定义域](front-door-custom-domain.md)。
- 了解如何[在自定义域上启用 HTTPS](front-door-custom-domain-https.md)。
