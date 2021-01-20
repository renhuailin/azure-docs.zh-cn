---
title: Azure DDoS 保护标准常见问题
description: Azure DDoS 保护标准常见问题解答，有助于提供对 DDoS 攻击的防御。
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 89685d1b8c3a57fa142bbbfd1114f9aa0ff0c400
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611111"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS 保护标准常见问题

本文解答了有关 Azure DDoS 保护标准的常见问题。 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>什么是分布式拒绝服务 (DDoS) 攻击？
分布式拒绝服务（DDoS）是一种攻击，其中攻击者向应用程序发送的请求数超过了应用程序的处理能力。 产生的影响是资源耗尽，这会影响应用程序的可用性并为其客户提供服务。 在过去几年中，该行业已经明显增加了攻击，而且攻击变得越来越复杂，更大。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

## <a name="what-is-azure-ddos-protection-standard-service"></a>什么是 Azure DDoS 保护标准服务？
Azure DDoS 保护标准与应用程序设计最佳做法相结合，提供增强的 DDoS 缓解功能，防范 DDoS 攻击。 它会自动进行优化，以帮助保护虚拟网络中的特定 Azure 资源。 可在任何新的或现有的虚拟网络上启用保护，且无需对应用程序或资源做出任何更改。 与基本服务相比，该服务具有多种优势，包括日志记录、警报和遥测。 有关更多详细信息，请参阅 [Azure DDoS 保护标准概述](ddos-protection-overview.md) 。 

## <a name="how-does-pricing-work"></a>如何定价？
DDoS 保护计划的每月固定费用为 $2944，涵盖最多100个公共 IP 地址。 对于其他资源的保护，每个资源每月附加 $30。 

在租户下，可以跨多个订阅使用单个 DDoS 防护计划，因此无需创建多个 DDoS 防护计划。

有关更多详细信息，请参阅 [Azure DDoS 保护标准定价](https://azure.microsoft.com/pricing/details/ddos-protection/) 。

## <a name="is-the-service-zone-resilient"></a>服务区域是否能复原？
是的。 Azure DDoS 保护默认情况下是区域复原保护。

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>如何实现将服务配置为区域复原？
客户无需配置即可启用区域复原能力。 Azure DDoS 保护资源的区域复原默认情况下提供，由服务自身管理。

## <a name="what-about-protection-at-the-service-layer-layer-7"></a> (第7层) ，服务层上的保护是怎样的？
客户可将 Azure DDoS 保护服务与 Web 应用程序防火墙结合使用 (WAF) ，以保护 (第3层和第4层提供的网络层，第3层和第4层 () 提供此功能。 WAF 产品/服务包括 Azure [应用程序网关 WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 以及 [azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中提供的第三方 web 应用程序防火墙产品。

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Azure 中是否不包含服务的服务不安全？
在 Azure 上运行的服务在本质上受 Azure DDoS 保护基本保护，并已准备好保护 Azure 的基础结构。 但是，保护基础结构的保护的阈值要高得多，但它并不提供遥测或警报，因此，在接收流量时，可能会对接收它的应用程序造成破坏。 

通过载入 Azure DDoS 保护标准服务，应用程序可获取专用监视以检测攻击和应用程序特定的阈值。 服务将使用一个配置文件进行保护，该配置文件会优化到其预期的流量量，从而更严格防御 DDoS 攻击。

## <a name="what-are-the-supported-protected-resource-types"></a>受支持的受保护资源类型有哪些？
基于 ARM 的 Vnet 中的公共 Ip 目前是唯一受保护的资源类型。 PaaS 服务 (不支持多租户) 。 有关更多详细信息，请参阅 [Azure DDoS 保护标准参考体系结构](ddos-protection-reference-architectures.md) 。

## <a name="are-classicrdfe-protected-resources-supported"></a>是否支持经典/RDFE 受保护的资源？
预览版仅支持基于 ARM 的受保护资源。 不支持经典/RDFE 部署中的 Vm。 目前尚不打算针对经典/RDFE 资源提供支持。 有关更多详细信息，请参阅 [Azure DDoS 保护标准参考体系结构](ddos-protection-reference-architectures.md) 。

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>是否可以使用 DDoS 保护来保护 PaaS 资源？
目前不支持附加到多租户、单 VIP PaaS 服务的公共 Ip。 不受支持的资源的示例包括存储 Vip、事件中心 Vip 和应用/云服务应用程序。 有关更多详细信息，请参阅 [Azure DDoS 保护标准参考体系结构](ddos-protection-reference-architectures.md) 。

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>能否使用 DDoS 保护来保护本地资源？
需要将服务的公共终结点关联到 Azure 中的 VNet，才能启用 DDoS 保护。 示例设计包括：
- 网站 (本地 datacenter 中的 Azure 和后端数据库中的 IaaS) 。 
- Azure 中的应用程序网关 (在本地数据中心的应用网关/WAF) 和网站上启用 DDoS 保护。

有关更多详细信息，请参阅 [Azure DDoS 保护标准参考体系结构](ddos-protection-reference-architectures.md) 。

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>能否在 Azure 外部注册某个域，并将其关联到受保护的资源（例如 VM 或 ELB）？
对于公共 IP 方案，DDoS 保护服务将支持任何应用程序，无论关联域在何处注册或托管，只要关联的公共 IP 托管在 Azure 上。 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>是否可以手动配置应用于 Vnet/公共 Ip 的 DDoS 策略？
不，很遗憾，策略自定义目前不可用。

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>能否允许列表/阻止列表特定的 IP 地址？
不可以，很遗憾，手动配置目前不可用。

## <a name="how-can-i-test-ddos-protection"></a>如何测试 DDoS 防护？
请参阅 [通过模拟进行测试](test-through-simulations.md)。

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>在门户上加载指标需要多长时间？
指标应在5分钟内显示在门户上。 如果资源受到攻击，其他度量值将在5-7 分钟内开始显示在门户上。 

## <a name="does-the-service-store-customer-data"></a>服务是否存储客户数据？
不，Azure DDoS 保护不存储客户数据。
    
