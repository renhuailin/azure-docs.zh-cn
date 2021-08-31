---
title: 用于出站连接的源网络地址转换 (SNAT)
titleSuffix: Azure Load Balancer
description: 了解如何将 Azure 负载均衡器用于出站 Internet 连接 (SNAT)。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 07/01/2021
ms.author: allensu
ms.openlocfilehash: 78412ba9e71465761d68899e0d48e95864f444f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732759"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>使用源网络地址转换 (SNAT) 实现出站连接

某些方案要求虚拟机或计算实例与 Internet 建立出站连接。 可使用 Azure 公共负载均衡器的前端 IP 为后端实例提供到 Internet 的出站连接。 此配置使用源网络地址转换 (SNAT)，因为源或虚拟机的专用 IP 将转换为公共 IP 地址。 SNAT 将后端的 IP 地址映射到负载均衡器的公共 IP 地址。 SNAT 可以防止外部源直接访问后端实例。  

## <a name="azures-outbound-connectivity-methods"></a><a name="scenarios"></a>Azure 的出站连接方法

可通过以下方式启用与 Internet 的出站连接：

| # | 方法 | 端口分配类型 | 生产级？ | Rating |
| ------------ | ------------ | ------ | ------------ | ------------ |
| 1 | 使用负载均衡器的前端 IP 地址通过出站规则建立出站连接 | 静态，显式 | 是，但不是大规模 | OK | 
| 2 | 将 NAT 网关关联到子网 | 静态，显式 | 是 | 最佳 | 
| 3 | 将公共 IP 分配到虚拟机 | 静态，显式 | 是 | OK | 
| 4 | 使用负载均衡器的前端 IP 地址建立出站（和入站）连接 | 隐式 | 否 | 第二差 |
| 5 | 使用默认出站访问 | 隐式 | 否 | 最差 |

## <a name="using-the-frontend-ip-address-of-a-load-balancer-for-outbound-via-outbound-rules"></a><a name="outboundrules"></a>使用负载均衡器的前端 IP 地址通过出站规则建立出站连接

使用出站规则可以为标准公共负载均衡器显式定义 SNAT（源网络地址转换）。 

通过此配置，可以使用负载均衡器的一个或多个公共 IP 建立后端实例的出站连接。

此配置可实现：

- IP 伪装
- 简化允许列表。
- 减少用于部署的公共 IP 资源的数量。

使用出站规则，你可以完全声明性地控制出站 Internet 连接。 通过出站规则，你可以根据特定需要微调和调整此功能。

有关出站规则的详细信息，请参阅[出站规则](outbound-rules.md)。

>[!Important]
> 当后端池通过 IP 地址进行配置时，它的行为就像启用了默认出站地址的基本负载均衡器。 为了在默认情况下保护有很高出站需求的配置和应用，可以用 NIC 配置后端池。

## <a name="associating-a-nat-gateway-to-the-subnet"></a>将 NAT 网关关联到子网

虚拟网络 NAT 简化了虚拟网络仅限出站的 Internet 连接。 在子网中配置后，所有出站连接将使用指定的静态公共 IP 地址。 无需使用负载均衡器或将公共 IP 地址直接附加到虚拟机，即可建立出站连接。 NAT 是完全托管式的，且具有很高的复原能力。

使用 NAT 网关是出站连接的最佳方法。 NAT 网关具有高度的可扩展性和可靠性，没有 SNAT 端口耗尽的问题。

有关 Azure 虚拟网络 NAT 的详细信息，请参阅[什么是 Azure 虚拟网络 NAT](../virtual-network/nat-gateway/nat-overview.md)。

