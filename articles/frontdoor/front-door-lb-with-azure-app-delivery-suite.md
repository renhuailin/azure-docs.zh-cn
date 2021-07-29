---
title: Azure Front Door - 通过 Azure 的应用程序交付套件进行负载均衡 | Microsoft Docs
description: 本文介绍 Azure 建议通过其应用程序传送套件进行负载均衡
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2021
ms.author: duau
ms.openlocfilehash: 9910cadfcbb40bbb411c8d07bc73ad2205c085e5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070509"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>使用 Azure 的应用程序传送套件进行负载均衡

## <a name="introduction"></a>简介
Microsoft Azure 提供了多种全球和区域服务，用于管理网络流量的分布和负载均衡方式： 

* 应用程序网关
* Front Door 
* 负载均衡器  
* 流量管理器

将这些服务与 Azure 的许多区域和分区体系结构一起使用，能够生成可靠且可缩放的高性能应用程序。

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="应用程序传送套件":::
 
这些服务分为两个类别：
1. 负载均衡服务（例如流量管理器和 Front Door），此类服务将最终用户的流量跨区域后端、云和混合本地服务分布。 全局负载均衡将流量路由到最近的服务后端，并对服务可靠性的更改作出响应，从而为用户提供 Always On 可用性和高性能。 
1. 区域负载均衡服务（例如负载均衡器和应用程序网关）可以将流量分配到虚拟网络 (VNET) 中的虚拟机 (VM) 或区域内的服务终结点。

当你组合使用这些全局和区域服务时，你的应用程序将从可靠且安全的端到端流量（从你的最终用户发送到 IaaS、PaaS 或本地服务）中获益。 下一部分介绍上述各项服务。

## <a name="global-load-balancing"></a>全局负载均衡
**流量管理器** 提供全局 DNS 负载均衡。 它着眼于传入的 DNS 请求，并根据客户选择的路由策略使用正常的后端进行响应。 路由方法选项包括：
- “性能路由”将请求发送到延迟最低的最近的后端。
- “优先级路由”将所有流量定向到一个后端，其他后端作为备份。
- 加权轮循机制路由根据分配给每个后端的权重进行流量分布。
- 地理路由可确保由为这些区域映射的后端来处理来自特定地理区域的请求。 （例如，来自西班牙的所有请求应定向到法国中部 Azure 区域）
- 通过子网路由，你可以将 IP 地址范围映射到后端，以便将这些 IP 的传入请求发送到特定的后端。 （例如，从公司总部的 IP 地址范围进行连接的任何用户都应该获取不同于一般用户的 Web 内容）

客户端直接连接到该后端。 当某个后端运行不正常时，Azure 流量管理器可以检测到这种状态，然后将客户端重定向到另一个正常的实例。 若要详细了解此服务，请参阅 [Azure 流量管理器文档](../traffic-manager/traffic-manager-overview.md)。

Azure Front Door 提供包括全局 HTTP 负载平衡在内的动态网站加速 (DSA)。  它着眼于为指定的主机名、URL 路径和配置的规则路由到最近后端/区域的传入 HTTP 请求。  
Front Door 在 Microsoft 网络边缘终止 HTTP 请求，并主动进行探测以检测应用程序或基础结构在运行状况或延迟方面的变化。  然后始终将流量路由到可用（正常运行）的最远后端。 参阅 Front Door 的[路由基础结构](front-door-routing-architecture.md)详细信息以及[流量路由方法](front-door-routing-methods.md)深入了解该服务。

## <a name="regional-load-balancing"></a>区域负载均衡
应用程序网关以服务形式提供应用程序传送控制器 (ADC)，借此为应用程序提供许多第 7 层负载均衡功能。 客户可以用它将 CPU 密集型 TLS 终止卸载到应用程序网关，优化 Web 场工作效率。 其他额外的第 7 层路由功能还包括传入流量的轮循机制分布、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后托管多个网站的功能。 可将应用程序网关配置为面向 Internet 的终结点、仅限内部访问的终结点或二者合一的终结点。 应用程序网关完全由 Azure 管理，为你提供可伸缩性和高可用性。 它提供丰富的诊断和日志记录功能以改进可管理性。

