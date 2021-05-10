---
title: Azure DDoS 防护功能
description: 了解 Azure DDoS 防护功能
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 602bb98f2cdc8a96874eba8dadfa33f3267d19ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746552"
---
# <a name="azure-ddos-protection-standard-features"></a>Azure DDoS 防护标准功能

以下部分概述了标准 Azure DDoS 防护服务的重要功能。

## <a name="always-on-traffic-monitoring"></a>始终可用的流量监控

DDoS 保护标准监控实际流量利用率，并不断将其与 DDoS 策略中定义的阈值进行比较。 当超过流量阈值时，将自动启动 DDoS 缓解。 当流量返回到阈值以下时，缓解将停止。

![Azure DDoS 防护标准缓解](./media/ddos-protection-overview/mitigation.png)

在缓解期间，DDoS 防护服务会重定向发送到受保护资源的流量，并会执行多项检查，例如：

- 确保数据包符合 Internet 规范且格式正确。
- 与客户端交互，确定该流量是否可能是欺骗性数据包（例如：SYN Auth 或 SYN Cookie，或者通过丢弃数据包，让源重新传输它）。
- 如果没有其他可以执行的强制方法，将对数据包进行速率限制。

DDoS 防护会删除攻击流量并将剩余流量转发至其预期目标。 在检测到攻击的几分钟内，系统会使用 Azure Monitor 指标通知你。 通过在 DDoS 保护标准遥测上配置日志记录，可将日志写入可用选项以供将来分析。 Azure Monitor 中 DDoS 保护标准的指标数据会保留 30 天。

## <a name="adaptive-real-time-tuning"></a>自适应实时优化

Azure DDoS 防护基本服务可帮助保护客户，并防止影响其他客户。 例如，如果为典型的合法传入流量预配了某个服务，并且该流量小于基础结构范围 DDoS 防护策略的触发率，那么，针对该客户资源的 DDoS 攻击可能会被忽略。 更一般地说，最近发生的攻击（例如多向量 DDoS）的复杂性，以及租户的应用程序特定行为，要求按客户采用定制的保护策略。 该服务使用两项见解来实现这种定制：

- 自动学习每个客户（每个公共 IP）的第 3 层和第 4 层流量模式。

- 尽量减少误报，因为 Azure 的规模可让它吸收大量的流量。

![标准 DDoS 防护工作原理示意图，其中圈住了“策略生成”](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS 防护遥测、监视和警报

标准 DDoS 防护通过 [Azure Monitor](../azure-monitor/overview.md) 公开丰富的遥测数据。 可以针对 DDoS 防护使用的任何 Azure Monitor 指标配置警报。 可将日志记录集成到 Splunk（Azure 事件中心）、Azure Monitor 日志和 Azure 存储，以通过 Azure Monitor 诊断界面进行高级分析。

### <a name="ddos-mitigation-policies"></a>DDoS 缓解策略

在 Azure 门户中，选择“监视” > “指标”。  在“指标”窗格上，依次选择资源组、“公共 IP 地址”资源类型和 Azure 公共 IP 地址。 DDoS 指标将显示在“可用指标”窗格中。

标准 DDoS 防护针对已启用 DDoS 的虚拟网络中受保护资源的每个公共 IP，应用三个自动优化的缓解策略（TCP SYN、TCP 和 UDP）。 可以选择“触发 DDoS 缓解措施的入站数据包数”指标来查看策略阈值。

![可用指标和指标图表](./media/ddos-best-practices/image-7.png)

策略阈值是通过基于机器学习的网络流量探查自动配置的。 仅当超过策略阈值时，才会对受攻击的 IP 地址进行 DDoS 缓解。

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>受 DDoS 攻击的 IP 地址的指标

如果公共 IP 地址受到攻击，则“是否受 DDoS 攻击”指标的值将切换为 1，因为 DDoS 防护会针对攻击流量执行缓解措施。

![“是否受 DDoS 攻击”指标和图表](./media/ddos-best-practices/image-8.png)

我们建议对此指标配置警报。 然后，在对公共 IP 地址执行主动的 DDoS 缓解措施时会收到通知。

有关详细信息，请参阅[使用 Azure 门户管理标准 Azure DDoS 防护](manage-ddos-protection.md)。

## <a name="web-application-firewall-for-resource-attacks"></a>防范资源攻击的 Web 应用程序防火墙

针对应用层中发生的资源攻击，应该配置 Web 应用程序防火墙 (WAF) 来帮助保护 Web 应用程序。 WAF 会检查入站 Web 流量，以阻止 SQL 注入、跨站点脚本、DDoS 和其他第 7 层攻击。 Azure 提供 [WAF 作为应用程序网关的一项功能](../web-application-firewall/ag/ag-overview.md)，以便在出现常见攻击和漏洞时为 Web 应用程序提供集中保护。 此外，Azure 合作伙伴还会通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)提供其他 WAF 产品/服务，它们可能更适合解决你的需求。

即使是 Web 应用程序防火墙这样的服务，也很容易遭受容量耗尽和状态耗尽攻击。 我们强烈建议在 WAF 虚拟网络上启用标准 DDoS 防护，以帮助防范容量耗尽攻击和协议攻击。 有关详细信息，请参阅 [DDoS 防护参考体系结构](ddos-protection-reference-architectures.md)部分。

## <a name="protection-planning"></a>保护规划

规划和准备对于了解系统在遇到 DDoS 攻击期间的表现至关重要。 设计事件管理响应计划属于此工作的一部分。

如果使用标准 DDoS 防护，请确保在面向 Internet 的终结点的虚拟网络上启用它。 配置 DDoS 警报有助于持续密切关注基础结构上存在的任何潜在攻击。 

独立监视应用程序。 了解应用程序的正常行为。 如果应用程序在遇到 DDoS 攻击期间的行为不符合预期，请准备好采取措施。 

通过[模拟测试](test-through-simulations.md)了解服务会如何响应攻击。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 DDoS 防护计划](manage-ddos-protection.md)。
