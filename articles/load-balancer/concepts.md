---
title: Azure 负载均衡器的概念
description: 概述了 Azure 负载均衡器的概念
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 946741c8aa70040dd0186deceab0fb090cf38c11
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271738"
---
# <a name="azure-load-balancer-algorithm"></a>Azure 负载均衡器算法

负载均衡器针对 UDP 和 TCP 应用程序提供了多种功能。

## <a name="load-balancing-algorithm"></a>负载均衡算法

通过创建负载均衡器规则，可以将入站流量从负载均衡器的前端分配到其后端池。 Azure 负载均衡器使用五元组哈希算法来分配入站流（非字节）。  在将流量定向到后端池实例时，负载均衡器会重写 TCP/UDP 流的头（负载均衡器不会重写 HTTP/HTTPS 头）。 当负载均衡器的运行状况探测指示正常的后端终结点时，可以使用后端实例来接收新的流量流。

默认情况下，负载均衡器使用五元组哈希。

哈希包括：

- **源 IP 地址**
- **源端口**
- **目标 IP 地址**
- **目标端口**
- **用于将流映射到可用服务器的 IP 协议号**

## <a name="session-persistence"></a>会话暂留

同一个流的数据包将到达同一个后端池实例。 但是，当客户端从同一源 IP 启动新流时，源端口将会更改。 因此，五元组哈希可能会导致流量定向到不同的后端终结点。 

源 IP 地址的会话持久性是使用二元组或三元组哈希创建的。 启用会话持久性后，来自同一客户端 IP 地址的后续请求将由同一虚拟机处理。 有关 Azure 负载均衡器分配模式的详细信息，请参阅[配置 Azure 负载均衡器的分配模式](./load-balancer-distribution-mode.md)。

下图显示了基于哈希的分配：

![基于哈希的分发](./media/load-balancer-overview/load-balancer-distribution.png)

*图：基于哈希的分发*

## <a name="application-independence-and-transparency"></a>应用程序独立性和透明度

负载均衡器支持任何 TCP/UDP 应用程序方案，并且不会关闭或发起流。 负载均衡器也不会与任何流的有效负载交互。 

- 应用程序有效负载对于负载均衡器是透明的。 可以支持任何 UDP 或 TCP 应用程序。

负载均衡器在第 4 层上运行，不提供应用程序层网关功能。 协议握手始终直接在客户端与后端池实例之间进行。 

- 由于负载均衡器不会与 TCP 有效负载交互，也不提供 TLS 卸载，因此你可以构建全面的加密方案。 使用负载均衡器可通过在 VM 自身上终止 TLS 连接来实现 TLS 应用程序的大规模横向扩展。 例如，将会根据添加到后端池的 VM 类型和数目限制 TLS 会话密钥容量。

对入站流做出的响应始终是来自虚拟机的响应。 当流抵达虚拟机时，也会保留原始的源 IP 地址。

- 每个终结点由某个 VM 应答。 例如，TCP 握手在客户端与选定的后端 VM 之间发生。 对前端请求做出的响应是后端 VM 生成的响应。 成功验证与前端的连接后，将会验证与至少一个后端虚拟机的整个连接。


## <a name="next-steps"></a>后续步骤

- 了解组成 Azure 负载均衡器的[组件](components.md)。
- 请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)以开始使用负载均衡器：在已安装自定义 IIS 扩展的情况下创建 VM，然后对 VM 之间的 Web 应用进行负载均衡。
- 了解 [Azure 负载均衡器出站连接](load-balancer-outbound-connections.md)。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
- 了解[运行状况探测](load-balancer-custom-probe-overview.md)。
- 了解有关[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)的信息。
- 详细了解[网络安全组](../virtual-network/network-security-groups-overview.md)。
