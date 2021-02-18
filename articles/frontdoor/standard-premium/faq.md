---
title: Azure 前门：常见问题
description: 本页提供有关 Azure 前门标准/高级版常见问题的解答。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a42601b696f292e9d2a9da90070fea3662acae87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098681"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Azure 前门标准/高级 (预览版常见问题解答) 

本文解答了有关 Azure 前门功能的常见问题。

## <a name="general"></a>常规

### <a name="what-is-azure-front-door"></a>什么是 Azure Front Door？

Azure 前门是一种快速、可靠且安全的新式云 CDN，具有智能威胁防护。 它提供静态和动态内容加速、全局负载均衡，以及具有智能威胁防护功能的全局超大规模应用程序、Api、网站和云服务的增强安全性。

### <a name="what-features-does-azure-front-door-support"></a>Azure 前门支持哪些功能？

Azure 前门支持：

* 静态内容和动态应用程序加速
* TLS/SSL 卸载和端到端 TLS
* Web 应用程序防火墙
* 基于 Cookie 的会话关联
* 基于 Url 路径的路由
* 免费证书和多个域管理

有关支持的功能的完整列表，请参阅 [Azure 前门概述](overview.md)。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure 前门与 Azure 应用程序网关之间的区别是什么？

尽管前门和应用程序网关是第7层 (HTTP/HTTPS) 负载均衡器，但主要差别在于，前门是一项全局服务。 应用程序网关是一种区域服务。 尽管前门可以在不同区域中的不同缩放单位/分类/戳记单元之间进行负载平衡，但应用程序网关允许在规模单位内的 Vm/容器之间进行负载平衡。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>何时应在前门后部署应用程序网关？

关键情况是应该使用应用程序网关的前端的情况如下：

* 前门只能在全局级别执行基于路径的负载均衡，但如果要在虚拟网络中进一步平衡流量 (VNET) ，则应使用应用程序网关。
* 由于前门在 VM/容器级别不起作用，因此不能进行连接排出。 但是，应用程序网关允许进行连接排出。 
* 使用前门之前的应用程序网关，可以实现100% 的 TLS/SSL 卸载并仅在其虚拟网络 (VNET) 中路由 HTTP 请求。
* 前门和应用程序网关都支持会话相关性。 前门可以将来自用户会话的流量定向到给定区域中的同一群集或后端。 应用程序网关可以将流量定向到群集中的同一服务器。关联  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>是否可以在前门后部署 Azure 负载均衡器？

Azure 前门需要公共 VIP 或公开提供的 DNS 名称才能将流量路由到。 在前门后部署 Azure 负载均衡器是一个常见的用例。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure 前门支持哪些协议？

Azure 前门支持 HTTP、HTTPS 和 HTTP/2。

### <a name="how-does-azure-front-door-support-http2"></a>Azure 前门如何支持 HTTP/2？

HTTP/2 协议支持仅适用于连接到 Azure 前门的客户端。 与后端池中后端的通信通过 HTTP/1.1 进行。 默认情况下，启用 HTTP/2 支持。

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>目前支持哪些资源作为源组的一部分？

源组可以包含存储、Web 应用、Kubernetes 实例或具有公共连接的任何其他自定义主机名。 Azure 前门要求源通过公共 IP 或可公开解析的 DNS 主机名进行定义。 源组的成员可以跨区域、区域甚至在 Azure 外部，前提是它们具有公用连接。

### <a name="what-regions-is-the-service-available-in"></a>该服务已在哪些区域推出？

Azure 前门是一种全球服务，并不受限于任何特定的 Azure 区域。 为资源组创建前门时需要指定的唯一位置。 此位置本质上指定了将存储资源组元数据的位置。 前门资源本身作为全局资源创建，并将配置全局部署到所有 Pop (点) 。 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure 前门的 POP 位置有哪些？

Azure 前门与 Microsoft 中的 Azure CDN 具有相同的 POP (点列表) 位置。 有关 Pop 的完整列表，请参阅 [Microsoft AZURE CDN pop 位置](../../cdn/cdn-pop-locations.md)。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure 前门是应用的专用部署，还是在客户之间共享？

Azure 前门是全球分布的多租户服务。 前门的基础结构在其所有客户之间共享。 通过创建前门配置文件，可以定义应用程序所需的特定配置。 对前门所做的更改不会影响其他前门配置。

### <a name="is-http-https-redirection-supported"></a>是否支持 HTTP 到 HTTPS 的重定向？

是。 事实上，Azure 前门支持主机、路径、查询字符串重定向和 URL 重定向的一部分。 了解有关 [URL 重定向](concept-rule-set-url-redirect-and-rewrite.md)的详细信息。 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>如何实现锁定对我的后端的访问仅限于 Azure 前门？