负载均衡器是 Azure SDN 堆栈的组成部分，为所有 UDP 和 TCP 协议提供高性能、低延迟的第 4 层负载均衡服务。 可以通过定义将入站连接映射到后端池的规则来配置公共或内部负载均衡终结点。 通过利用 TCP 或 HTTPS 的运行状况探测监视，可帮助你管理服务可用性。

## <a name="choosing-a-global-load-balancer"></a>选择全局负载均衡器
选择是将负载均衡器还是 Azure Front Door 用作全局负载均衡器时，应考虑这两个服务的异同点。   它们均提供以下功能：
- 多地冗余：如果一个区域出现故障，则将流量无缝路由到最近的区域，无需应用程序所有者做出任何干预。
- **最近区域路由：** 自动将流量路由到最近的区域

</br>下表说明流量管理器和 Azure Front Door 之间的差异：</br>

| 流量管理器 | Azure Front Door |
| --------------- | ------------------------ |
|任何协议：流量管理器在 DNS 层运行，因此可以路由任何类型的网络流量，比如 HTTP、TCP、UDP 等。 | HTTP 加速：在 Microsoft 网络边缘代理 Front Door 流量。 HTTP/S 请求在延迟和吞吐量方面有所改善，降低了 TLS 协商的延迟。|
|本地路由：在 DNS 层进行路由，始终点到点地路由流量。  即使在自己的网络上使用流量管理器，从分公司路由到本地数据中心亦可采用一条直接的路径。 | 独立的可伸缩性：Front Door 适用于 HTTP 请求，因此，可根据各个应用程序微服务的规则和运行状况，将路由到不同 URL 路径的请求路由到不同的后端/区域服务池（微服务）。|
|计费形式：基于 DNS 的计费随用户数增加，适用于具有更多用户和更高稳定性的服务，从而为使用率较高的情况降低费用。 |**内联安全性：** Front Door 启用流量限速和 IP ACL 等规则，使你能在流量达到应用程序前保护后端。 

</br>我们建议客户为其 HTTP 工作负载使用 Front Door，因为将 HTTP 用于 Front Door 时具有性能、可操作性和安全性优势。 流量管理器和 Front Door 可并行使用，为应用程序提供全部流量。 

## <a name="building-with-azures-application-delivery-suite"></a>使用 Azure 的应用程序传送套件进行生成 
建议所有网站、API 和服务均为地域冗余的，以便在可能的情况下将流量从距用户最近的位置传送给他们。  组合使用多个负载均衡服务，可以构建地理和区域冗余，以最大程度地提高可靠性和性能。

下图展示一个示例体系结构，它结合使用这些服务来生成一个全局 Web 服务。 体系结构使用流量管理器将流量路由到全局后端供存档和对象传送，使用 Front Door 在全局范围内将与模式 /store/* 匹配的 URL 路径路由到他们已迁移到应用服务的服务，并将所有其他请求路由到区域应用程序网关。

在 IaaS 服务的每个区域中，应用程序开发者决定，为访问与 /images/* 模式匹配的任意 URL 的用户，从 VM 专用池提供服务。 此 VM 池不同于 Web 场的其余部分。

此外，提供动态内容的默认 VM 池需要与高可用性群集上托管的后端数据库通信。 整个部署通过 Azure 资源管理器进行配置。

下图显示了此方案的体系结构：

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="应用程序传送套件详细体系结构":::

> [!NOTE]
> 此示例只是 Azure 提供的负载均衡服务的众多可能配置之一。 可采用最符合负载均衡需求的方式，混合搭配流量管理器、Front Door、应用程序网关和负载均衡器。 例如，如果 TLS/SSL 卸载或第 7 层处理没有必要，负载均衡器可以替代应用程序网关。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
