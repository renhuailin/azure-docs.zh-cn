---
title: Azure DDoS 防护标准概述
description: 了解 Azure DDoS 防护标准在与应用程序设计最佳做法相结合时如何防范 DDoS 攻击。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 2cee2aed6922a5a5d3f457c9f14fb54c07b18cd3
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310499"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS 保护标准概述

分布式拒绝服务 (DDoS) 攻击是将应用程序移动到云的客户所面临的一些最大的可用性和安全性问题。 DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

Azure 中的每个属性均免费受 Azure 的基础结构 DDoS（基本）防护所保护。 全球部署的 Azure 网络的规模和容量通过始终开启的监视和实时缓解措施，来防御公用网络层攻击。 基本 DDoS 防护不需要对用户配置或应用程序做出任何更改。 基本 DDoS 防护会帮助保护所有 Azure 服务，包括 Azure DNS 等 PaaS 服务。

Azure DDoS 防护标准与应用程序设计最佳做法相结合，提供增强的 DDoS 缓解功能来防御 DDoS 攻击。 这种防护自动经过优化，可帮助保护虚拟网络中的特定 Azure 资源。 可在任何新的或现有的虚拟网络上启用保护，且无需对应用程序或资源做出任何更改。 与基本服务相比，该服务具有多种优势，包括日志记录、警报和遥测。 

![Azure DDoS 防护服务比较](./media/ddos-protection-overview/ddos-comparison.png)

否，Azure DDoS 防护不存储客户数据。

## <a name="features"></a>功能

- **本机平台集成：** 本机集成到 Azure 中。 包括通过 Azure 门户进行配置。 DDoS 保护标准了解你的资源和资源配置。
- **统包保护：** 一旦启用 DDoS 防护标准，简化的配置会立即保护虚拟网络上的所有资源。 要求没有干预或用户定义。 
- **始终可用的流量监控：** 应用程序流量模式将全天候受到监控，以寻找 DDoS 攻击的迹象。 一旦检测到攻击，标准 DDoS 保护会立即自动减轻攻击。
- **自适应优化：** 智能流量分析了解一段时间的应用程序流量，并选择和更新最适合服务的配置文件。 当流量随时间变化时，配置文件将进行调整。
- **多层保护：** 与 Web 应用程序防火墙 (WAF) 一起部署时，DDoS 防护标准在网络层（由 Azure DDoS 防护标准提供支持的第 3 层和第 4 层）和应用程序层（由 WAF 提供支持的第 7 层）均提供保护。 WAF 产品/服务包括 Azure [应用程序网关 WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中提供的第三方 Web 应用程序防火墙产品/服务。
- **广泛的缓解规模：** 可以使用全球容量缓解超过 60 种不同攻击类型，从而防止最大的已知 DDoS 攻击。
- **攻击分析**：在攻击期间以五分钟为增量获取详细报告，在攻击结束后获取完整摘要。 将缓解流日志流式传输到 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) 或离线安全信息和事件管理 (SIEM) 系统，以便在攻击期间进行近实时监视。
- **攻击指标：** 可以通过 Azure Monitor 访问每个攻击的汇总指标。
- **攻击警报：** 可以使用内置攻击指标在攻击开始和停止时以及攻击持续期间配置警报。 警报会集成到操作软件中，如 Microsoft Azure Monitor 日志、Splunk、Azure 存储、电子邮件和 Azure 门户。
- **DDoS 快速响应**：联系 DDoS 防护快速响应 (DRR) 团队，以获取攻击调查和分析帮助。 要了解详细信息，请参阅 [DDoS 快速响应](ddos-rapid-response.md)。
- **成本保证：** 获得因记录的 DDoS 攻击而产生的资源成本的数据传输和应用程序横向扩展服务额度。

## <a name="pricing"></a>定价

DDoS 防护计划的每月固定费用为 2,944 美元/月，涵盖最多 100 个公共 IP 地址。 保护更多资源将使每个资源每月额外花费 30 美元。

在租户下，可以跨多个订阅使用单个 DDoS 防护计划，因此无需创建多个 DDoS 防护计划。

要了解 Azure DDoS 防护标准定价，请参阅 [Azure DDoS 防护标准定价](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="reference-architectures"></a>参考体系结构

DDoS 防护标准面向[虚拟网络中部署的服务](../virtual-network/virtual-network-for-azure-services.md)。 对于其他服务，将会应用默认的基本 DDoS 防护服务。 要详细了解支持的体系结构，请参阅 [DDoS 防护参考体系结构](./ddos-protection-reference-architectures.md)。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建 DDoS 防护计划](manage-ddos-protection.md)