若要将应用程序锁定为仅接受来自特定前门的流量，需要为后端设置 IP Acl。 然后将后端的流量限制为前门发送的标头 "FDID" 的特定值。 下面详细说明了这些步骤：

* 为后端配置 IP Acl，以接受来自 Azure 前门的后端 IP 地址空间和 Azure 的基础结构服务的流量。 有关 Acl 后端的信息，请参阅以下 IP 详细信息：
 
    * 请参阅 [AZURE IP 范围](https://www.microsoft.com/download/details.aspx?id=56519)中的 *AzureFrontDoor* ，以及前门的 IPv4 后端 IP 地址范围的服务标记。 你还可以使用 [网络安全组](../../virtual-network/network-security-groups-overview.md#security-rules)中的服务标记 *AzureFrontDoor* 。
    * Azure 的 [基本基础结构服务](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) ，通过虚拟化主机 IP 地址： `168.63.129.16` 和 `169.254.169.254` 。

    > [!WARNING]
    > 前端的后端 IP 空间可能会更改，但在此之前，我们将确保我们已与 [AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)集成。 建议订阅 [AZURE IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519) 以进行任何更改或更新。

* 使用 API 版本或更高版本在前门上执行 GET 操作 `2020-01-01` 。 在 API 调用中，查找 " `frontdoorID` 字段"。 筛选由前门发送到后端的传入标头 "**X-FDID**"，其值为 "字段" `frontdoorID` 。 你还可以 `Front Door ID` 在前门门户页面的 "概述" 部分下找到值。 

* 应用后端 web 服务器中的规则筛选，基于生成的 "FDID" 标头值限制流量。

  下面是 [Microsoft Internet Information Services (IIS) ](https://www.iis.net/)的示例：

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

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>可播 IP 是否可以在前端的生存期内更改？

前门的前端任意播 IP 通常不会更改，并且可能会在前门的生存期内保持静态。 但是， **不保证** 相同。 请不要对 IP 进行任何直接依赖。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure 前门是否支持静态或专用 Ip？

不，Azure 前门目前不支持静态或专用前端任意播 Ip。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure 前门是否支持 x 转发的标头？

是的，Azure 前门支持 X 转发的、X 转发的主机和 X 转发的标头。 对于 X 转发-如果标头已存在，则前门会将客户端套接字 IP 追加到该标头。 否则，会添加标头，其中包含客户端套接字 IP 作为值。 对于 X 转发主机和 X-正向-Proto，该值将被重写。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>部署 Azure 前门需要多长时间？ 更新时，前门是否仍然有效？

对于全球部署，新的前门创建或对现有前门的任何更新大约需要3到5分钟。 这意味着，在大约3到5分钟内，你的前门配置将在整个所有 Pop 中进行部署。

注意-自定义 TLS/SSL 证书更新需要在全球部署大约30分钟。

路由或后端池的任何更新都是无缝的，如果新配置是正确的) ，将导致零停机 (。 证书更新不会导致任何中断，除非你要从 "管理的 Azure 前门" 切换到 "使用你自己的证书" 或其他方法。


## <a name="configuration"></a>配置

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 前门是否可以在虚拟网络中对流量进行负载均衡或路由

Azure 前门 (AFD) 需要公共 IP 或可公开解析的 DNS 名称来路由流量。 Azure 前门无法直接路由到虚拟网络中的资源。 可以使用应用程序网关或带有公共 IP 的 Azure 负载均衡器来解决此问题。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure 前门有哪些不同的超时和限制？

了解 Azure 前门的所有已记录的 [超时和限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)。

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>将规则添加到前门规则引擎后，规则需要多长时间？

规则引擎配置需要大约10到15分钟的时间来完成更新。 当更新完成后，你可能会希望该规则生效。 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>是否可以在我的 Azure CDN 后配置前门配置文件或前门后 Azure CDN？

Azure 前门和 Azure CDN 无法一起配置，因为在响应请求时，两个服务使用相同的 Azure 边缘站点。 

## <a name="performance"></a>性能

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure 前门如何支持高可用性和可伸缩性？

Azure 前门是全球分布的多租户平台，具有巨大的容量，可满足应用程序的可伸缩性需求。 从 Microsoft 全球网络的边缘提供，前门提供全局负载平衡功能，使你可以故障转移整个应用程序甚至跨区域或不同云的单个微服务。

## <a name="tls-configuration"></a>TLS 配置

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure 前门支持哪些 TLS 版本？

2019年9月之后创建的所有前门配置文件都使用 TLS 1.2 作为默认的最小值。

前门支持 TLS 版本1.0、1.1 和1.2。 目前尚不支持 TLS 1.3。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure 前门支持哪些证书？

若要在前端自定义域上启用 HTTPS 协议，可以选择由 Azure 前门管理的证书，也可以使用自己的证书。
前门托管选项通过 Digicert 预配标准的 TLS/SSL 证书，并将其存储在前门的 Key Vault 中。 如果选择使用你自己的证书，则可以从受支持的 CA 载入证书，并且可以是标准 TLS、扩展验证证书，甚至是通配符证书。 不支持自签名证书。

### <a name="does-front-door-support-autorotation-of-certificates"></a>前门是否支持证书 autorotation？

对于前门托管证书选项，证书是由前门 autorotated 的。 如果你使用的是前门托管证书，并发现证书到期日期小于60天，请提交支持票证。

对于自定义的 TLS/SSL 证书，不支持 autorotation。 与第一次为给定自定义域设置的方式类似，需要将前门指向 Key Vault 中正确的证书版本。 确保前门的服务主体仍有权访问 Key Vault。 此更新后的证书推出操作不会导致任何生产停止时间，前提是证书的使用者名称或 SAN 不会更改。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure 前门支持哪些当前密码套件？

对于 TLS 1.2，支持以下密码套件： 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

使用启用了 TLS 1.0/1.1 的自定义域时，支持以下密码套件：

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>能否配置 TLS 策略来控制 TLS 协议版本？

你可以使用 Azure 门户或 [azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)在 azure 前门中配置最小 TLS 版本。 目前，可以在1.0 和1.2 之间进行选择。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>是否可以将前门配置为仅支持特定密码套件？

否，不支持配置特定密码套件的前门。 你可以从证书颁发机构获取自定义的 TLS/SSL 证书， (例如 Verisign、Entrust 或 Digicert) 。 然后，在生成证书时对其标记特定密码套件。 

### <a name="does-front-door-support-ocsp-stapling"></a>前门是否支持 OCSP 装订？

是的，前端默认支持 OCSP 装订，无需进行任何配置。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 前门是否还支持向后端重新加密流量？

是的，Azure 前门支持 TLS/SSL 卸载和端到端 TLS，这会将流量重新加密到后端。 由于连接到后端是通过公共 IP 进行的，因此建议您将前门配置为使用 HTTPS 作为转发协议。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>前门是否支持 HTTPS 连接的后端上的自签名证书？

不支持，在前门上不支持自签名证书，并且此限制适用于这两种证书：

* **后端**：如果要将流量转发为 HTTPS 或 https 运行状况探测，或为启用了缓存的路由规则填充源缓存，则无法使用自签名证书。
* **前端**：使用自定义的 TLS/SSL 证书时，不能使用自签名证书在自定义域上启用 HTTPS。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>为什么 HTTPS 到后端的通信失败？

若要使 HTTPS 与后端的 HTTPS 连接成功，无论是针对运行状况探测还是转发请求，可能有两个原因导致 HTTPS 通信失败：

* **证书使用者名称不匹配**：对于 HTTPS 连接，前门要求后端从有效 CA 提供证书，该证书的使用者名称 (s) 与后端主机名匹配。 例如，如果后端主机名设置为 `myapp-centralus.contosonews.net` ，并且你后端在 TLS 握手期间提供的证书不具有 `myapp-centralus.contosonews.net` 或 `*myapp-centralus*.contosonews.net` 。 然后，前门将拒绝连接，并导致错误。 
    * **解决** 方法：不建议从合规性角度来解决此错误，只需对前门禁用证书使用者名称检查即可。 可以在 "设置" 下的 "Azure 门户" 和 "API 中的 BackendPoolsSettings" 下找到此选项。
* **后端托管证书来自无效 CA**：只有 [有效证书颁发机构](troubleshoot-allowed-certificate-authority.md) 提供的证书才能在前门上用于前门。 不允许来自内部 Ca 或自签名证书的证书。

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>能否对 Azure 前门使用客户端/相互身份验证？

不是。 尽管 Azure 前门支持 TLS 1.2，后者引入了 [RFC 5246](https://tools.ietf.org/html/rfc5246)中的客户端/相互身份验证，但目前，azure 前门不支持客户端/相互身份验证。

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure 前门提供哪些类型的指标和日志？

有关日志和其他诊断功能的信息，请参阅为前门监视指标和日志。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>什么是诊断日志的保留策略？

诊断日志将发往客户存储帐户，客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅 [Azure 前门日志记录](how-to-logs.md)。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>如何实现获取 Azure 前门的审核日志？

审核日志适用于 Azure 前门。 在门户中，在前门的菜单页中选择 " **活动日志** "，以访问审核日志。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>能否通过 Azure 前门设置警报？

是，Azure 前门支持警报。 警报是针对指标配置的。 

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