##  <a name="assigning-a-public-ip-to-the-virtual-machine"></a>将公共 IP 分配到虚拟机

 | 关联 | 方法 | IP 协议 |
 | ---------- | ------ | ------------ |
 | VM NIC 上的公共 IP | [SNAT（源网络地址转换）](#snat) </br> 未使用。 | TCP（传输控制协议） </br> UDP（用户数据报协议） </br> ICMP（Internet 控制消息协议） </br> ESP（封装安全有效负载） |

 流量将从虚拟机的公共 IP 地址（实例级别 IP）返回请求客户端。
 
 Azure 将分配给实例 NIC 的 IP 配置的公共 IP 用于所有出站流。 此实例具有所有可用的临时端口。 VM 是否负载均衡无关紧要。 此方案优先于其他方案。 

 分配到 VM 的公共 IP 属于 1 对 1 关系（而不是 1 对多关系），并实现为无状态的 1 对 1 NAT。

## <a name="using-the-frontend-ip-address-of-a-load-balancer-for-outbound-and-inbound"></a><a name="snat"></a>使用负载均衡器的前端 IP 地址建立出站（和入站）连接
>[!NOTE]
> 不建议对生产工作负载使用此方法，因为此方法会增加耗尽端口的风险。 请不要对生产工作负载使用此方法，以避免因 SNAT 端口耗尽而导致潜在的连接故障。 

不包含以下对象的负载均衡器后端的资源：

* 出站规则
* 实例层级公共 IP 地址
* 已配置的 NAT 网关

通过负载均衡器的前端 IP 创建出站连接，并利用默认 SNAT（也称为默认出站访问）。

## <a name="default-outbound-access"></a>默认出站访问

不包含以下对象的资源：

* 出站规则
* 实例层级公共 IP 地址
* 已配置的 NAT 网关
* 负载均衡器

通过默认 SNAT 创建出站连接。 这称为默认出站访问。 另一个使用默认 SNAT 的场景示例是 Azure 中的虚拟机（没有上述关联）。 在这种情况下，出站连接是由默认出站访问 IP 提供的。 这是 Azure 分配的动态 IP，你无法对其进行控制。 对于生产工作负载，不建议使用默认 SNAT

### <a name="what-are-snat-ports"></a>什么是 SNAT 端口？

端口用于生成用于维护不同流的唯一标识符。 Internet 使用五元组来提供这种区别。

如果一个端口用于入站连接，它将有一个用于该端口上入站连接请求的侦听器。 此端口不能用于出站连接。 若要建立出站连接，使用临时端口为目标提供一个端口，在该端口上进行通信并维护不同的通信流。 当这些临时端口用于执行 SNAT 时，它们称为 SNAT 端口 

根据定义，每个 IP 地址具有 65,535 个端口。 每个端口都可以用于 TCP（传输控制协议）和 UDP（用户数据报协议）的入站或出站连接。 将公共 IP 地址作为前端 IP 添加到负载均衡器时，有 64,000 个端口可用作 SNAT。

用于负载均衡或入站 NAT 规则的端口使用 64,000 个端口中的 8 个端口。 这种用法可以减少可用作 SNAT 的端口数。 如果负载均衡或入站 NAT 规则与其他规则使用相同的八个端口，则它不会使用额外的端口。 

### <a name="how-does-default-snat-work"></a>默认 SNAT 的工作原理是什么？

当 VM 创建出站流时，Azure 将源 IP 地址转换为临时 IP 地址。 此转换通过 [SNAT](#snat) 完成。 

如果通过负载均衡规则使用 SNAT，则会预分配 SNAT 端口，如[默认 SNAT 端口分配表](#snatporttable)中所述。
 
使用标准内部负载均衡器时，不会将临时 IP 地址用于 SNAT。 默认情况下，此功能支持安全性。 此功能可确保资源使用的所有 IP 地址都可配置并可保留。 

若要在使用标准内部负载均衡器时实现与 Internet 的出站连接，请配置：

- 实例层级公共 IP 地址 
- NAT 网关
- 将后端实例添加到配置了出站规则的标准公共负载均衡器。  

### <a name="what-is-the-ip-for-default-snat"></a>默认 SNAT 的 IP 是什么？
当 VM 创建出站流时，Azure 将源 IP 地址转换为动态给定的公共源 IP 地址。 此公共 IP 地址不可配置且无法保留。 针对订阅的公共 IP 资源限制进行计数时，不会计入此地址。 

如果你重新部署以下项，系统会释放此公共 IP 地址并请求新的公共 IP： 

 * 虚拟机
 * 可用性集
 * 虚拟机规模集 

>[!NOTE]
> 不建议对生产工作负载使用此方法，因为此方法会增加耗尽端口的风险。 请不要对生产工作负载使用此方法，以避免潜在的连接故障。 

| 类型 | 出站行为 | 
| ------------ | ------ | 
| 标准公共负载均衡器 | 将负载均衡器前端 IP 用于 SNAT |
| 标准内部负载均衡器 | 不连接到 Internet，默认是安全的 | 
| 基本公共负载均衡器 | 将负载均衡器前端 IP 用于 SNAT |
| 基本内部负载均衡器 | 具有未知动态 IP 地址的 SNAT |

## <a name="exhausting-ports"></a> 大量消耗端口

每个连接到同一目标 IP 和目标端口的连接都将使用 SNAT 端口。 此连接维护从后端实例或从客户端到服务器的不同流量  。 这个过程为服务器提供了一个不同的端口来处理流量。 如果没有此过程，客户端计算机将无法知道数据包属于哪个流。

假设有多个浏览器将访问 https://www.microsoft.com ，即：

* 目标 IP = 23.53.254.142
* 目标端口 = 443
* 协议 = TCP

如果返回流量的目标端口（用于建立连接的 SNAT 端口）都相同，客户端将无法将一个查询结果与另一个查询结果分开。

出站连接可能会突发。 后端实例可能无法分配到足够的端口。 如果未启用连接重用，则会增加 SNAT 端口耗尽的风险 。

当端口耗尽时，与目标 IP 的新出站连接将失败。 当端口变为可用时，连接将成功。 当来自 IP 地址的 64,000 个端口在许多后端实例上分散分布时，就会发生这种耗尽。 有关缓解 SNAT 端口耗尽的指导，请参阅[故障排除指南](./troubleshoot-outbound-connection.md)。  

对于 TCP 连接，负载均衡器将为每个目标 IP 和端口使用一个 SNAT 端口。 这种多用途允许使用相同 SNAT 端口建立与相同目标 IP 的多个连接。 如果连接不是指向不同的目标端口，那么这种多用途是有限的。

对于 UDP 连接，负载均衡器使用端口受限的 cone NAT 算法，无论目标端口是什么，每个目标 IP 都会消耗一个 SNAT 端口。 

可以在无限数量的连接中重复使用端口。 仅当目标 IP 或端口不同时，才可重复使用端口。

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> 默认端口分配

作为负载均衡器的前端 IP 分配的每个公共 IP 都会为其后端池成员分配 64,000 个 SNAT 端口。 这些端口无法与后端池成员共享。 一系列的 SNAT 端口只能由单个后端实例使用，这样才可确保正确路由返回包。 

如果选择通过负载均衡规则自动分配出站 SNAT，分配表将定义每个 IP 的端口分配。

下表<a name="snatporttable"></a>显示了针对后端池大小的层级的 SNAT 端口预分配情况：

| 池大小（VM 实例） | 每个 IP 配置的默认 SNAT 端口 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

## <a name="constraints"></a>约束

*   如果连接处于闲置状态且没有发送新的数据包，则将在 4 - 120 分钟后释放端口。
  * 可以通过出站规则配置此阈值。
*   每个 IP 地址提供 64,000 个端口，这些端口可用于 SNAT。
*   每个端口都可以用于到目标 IP 地址的 TCP 和 UDP 连接
  * 无论目标端口是否唯一，都需要 UDP SNAT 端口。 对于每个到目标 IP 的 UDP 连接，将使用一个 UDP SNAT 端口。
  * 如果目标端口不同，则可以将一个 TCP SNAT 端口用于到同一目标 IP 的多个连接。
*   当后端实例用完给定的 SNAT 端口时，会发生 SNAT 耗尽。 负载均衡器仍然可以有未使用的 SNAT 端口。 如果后端实例的已用 SNAT 端口超过其给定的 SNAT 端口，它将无法建立新的出站连接。
*   除非出站连接通过 VM 的 NIC 上的实例级公共 IP，否则碎片化的数据包将被丢弃。

## <a name="next-steps"></a>后续步骤

*   [SNAT 耗尽造成的出站连接失败故障排除](./troubleshoot-outbound-connection.md)
*   [查看 SNAT 指标](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation)并熟悉筛选、拆分和查看它们的正确方法。
