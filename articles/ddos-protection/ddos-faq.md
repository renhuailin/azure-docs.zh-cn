---
title: Azure DDoS 防护标准常见问题解答
description: 有关 Azure DDoS 防护标准的常见问题解答，有助于提供对 DDoS 攻击的防御。
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98611111"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS 防护标准常见问题解答

本文解答了有关 Azure DDoS 防护标准的常见问题。 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>什么是分布式拒绝服务 (DDoS) 攻击？
分布式拒绝服务 (DDoS) 是一种攻击，其中攻击者向应用程序发送的请求数超过了应用程序的处理能力。 产生的影响是资源耗尽，从而影响应用程序的可用性以及为其客户提供服务的能力。 在过去几年中，该行业的攻击急剧增加，攻击变得越来越复杂，规模也越来越大。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

## <a name="what-is-azure-ddos-protection-standard-service"></a>什么是 Azure DDoS 防护标准服务？
Azure DDoS 防护标准与应用程序设计最佳做法相结合，提供增强的 DDoS 缓解功能来防御 DDoS 攻击。 这种防护自动经过优化，可帮助保护虚拟网络中的特定 Azure 资源。 可在任何新的或现有的虚拟网络上启用保护，且无需对应用程序或资源做出任何更改。 与基本服务相比，该服务具有多种优势，包括日志记录、警报和遥测。 有关更多详细信息，请参阅 [Azure DDoS 防护标准概述](ddos-protection-overview.md)。 

## <a name="how-does-pricing-work"></a>如何定价？
DDoS 防护计划的每月固定费用为 2,944 美元/月，涵盖最多 100 个公共 IP 地址。 保护更多资源将使每个资源每月额外花费 30 美元。 

在租户下，可以跨多个订阅使用单个 DDoS 防护计划，因此无需创建多个 DDoS 防护计划。

有关更多详细信息，请参阅 [Azure DDoS 防护标准定价](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="is-the-service-zone-resilient"></a>服务区域是否可复原？
是。 默认情况下，Azure DDoS 防护可在区域内复原。

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>如何将服务配置为可在区域内复原？
客户无需配置即可启用区域复原能力。 Azure DDoS 防护资源默认提供区域复原能力，由服务自身进行管理。

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>服务层（第 7 层）的保护怎样？
客户可以将 Azure DDoS 防护服务与 Web 应用程序防火墙 (WAF) 结合使用，以在网络层（第 3 层和第 4 层，由 Azure DDoS 防护标准提供）和应用程序层（第 7 层，由 WAF 提供）进行保护。 WAF 产品/服务包括 Azure [应用程序网关 WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中提供的第三方 Web 应用程序防火墙产品/服务。

## <a name="are-services-unsafe-in-azure-without-the-service"></a>若没有该服务，Azure 中的服务是否不安全？
在 Azure 上运行的服务本质上受 Azure DDoS 防护基本版保护，该版本可以保护 Azure 的基础结构。 但是，保护基础结构的保护的阈值比大多数具有处理能力的应用程序高得多，并且不提供遥测或警报功能，因此尽管平台可能会将流量视为无害，但对于接收该流量的应用程序来说可能是毁灭性的。 

通过加入 Azure DDoS 防护标准服务，应用程序可获取专用监视以检测攻击和应用程序特定的阈值。 服务将通过调整到其预期流量的配置文件得到保护，从而更加严密地防御 DDoS 攻击。

## <a name="what-are-the-supported-protected-resource-types"></a>受支持的受保护资源类型有哪些？
基于 ARM 的 VNet 中的公共 IP 目前是唯一受保护的资源类型。 目前不支持 PaaS 服务（多租户）。 有关更多详细信息，请参阅 [Azure DDoS 防护标准参考体系结构](ddos-protection-reference-architectures.md)。

## <a name="are-classicrdfe-protected-resources-supported"></a>是否支持经典/RDFE 保护的资源？
预览版仅支持基于 ARM 的受保护资源。 不支持经典/RDFE 部署中的 VM。 目前尚不打算针对经典/RDFE 资源提供支持。 有关更多详细信息，请参阅 [Azure DDoS 防护标准参考体系结构](ddos-protection-reference-architectures.md)。

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>是否可以使用 DDoS 防护来保护 PaaS 资源？
目前不支持附加到多租户、单 VIP PaaS 服务的公共 IP。 不受支持的资源的示例包括存储 VIP、事件中心 VIP 和应用/云服务应用程序。 有关更多详细信息，请参阅 [Azure DDoS 防护标准参考体系结构](ddos-protection-reference-architectures.md)。

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>是否可以使用 DDoS 防护来保护本地资源？
需要将服务的公共终结点关联到 Azure 中的 VNet，才能启用 DDoS 防护。 示例设计包括：
- Azure 中的网站 (IaaS) 和本地数据中心中的后端数据库。 
- Azure 中的应用程序网关（在应用程序网关/WAF 上启用了 DDoS 防护）和本地数据中心中的网站。

有关更多详细信息，请参阅 [Azure DDoS 防护标准参考体系结构](ddos-protection-reference-architectures.md)。

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>能否在 Azure 外部注册某个域，并将其关联到受保护的资源（如 VM 或 ELB）？
对于公共 IP 方案，DDoS 防护服务将支持任何应用程序，无论关联域在何处注册或托管，只要关联的公共 IP 托管在 Azure 上即可。 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>是否可以手动配置应用于 VNet/公共 IP 的 DDoS 策略？
不可以，很遗憾，策略自定义目前不可用。

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>能否将特定 IP 地址添加到允许列表/阻止列表？
不可以，很遗憾，手动配置目前不可用。

## <a name="how-can-i-test-ddos-protection"></a>如何测试 DDoS 防护？
请参阅[通过模拟进行测试](test-through-simulations.md)。

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>在门户上加载指标需要多长时间？
指标应在 5 分钟内显示在门户上。 如果资源受到攻击，其他指标将在 5-7 分钟内开始显示在门户上。 

## <a name="does-the-service-store-customer-data"></a>该服务是否存储客户数据？
否，Azure DDoS 防护不存储客户数据。
    
