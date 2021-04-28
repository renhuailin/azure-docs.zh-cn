---
title: Azure DDoS 防护标准业务连续性 | Microsoft Docs
description: 了解在发生影响 Azure DDoS 防护标准的 Azure 服务中断事件时该怎么办。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730274"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS 防护标准 - 业务连续性

使用 Azure DDoS 防护标准中的业务连续性和灾难恢复，你的业务能够在发生中断时继续正常运转。 本文讨论可用性（区域内）和灾难恢复。

## <a name="overview"></a>概述
Azure DDoS 防护标准可保护虚拟网络中的公共 IP 地址。 可在任何新的或现有的虚拟网络上启用保护，且无需进行任何应用程序或资源更改。

虚拟网络 (VNet) 是网络在云中的逻辑表示形式。 Vnet 用作信任边界来托管资源，例如 Azure 应用程序网关、Azure 防火墙和 Azure 虚拟机。 它是在区域范围内创建的。 可以在两个不同的区域（例如美国东部和美国西部）创建具有相同地址空间的 VNet，但由于它们具有相同的地址空间，你无法将它们连接到一起。 

## <a name="business-continuity"></a>业务连续性

可通过多种不同的方式中断应用程序。 某个区域可能会因自然灾害而完全中断，或因多个设备/服务的故障而局部中断。 每种情况下对受保护 Vnet 的影响有所不同。

问：如果整个区域发生停电，我该怎么办？例如，如果由于自然灾害区域内完全停电该怎么办？该区域中托管的虚拟网络会发生什么情况？

答：在服务中断期间，受影响区域的虚拟网络和资源将无法访问。

![简单虚拟网络示意图。](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**问：我怎样才能在不同的区域中重新创建相同的虚拟网络？**

答：虚拟网络是相当轻量级的资源。 可以调用 Azure API 在不同的区域中创建具有相同地址空间的 VNet。 若要重新创建曾存在于受影响区域中的相同环境，请调用 API 来重新部署曾拥有的 VNet 中的资源。 如果有本地连接（如在混合部署中），还必须部署新的 VPN 网关并连接本地网络。

若要创建虚拟网络，请参阅[创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。

**问：能否在其他区域提前重新创建给定区域的 VNet 副本？**

答：能。可以在不同区域中提前创建两个使用相同的专用 IP 地址空间和资源的 VNet。 如果将在 VNet 中托管面向 Internet 的服务，必须设置流量管理器，将流量异地路由到可用区域。 不过，不能将两个地址空间相同的 VNet 连接到本地网络，因为这样做可能会导致路由问题发生。 当一个区域中的 VNet 发生灾难和丢失时，可以将可用区域中具有匹配地址空间的其他 VNet 连接到本地网络。

若要创建虚拟网络，请参阅[创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 DDoS 防护计划](manage-ddos-protection.md)。