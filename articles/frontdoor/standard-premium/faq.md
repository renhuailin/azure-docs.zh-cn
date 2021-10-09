---
title: Azure Front Door：常见问题解答
description: 本页提供有关 Azure Front Door 标准版/高级版的常见问题的解答。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/18/2021
ms.author: duau
ms.openlocfilehash: 378ba28eaa4b68cb1d05fa0af5c11ca95bd52a8b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210319"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Azure Front Door 标准版/高级版（预览版）常见问题解答

本文解答有关 Azure Front Door 特性和功能的常见问题。

## <a name="general"></a>常规

### <a name="what-is-azure-front-door"></a>什么是 Azure Front Door？

Azure Front Door 是可提供智能威胁防护的快速、可靠且安全的新式云 CDN。 它为全球超大规模应用程序、API、网站和云服务提供静态和动态内容加速、全局负载均衡和增强的安全性，并具备智能威胁防护功能。

### <a name="what-features-does-azure-front-door-support"></a>Azure Front Door 支持哪些功能？

Azure Front Door 支持：

* 静态内容和动态应用程序加速
* TLS/SSL 卸载和端到端 TLS
* Web 应用程序防火墙
* 基于 Cookie 的会话关联
* 基于 URL 路径的路由
* 免费证书和多域管理

有关支持的功能的完整列表，请参阅 [Azure Front Door 概述](overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front Door 和 Azure 应用程序网关有什么差别？

Front Door 和应用程序网关都是第 7 层 (HTTP/HTTPS) 负载均衡器，两者的主要差别在于，Front Door 是全球性的服务。 应用程序网关是区域性的服务。 Front Door 可以在各区域中的不同缩放单元/群集/服务器组单元之间进行负载均衡，而应用程序网关可用于在缩放单元内部的 VM/容器之间进行负载均衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>何时应在 Front Door 后面部署应用程序网关？

为何应在 Front Door 后面使用应用程序网关的重要原因是：

* Front Door 只能在全球级别执行基于路径的负载均衡，但如果用户要在其虚拟网络 (VNET) 中进一步对流量进行负载均衡，则应使用应用程序网关。
* 由于 Front Door 不是在 VM/容器级别工作，因此无法执行连接排出。 但是，应用程序网关可用于执行连接排出。 
* 在 Front Door 后面使用应用程序网关，用户可以实现 100% 的 TLS/SSL 卸载，并仅在其虚拟网络 (VNET) 内部路由 HTTP 请求。
* Front Door 和应用程序网关都支持会话亲和性。 Front Door 可将亲和后生成的流量从用户会话定向到给定区域中的相同群集或后端。 应用程序网关可将亲和的流量定向到群集中的相同服务器。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>是否可以在 Front Door 后面部署 Azure 负载均衡器？

Azure Front Door 需要一个可将流量路由到的公共 VIP 或公用 DNS 名称。 在 Front Door 后面部署 Azure 负载均衡器是一个常见用例。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front Door 支持哪些协议？

Azure Front Door 支持 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-support-http2"></a>Azure Front Door 如何支持 HTTP/2？

HTTP/2 协议支持仅适用于连接到 Azure Front Door 的客户端。 与后端池中后端的通信是通过 HTTP/1.1 进行的。 默认已启用 HTTP/2 支持。

### <a name="what-resources-are-supported-today-as-part-of-an-origin-group"></a>目前支持将哪些资源用作源组的一部分？

源组可以包含两种类型的源：

- 公共源包括存储帐户、应用服务应用、Kubernetes 实例或已建立公共连接的任何其他自定义主机名。 必须通过公共 IP 地址或可公开解析的 DNS 主机名来定义这些源。 源组的成员可以跨可用性区域、地理区域，甚至在 Azure 外部进行部署，前提是它们已建立公共连接。 Azure Front Door 标准和高级层支持公共源。
- 使用 Azure Front Door（高级）时，可以使用[专用链接](concept-private-link.md)源。

### <a name="what-regions-is-the-service-available-in"></a>该服务已在哪些区域推出？

Azure Front Door 是一个全球性的服务，并不局限于任何特定的 Azure 区域。 在创建 Front Door 时唯一需要指定的位置是资源组的位置。 该位置在本质上是指定资源组元数据要存储到的位置。 Front Door 资源本身是作为全局资源创建的，其配置将全局部署到所有边缘位置。 

### <a name="where-are-the-edge-locations-for-azure-front-door"></a>Azure Front Door 的边缘位置在哪儿？

有关 Azure Front Door 边缘位置的完整列表，请参阅 [Azure Front Door 边缘位置](edge-locations.md)。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door 是应用程序的专用部署还是在客户之间共享？

Azure Front Door 是全球分布式多租户服务。 Front Door 的基础结构在其所有客户之间共享。 创建 Front Door 配置文件可以定义应用程序所需的特定配置。 对你的 Front Door 所做的更改不会影响其他 Front Door 配置。

### <a name="is-http-https-redirection-supported"></a>是否支持 HTTP 到 HTTPS 的重定向？

是。 事实上，Azure Front Door 支持主机、路径、查询字符串重定向，以及一部分 URL 重定向。 详细了解 [URL 重定向](concept-rule-set-url-redirect-and-rewrite.md)。 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何锁定为仅限通过 Azure Front Door 访问我的后端？

将应用程序锁定为仅接受来自特定 Front Door 实例的流量的最佳方式是通过专用终结点发布应用程序。 Front Door 与应用程序之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行传输，避免暴露给公共 Internet。

详细了解如何[使用专用链接保护 Front Door 的源](concept-private-link.md)。  

将应用程序锁定为仅接受来自特定 Front Door 的流量的替代方式是为后端设置 IP ACL。 然后，使用 Front Door 发送的“X-Azure-FDID”头的特定值限制后端流量。 下面详细说明了这些步骤：

* 为后端配置 IP ACL，以仅接受来自 Azure Front Door 后端 IP 地址空间和 Azure 基础结构服务的流量。 有关为后端配置 ACL 的信息，请参阅以下 IP 详细信息：
 
    * 有关 Front Door 的 IPv4 后端 IP 地址范围，参阅 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)中的“AzureFrontDoor.Backend”部分。 还可以在[网络安全组](../../virtual-network/network-security-groups-overview.md#security-rules)中使用服务标记 AzureFrontDoor.Backend。
    * 通过虚拟化主机 IP 地址 `168.63.129.16` 和 `169.254.169.254` 配置 Azure 的[基本基础结构服务](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations)。

    > [!WARNING]
    > Front Door 的后端 IP 空间今后可能会更改，但在此之前，我们会确保与 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)相集成。 我们建议你订阅 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)，以随时了解任何更改或更新。

