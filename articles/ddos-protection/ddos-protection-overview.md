---
title: Azure DDoS 保护标准概述
description: 了解 Azure DDoS 防护标准在与应用程序设计最佳做法相结合时如何防范 DDoS 攻击。
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 749d8d6edf9c94185c8363c5fec1b91e60ae9272
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746465"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS 保护标准概述

分布式拒绝服务 (DDoS) 攻击是将应用程序移动到云的客户所面临的一些最大的可用性和安全性问题。 DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

Azure 中的每个属性都受 Azure 的基础结构 DDoS 保护 (基本) 保护，无需额外付费。 全球部署的 Azure 网络的规模和容量通过始终开启的监视和实时缓解措施，来防御公用网络层攻击。 基本 DDoS 防护不需要对用户配置或应用程序做出任何更改。 基本 DDoS 防护会帮助保护所有 Azure 服务，包括 Azure DNS 等 PaaS 服务。

Azure DDoS 保护标准与应用程序设计最佳做法相结合，提供增强的 DDoS 缓解功能，防范 DDoS 攻击。 它会自动进行优化，以帮助保护虚拟网络中的特定 Azure 资源。 可在任何新的或现有的虚拟网络上启用保护，且无需对应用程序或资源做出任何更改。 与基本服务相比，该服务具有多种优势，包括日志记录、警报和遥测。 

![Azure DDoS 保护服务比较](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS 保护不存储客户数据。

## <a name="features"></a>功能

- **本机平台集成：** 本机集成到 Azure 中。 包括通过 Azure 门户进行配置。 DDoS 保护标准了解你的资源和资源配置。
- **全包式保护：** 启用 DDoS 保护标准后，简化的配置会立即保护虚拟网络上的所有资源。 要求没有干预或用户定义。 一旦检测到攻击，标准 DDoS 保护会立即自动减轻攻击。
- **Always on 流量监视：** 每周7天，你的应用程序流量模式每周监视24小时，寻找 DDoS 攻击的迹象。 将在超出保护策略范围时执行缓解措施。
- **自适应优化：** 智能流量分析了解应用程序在一段时间内的流量，并选择和更新最适合服务的配置文件。 当流量随时间变化时，配置文件将进行调整。
- **多层保护：** 在与 web 应用程序防火墙一起使用时，提供完整的堆栈 DDoS 保护，同时在由 Azure DDoS 保护标准) 提供的网络 (层和 WAF) 提供的应用程序层 (第7层获取保护。 WAF 产品/服务包括 Azure [应用程序网关 WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 以及 [azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中提供的第三方 web 应用程序防火墙产品。
- **广泛的缓解规模：** 可以使用全球容量缓解超过 60 种不同攻击类型，从而防止最大的已知 DDoS 攻击。
- **攻击分析**：在攻击期间以五分钟为增量获取详细报告，在攻击结束后获取完整摘要。 将 SIEM) system 的流缓解流日志流式传输到 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) 或脱机安全信息和事件管理 (在攻击期间进行近乎实时的监视。
- **攻击指标：** 可以通过 Azure Monitor 访问每个攻击的汇总指标。
- **攻击警报：** 可以使用内置攻击指标在攻击开始和停止时以及攻击持续期间配置警报。 警报集成到操作软件，如 Microsoft Azure 监视日志、Splunk、Azure 存储、电子邮件和 Azure 门户。
- **Ddos 快速响应**：将 Ddos 防护快速响应 (DRR) 团队，以帮助进行攻击调查和分析。 若要了解详细信息，请参阅 [DDoS 快速响应](ddos-rapid-response.md)。
- **成本保证：** 针对所述 DDoS 攻击的数据传输和应用程序扩展服务信用额度。

## <a name="pricing"></a>定价

若要了解有关 Azure DDoS 保护标准定价的信息，请参阅 [Azure Ddos 保护标准定价](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建 DDoS 保护计划](manage-ddos-protection.md)