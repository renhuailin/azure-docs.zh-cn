---
title: Azure DDoS 防护的基本最佳做法
description: 了解使用 DDoS 防护的最佳安全做法。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 66c1ab1cb5ed478aa34825fb6903e4d06f834097
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94989467"
---
# <a name="fundamental-best-practices"></a>基本最佳做法

以下部分提供了有关在 Azure 中构建弹性应对 DDoS 的服务的规范性指导。

## <a name="design-for-security"></a>安全设计

确保优先考虑从设计和实施到部署和操作的整个应用程序生命周期的安全性。 应用程序可能包含 bug，使相对较少的请求使用过多的资源，导致服务中断。 

为帮助保护 Microsoft Azure 上运行的服务，应该对应用程序体系结构有充分的了解，并重点关注[软件质量的五大要素](/azure/architecture/guide/pillars)。
应该清楚典型的流量大小、应用程序与其他应用程序之间的连接模型，以及向公共 Internet 公开的服务终结点。

至关重要的一点是，确保应用程序具有足够的弹性，可应对针对应用程序本身的拒绝服务攻击。 从[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/default.aspx) 开始，安全和隐私就已内置到 Azure 平台中。 SDL 可以解决每个开发阶段的安全性，并确保 Azure 不断更新，以变得越来越安全。

## <a name="design-for-scalability"></a>可伸缩性设计

可伸缩性是指系统处理增加的负载的能力。 采用可[横向缩放](/azure/architecture/guide/design-principles/scale-out)的应用程序设计，以满足放大负载的需求，尤其是防范 DDoS 攻击。 如果应用程序依赖于服务的单个实例，则会造成单一故障点。 预配多个实例能够提高复原能力和可伸缩性。

对于 [Azure 应用服务](../app-service/overview.md)，请选择提供多个实例的[应用服务计划](../app-service/overview-hosting-plans.md)。 对于 Azure 云服务，请将每个角色配置为使用[多个实例](../cloud-services/cloud-services-choose-me.md)。 对于 [Azure 虚拟机](../virtual-machines/index.yml)，请确保虚拟机 (VM) 体系结构包含多个 VM，并且每个 VM 包含在[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)中。 我们建议使用[虚拟机规模集](../virtual-machine-scale-sets/overview.md)来实现自动缩放功能。

## <a name="defense-in-depth"></a>深层防御

深层防御的理念是通过多样化的防御策略来掌控风险。 应用程序中的分层安全防御可以减少攻击成功的可能性。 我们建议使用 Azure 平台的内置功能对其应用程序实施安全设计。

例如，攻击风险会随着应用程序的规模（外围应用）的增大而增大。 可以使用审批列表关闭负载均衡器（[Azure 负载均衡器](../load-balancer/quickstart-load-balancer-standard-public-portal.md)和 [Azure 应用程序网关](../application-gateway/application-gateway-create-probe-portal.md)）上不需要的公开 IP 地址空间和侦听端口，从而减少外围应用。 [网络安全组 (NSG)](../virtual-network/network-security-groups-overview.md) 是缩小受攻击面的另一种方法。
可以使用[服务标记](../virtual-network/network-security-groups-overview.md#service-tags)和[应用程序安全组](../virtual-network/network-security-groups-overview.md#application-security-groups)来最大程度地简化安全规则的创建，并将网络安全性配置为应用程序结构的自然扩展。

应尽可能地在[虚拟网络](../virtual-network/virtual-networks-overview.md)中部署 Azure 服务。 这种做法可让服务资源通过专用 IP 地址通信。 来自虚拟网络的 Azure 服务流量默认使用公共 IP 地址作为源 IP 地址。 使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)时，服务流量会在通过虚拟网络访问 Azure 服务时改用虚拟网络专用地址作为源 IP 地址。

我们经常看到，客户本地资源会连同其在 Azure 中资源的一起受到攻击。 如果将本地环境连接到 Azure，我们建议尽量不要在公共 Internet 上公开本地资源。 可以通过在 Azure 中部署已知的公共实体，来利用 Azure 的规模和高级 DDoS 防护功能。 由于这些可公开访问的实体通常会成为 DDoS 攻击的目标，将它们放在 Azure 中可以降低对本地资源的影响。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 DDoS 防护计划](manage-ddos-protection.md)。