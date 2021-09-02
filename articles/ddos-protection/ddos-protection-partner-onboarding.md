---
title: 通过 Azure DDoS 防护标准合作
description: 了解 Azure DDoS 防护标准启用的合作机会。
ms.service: ddos-protection
documentationcenter: na
author: aletheatoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 67b8e9e5c913323d1786427fcd102de9b98d2827
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310465"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>通过 Azure DDoS 防护标准合作
本文介绍 Azure DDoS 防护标准启用的合作机会。 本文旨在帮助产品经理和业务开发角色理解投资途径，并深入了解合作价值主张。

## <a name="background"></a>背景
分布式拒绝服务 (DDoS) 攻击是将应用程序移动到云的客户所表达的最大的可用性和安全性问题之一。 敲诈勒索和黑客主义是 DDoS 攻击的常见动机，它们在类型、规模和发生频率方面一直在不断提高，因为这种攻击发动起来相对简单且价格低廉。

Azure DDoS 防护利用 Azure 网络的全球规模，提供了针对最复杂 DDoS 威胁的应对措施。 此服务为虚拟网络中部署的应用程序和资源提供了增强的 DDoS 缓解功能。

技术合作伙伴可以通过 Azure DDoS 防护标准来保护其客户的本机资源，以解决 DDoS 攻击导致的可用性和可靠性问题。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 防护标准简介
Azure DDoS 防护标准针对第 3 层和第 4 层 DDoS 攻击提供增强的 DDoS 缓解功能。 下面是 DDoS 防护标准服务的重要功能。

### <a name="adaptive-real-time-tuning"></a>自适应实时优化
对于每个受保护的应用程序，Azure DDoS 防护标准会根据应用程序的流量配置文件模式自动优化 DDoS 缓解策略阈值。 该服务使用两项见解来实现这种自定义：

- 自动学习每个客户（每个 IP）的第 3 层和第 4 层流量模式。
- 尽量减少误报，因为 Azure 的规模可让它吸收大量的流量。

![自适应实时优化](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻击分析、遥测、监视和警报
Azure DDoS 防护将识别并缓解 DDoS 攻击，而无需任何用户干预。

- 如果受保护的资源位于 Azure 安全中心所覆盖的订阅中，则每当检测到 DDoS 攻击并针对受保护的应用程序进行缓解时，DDoS 防护标准会自动将警报发送到安全中心。
- 或者，若要在受保护公共 IP 受到的攻击被主动缓解时收到通知，可以针对是否受 DDoS 攻击指标[配置警报](alerts.md)。
- 可以额外选择针对其他 DDoS 指标创建警报和[配置攻击遥测](telemetry.md)，以了解攻击规模、丢弃的流量、攻击途径、首要参与者和其他详细信息。

![DDoS 指标](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 快速响应 (DRR)
DDoS 防护标准版客户可以在攻击正在进行时联系[快速响应团队](ddos-rapid-response.md)。 DRR 可以帮助在攻击发生期间进行攻击调查，以及进行攻击后分析。

### <a name="sla-guarantee-and-cost-protection"></a>SLA 保证和成本保护
DDoS 防护标准服务 99.99% 由 SLA 覆盖，且成本保护提供了在有记录的攻击期间横向扩展的资源额度。 有关详细信息，请参阅 [Azure DDoS 防护的 SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)。

## <a name="featured-partner-scenarios"></a>特别推荐合作伙伴方案
下面是与 Azure DDoS 防护标准集成可以获得的主要优点：

- 合作伙伴向其客户提供的服务(负载均衡器、web 应用程序防火墙、防火墙等）会自动受到后端中 Azure DDoS 防护标准保护（白色标记）。
- 合作伙伴有权访问 Azure DDoS 防护标准攻击分析和遥测，它们可以与自己的产品集成，提供统一的客户体验。  
- 即使在没有 Azure 快速响应的情况下，合作伙伴也可以访问 DDoS 快速响应支持，处理 DDoS 相关问题。
- 出现 DDoS 攻击时，合作伙伴受保护的应用程序受 DDoS SLA 保证和成本保护的支持。

## <a name="technical-integration-overview"></a>技术集成概述
Azure DDoS 防护标准合作机会可通过 Azure 门户、API 和 CLI/PS 获得。

### <a name="integrate-with-ddos-protection-standard"></a>与 DDoS 防护标准集成
若要配置与 Azure DDoS 防护标准的集成，合作伙伴需要执行以下步骤：
1. 在所需的(合作伙伴)订阅中创建 DDoS 防护计划。 有关分步说明，请参阅[创建 DDoS 标准防护计划](manage-ddos-protection.md#create-a-ddos-protection-plan)。
   > [!NOTE]
   > 对于给定租户，只需创建 1 个 DDoS 防护计划。 
2. 在(合作伙伴）订阅中部署具有公共终结点的服务，例如负载均衡器、防火墙和 web 应用程序防火墙。 
3. 使用第一步中创建的 DDoS 防护计划在具有公共终结点的服务的虚拟网络上启用 Azure DDoS 保护标准。 有关分步说明，请参阅[启用 DDoS 标准防护计划](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > 在虚拟网络上启用 Azure DDoS 防护标准后，该虚拟网络中的所有公共 IP 都自动受到保护。 这些公共 IP 的来源可以位于 Azure (客户端订阅)内或 Azure 外部。 
4. （可选）在特定于应用程序的面向客户的仪表板中集成 Azure DDoS 防护标准遥测和攻击分析。 有关使用遥测的详细信息，请参阅[查看和配置 DDoS 防护遥测](telemetry.md)。 

### <a name="onboarding-guides-and-technical-documentation"></a>加入指南和技术文档

- [Azure DDoS 防护产品页](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS 防护文档](ddos-protection-overview.md)
- [Azure DDoS 防护 API 参考](/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 虚拟网络 API 参考](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>获取帮助

- 如果对使用 Azure DDoS 防护标准的应用程序、服务或产品集成有疑问，请联系 [Azure 安全社区](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)。
- 关注 [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/) 上的讨论。

### <a name="get-to-market"></a>进入市场

- 与 Microsoft 合作的主要程序是 [Microsoft 合作伙伴网络](https://partner.microsoft.com/)。 – Microsoft Graph 安全集成，请参阅 [MPN 独立软件供应商 (ISV)](https://partner.microsoft.com/saas-solution-guide)。
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) 是专门为 Microsoft 安全合作伙伴提供的程序，旨在帮助用户丰富安全产品并提高客户发现与 microsoft 安全产品的集成的能力。

## <a name="next-steps"></a>后续步骤
查看现有合作伙伴集成：

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [Radware 的 Azure 云 WAF](https://www.radware.com/resources/microsoft-azure/)
