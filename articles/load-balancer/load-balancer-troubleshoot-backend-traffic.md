---
title: 排查 Azure 负载均衡器问题
description: 了解如何排查 Azure 负载均衡器的已知问题。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029167"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>排查 Azure 负载均衡器后端流量响应问题

此页介绍有关 Azure 负载均衡器问题的疑难解答信息。

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>负载均衡器后端的 VM 不响应已配置数据端口上的通信

如果后端池 VM 被列为正常且响应运行状况探测，但仍未参与负载均衡，或未响应数据流量，可能是由于以下某项原因：
* 负载均衡器后端池 VM 未侦听数据端口
* 网络安全组要阻止负载均衡器后端池 VM 上的端口  
* 从相同的 VM 和 NIC 访问负载均衡器
* 从参与的负载均衡器后端池 VM 访问 Internet 负载均衡器前端

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>原因 1：负载均衡器后端池 VM 未侦听数据端口

如果 VM 未响应数据流量，这可能是因为参与的 VM 上的目标端口未打开，或者 VM 未侦听此端口。 

验证及解决方法

1. 登录到后端 VM。 
2. 打开命令提示符并运行下列命令，以验证是否有应用程序在侦听数据端口：  
            netstat -an 
3. 如果端口状态未被列为“正在侦听”，请配置适当的侦听端口 
4. 如果端口被标记为“正在侦听”，请检查该端口的目标应用程序是否存在问题。

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2：网络安全组要阻止负载均衡器后端池 VM 上的端口  

如果子网或 VM 上配置的一个或多个网络安全组要阻止源 IP 或端口，则此 VM 无法响应。

对于公共负载均衡器，将使用 Internet 客户端的 IP 地址在客户端和负载均衡器后端 VM 之间通信。 请确保后端 VM 的网络安全组允许使用客户端的 IP 地址。

1. 列出后端 VM 上配置的网络安全组。 有关详细信息，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)
1. 在网络安全组列表中，检查：
    - 数据端口上的传入或传出流量是否受到干扰。 
    - 检查 VM NIC 或子网上是否存在优先级高于允许负载均衡探测和流量的默认规则的“全部拒绝”网络安全组规则（网络安全组必须允许负载均衡器 IP 168.63.129.16 - 即探测端口）
1. 如果某规则要阻止流量，请将其删除并将规则重新配置为允许数据流量。  
1. 测试 VM 是否现已开始响应运行状况探测。

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3：从相同的 VM 和网络接口访问负载均衡器 

如果负载均衡器后端 VM 上托管的应用程序正尝试通过同一网络接口访问同一后端 VM 上托管的其他应用程序，该操作不受支持且会失败。 

解决方法 - 可通以下方法解决该问题：
* 为每个应用程序配置单独的后端池 VM。 
* 在双 NIC VM 中配置应用程序，以便每个应用程序均使用自己的网络接口和 IP 地址。 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4：从参与的负载均衡器后端池 VM 访问 Internet 负载均衡器前端

如果在 VNet 中配置了内部负载均衡器，并且某个参与的后端 VM 正在尝试访问内部负载均衡器前端，则当将流映射到原始 VM 时会发生故障。 不支持这种情况。

解决方案：有几种方法来取消阻止此方案，包括使用代理。 评估应用程序网关或其他第三方代理服务器（例如 nginx 或 haproxy）。 有关应用程序网关的详细信息，请参阅[应用程序网关概述](../application-gateway/overview.md)

**详细信息** 内部负载均衡器不会将出站发起连接转换为内部负载均衡器的前端，因为两者都位于专用 IP 地址空间中。 公共负载均衡器提供从虚拟网络内部专用 IP 地址到公共 IP 地址的[出站连接](load-balancer-outbound-connections.md)。 对于内部负载均衡器，此方法可以避免不需要转换的唯一内部 IP 地址空间内发生 SNAT 端口耗尽。

负面影响是，如果来自后端池中 VM 的出站流尝试流向该 VM 所在池中内部负载均衡器的前端，并映射回到自身，则这两个流的分支不会匹配。 由于它们不匹配，因此流会失败。 如果流未映射回到后端池中的同一 VM（在前端中创建了流的 VM），则该流将会成功。

如果流映射回到自身，则出站流显示为源自 VM 并发往前端，并且相应的入站流显示为源自 VM 并发往自身。 从来宾操作系统的角度看，同一流的入站和出站部分在虚拟机内部不匹配。 TCP 堆栈不会将同一流的这两半看作是同一流的组成部分。 源和目标不匹配。 当流映射到后端池中的任何其他 VM 时，流的两半将会匹配，且 VM 可以响应流。

此方案的缺点在于，当流返回到发起该流的同一后端时将出现间歇性的连接超时。 常见的解决方法包括：在内部负载均衡器后插入代理层并使用直接服务器返回 (DSR) 样式规则。 有关详细信息，请参阅 [Azure 负载均衡器的多个前端](load-balancer-multivip-overview.md)。

可以将内部负载均衡器与任何第三方代理相结合，或使用内部[应用程序网关](../application-gateway/overview.md)替代 HTTP/HTTPS 的代理方案。 尽管可以使用公共负载均衡器来缓解此问题，但最终的方案很容易导致 [SNAT 耗尽](load-balancer-outbound-connections.md)。 除非有精心的管理，否则应避免此第二种方法。

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。