* 使用 API `2020-01-01` 或更高版本在 Front Door 上执行 GET 操作。 在 API 调用中查看 `frontdoorID` 字段。 根据 Front Door 发送到后端的、包含 `frontdoorID` 字段值的传入头“X-Azure-FDID”进行筛选。 还可以在 Front Door 门户页的“概述”部分下找到 `Front Door ID` 值。 

* 在后端 Web 服务器中应用规则筛选，以基于生成的“X-Azure-FDID”头值限制流量。

  下面是 [Microsoft Internet Information Services (IIS)](https://www.iis.net/) 的示例：

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

* Azure Front Door 还支持 AzureFrontDoor.Frontend 服务标记，该标记提供客户端在连接到 Front Door 时使用的 IP 地址的列表。 在你控制应该被允许连接到部署在 Azure Front Door 之后的服务的出站流量时，可使用 AzureFrontDoor.Frontend 服务标记。 Azure Front Door 还支持一个额外的服务标记 AzureFrontDoor.FirstParty，以与其他 Azure 服务在内部集成。 有关 Azure Front Door 服务标记用例的更多详细信息，请参阅[可用服务标记](../../virtual-network/service-tags-overview.md#available-service-tags)。

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>任意播 IP 在 Front Door 的生存期内是否可能更改？

Front Door 的前端任意播 IP 通常不会更改，在 Front Door 的生存期内可能保持静态。 但是，我们不能保证这一点。 敬请不要对 IP 有任何直接依赖。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door 是否支持静态或专用 IP？

否，Azure Front Door 目前不支持静态或专用前端任意播 IP。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door 是否支持 x-forwarded-for 头？

是，Azure Front Door 支持 X-Forwarded-For、X-Forwarded-Host 和 X-Forwarded-Proto 头。 对于 X-Forwarded-For，如果该头已存在，则 Front Door 会将客户端套接字 IP 追加到其中。 否则，它会添加该头，并在其中包含客户端套接字 IP 作为值。 对于 X-Forwarded-Host 和 X-Forwarded-Proto，将替代值。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>部署 Azure Front Door 需要多长时间？ 更新 Front Door 时，它是否仍可正常工作？

大多数规则引擎配置更新在 20 分钟内完成。 更新完成后，规则应该就会立即生效。 

 > [!Note]  
  > 全局部署大多数自定义 TLS/SSL 证书更新需要数分钟到一小时。

对路由或后端池的任何更新是无缝的，完全不会导致停机（如果新配置正确）。 除非你从“Azure Front Door 托管”切换到“使用自己的证书”或采用其他方法，否则证书更新不会导致任何服务中断。


## <a name="configuration"></a>配置

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door 是否可以对虚拟网络内部的流量进行负载均衡或路由？

Azure Front Door（标准）需要通过一个公共 IP 或可公开解析的 DNS 名称来路由流量。 Azure Front Door 无法直接路由到虚拟网络中的资源。 可以使用具有公共 IP 的应用程序网关或 Azure 负载均衡器来解决此问题。

Azure Front Door（高级）支持将流量路由到[专用链接源](concept-private-link.md)。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front Door 有哪些超时设置和限制？

了解文档中所述的所有 [Azure Front Door 的超时和限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)。

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>将某个规则添加到 Front Door 规则引擎后，该规则在多久后生效？

大多数规则引擎配置更新在 20 分钟内完成。 更新完成后，规则应该就会立即生效。 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>是否可以在 Front Door 配置文件后面配置 Azure CDN，或者在 Azure CDN 后面配置 Front Door？

不能同时配置 Azure Front Door 和 Azure CDN，因为这两个服务在响应请求时使用相同的 Azure 边缘站点。 

## <a name="performance"></a>性能

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure Front Door 如何支持高可用性和可伸缩性？

Azure Front Door 是全球分布式多租户平台，具有巨大的容量，可满足应用程序的可伸缩性需求。 Front Door 部署在 Microsoft 全球网络的边缘，提供全局负载均衡功能，可让你跨区域或不同的云故障转移整个应用程序甚至单个微服务。

## <a name="tls-configuration"></a>TLS 配置

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front Door 支持哪些 TLS 版本？

在 2019 年 9 月之后创建的所有 Front Door 配置文件使用 TLS 1.2 作为默认的最低版本。

Front Door 支持 TLS 版本 1.0、1.1 和 1.2。 目前尚不支持 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure Front Door 支持哪些证书？

若要在 Front Door 自定义域上启用 HTTPS 协议，可以选择由 Azure Front Door 管理的证书，或使用自己的证书。
Front Door 托管选项通过 Digicert 预配标准的 TLS/SSL 证书，并将其存储在 Front Door 的密钥保管库中。 如果你选择使用自己的证书，则可以从支持的 CA 加入证书，该证书可以是标准 TLS、扩展的验证证书，甚至通配符证书。 不支持自签名证书。

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door 是否支持自动轮换证书？

使用 Front Door 托管证书选项时，Front Door 会自动轮换证书。 如果你使用 Front Door 托管的证书并发现证书过期日期少于 60 天，请提交支持票证。

使用你自己的自定义 TLS/SSL 证书时，不支持自动轮换。 与首次设置给定的自定义域时一样，需要将 Front Door 指向密钥保管库中的正确证书版本。 确保 Front Door 的服务主体仍有权访问密钥保管库。 只要证书的使用者名称或 SAN 不会更改，Front Door 执行的这项已更新的证书推出操作就不会在生产过程中造成任何停机时间。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure Front Door 支持哪些最新的加密套件？

对于 TLS1.2，支持的加密套件如下： 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

在启用 TLS1.0/1.1 的情况下使用自定义域时，支持以下加密套件：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>是否可以配置 TLS 策略来控制 TLS 协议版本？

可以使用 Azure 门户或 [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) 在自定义域 HTTPS 设置中配置 Azure Front Door 中的最低 TLS 版本。 目前可以在 1.0 和 1.2 之间进行选择。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>是否可以将 Front Door 配置为仅支持特定的加密套件？

否，不支持将 Front Door 配置为使用特定的加密套件。 你可以从证书颁发机构（例如 Verisign、Entrust 或 Digicert）获取自己的自定义 TLS/SSL 证书。 然后在生成证书时，在证书中标记特定的加密套件。 

### <a name="does-front-door-support-ocsp-stapling"></a>Front Door 是否支持 OCSP 装订？

是，默认情况下，Front Door 无需经过任何配置即可支持 OCSP 装订。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door 是否也支持重新加密发往后端的流量？

是的，Azure Front Door 支持 TLS/SSL 卸载和端到端 TLS，这会重新加密发往后端的流量。 由于与后端的连接是通过公共 IP 进行的，因此建议你将 Front Door 配置为使用 HTTPS 作为转发协议。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Front Door 是否支持在后端上使用自签名证书进行 HTTPS 连接？

否，Front Door 不支持自签名证书，此项限制适用于以下两者：

* 后端：将流量作为 HTTPS 或 HTTPS 运行状况探测转发时，或者在启用缓存的情况下为路由规则的来源填充缓存时，不能使用自签名证书。
* 前端：使用自己的自定义 TLS/SSL 证书在自定义域上启用 HTTPS 时，时，不能使用自签名证书。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>发送到后端的 HTTPS 流量为何失败？

与后端成功建立 HTTPS 连接（不管是出于转发运行状况探测还是转发请求的目的）后，有两个可能的原因会导致 HTTPS 流量失败：

* 证书使用者名称不匹配：对于 HTTPS 连接，Front Door 预期后端会出示来自有效 CA 的证书，该证书中的使用者名称与后端主机名匹配。 例如，如果后端主机名设置为 `myapp-centralus.contosonews.net`，而后端在 TLS 握手期间出示的证书的使用者名称不包含 `myapp-centralus.contosonews.net` 或 `*myapp-centralus*.contosonews.net`。 那么，Front Door 将拒绝连接并生成错误。 
    * 解决方法：对 Front Door 禁用证书使用者名称检查可以解决此错误，不过，从合规的角度讲，我们不建议这样做。 可以在 Azure 门户中的“设置”下以及在 API 中的“BackendPoolsSettings”下找到此选项。
* 后端宿主证书来自无效的 CA：在包含 Front Door 的后端上只能使用来自[有效证书颁发机构](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)的证书。 不允许来自内部 CA 的证书或自签名证书。

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>是否可以对 Azure Front Door 使用客户端/相互身份验证？

不知道。 尽管 Azure Front Door 支持 [RFC 5246](https://tools.ietf.org/html/rfc5246) 中的 TLS 1.2（引入了客户端/相互身份验证），但目前 Azure Front Door 并不支持客户端/相互身份验证。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure Front Door 提供哪些类型的指标和日志？

有关日志和其他诊断功能的信息，请参阅“监视 Front Door 的指标和日志”。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>什么是诊断日志的保留策略？

诊断日志将发往客户存储帐户，客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅 [Azure Front Door 日志记录](how-to-logs.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>如何获取 Azure Front Door 的审核日志？

为 Azure Front Door 提供了审核日志。 在门户中，在 Front Door 的菜单页中选择“活动日志”即可访问审核日志。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>是否可对 Azure Front Door 设置警报？

是，Azure Front Door 支持警报。 警报是针对指标配置的。 

## <a name="billing"></a>计费

### <a name="will-i-be-billed-for-the-azure-front-door-resources-that-are-disabled"></a>是否会对已禁用的 Azure Front Door 资源计费？

Azure Front Door 资源（如 Front Door 配置文件）在禁用时不计费。

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door 标准版/高级版](create-front-door-portal.md)。
