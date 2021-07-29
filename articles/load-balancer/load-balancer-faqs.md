---
title: 常见问题解答 - Azure 负载均衡器
description: 有关 Azure 负载均衡器的常见问题解答。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: dea87239d3e7e9de04d7ebded1c8431445dad8b0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110068889"
---
# <a name="load-balancer-frequently-asked-questions"></a>负载均衡器常见问题解答

## <a name="what-types-of-load-balancer-exist"></a>存在哪些类型的负载均衡器？
内部负载均衡器（用于均衡 VNET 中的流量）和外部负载均衡器（用于均衡进出 Internet 连接的终结点的流量）。 有关详细信息，请参阅[负载均衡器类型](components.md#frontend-ip-configurations)。 

对于这两种类型，Azure 提供了基本 SKU 和标准 SKU，它们具有不同的功能、性能、安全性和运行状况跟踪功能。 [SKU 比较](skus.md)一文中介绍了这些差异。

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>如何从基本负载均衡器升级到标准负载均衡器？
有关自动化脚本和升级负载均衡器 SKU 的指南，请参阅[从“基本”升级到“标准”](upgrade-basic-standard.md)一文。

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure 中有哪些不同的负载均衡选项？
请参阅[负载均衡器技术指南](/azure/architecture/guide/technology-choices/load-balancing-overview)，了解可用的负载均衡服务以及每个服务的建议用途。

## <a name="where-can-i-find-load-balancer-arm-templates"></a>在哪里可以找到负载均衡器 ARM 模板？
有关常见部署的 ARM 模板，请参阅 [Azure 负载均衡器快速入门模板列表](/azure/templates/microsoft.network/loadbalancers#quickstart-templates)。

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>入站 NAT 规则与负载均衡规则有何不同？
NAT 规则用于指定要将流量路由到其中的后端资源。 例如，将特定负载均衡器端口配置为将 RDP 流量发送到特定 VM。 负载均衡规则用于指定要将流量路由到其中的后端资源池，从而在每个实例之间均衡负载。 例如，负载均衡器规则可以跨 Web 服务器池在负载均衡器的端口 80 上路由 TCP 数据包。

## <a name="what-is-ip-1686312916"></a>IP 168.63.129.16 是什么？
标记为 Azure 基础结构负载均衡器的主机的虚拟 IP 地址，从其进行 Azure 运行状况探测。 配置后端实例时，这些实例必须允许来自此 IP 地址的流量才能成功响应运行状况探测。 此规则不与负载均衡器前端的访问交互。 如果不使用 Azure 负载均衡器，则可重写此规则。 可在[此处](../virtual-network/service-tags-overview.md#available-service-tags)详细了解服务标记。

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>是否可以将全局 VNet 对等互连用于基本负载均衡器？
否。 基本负载均衡器不支持全局 VNET 对等互连。 可改用标准负载均衡器。 有关无缝升级，请参阅[从“基本”升级到“标准”](upgrade-basic-standard.md)一文。

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>如何发现 Azure VM 使用的公共 IP？

有多种方法来确定出站连接的公共源 IP 地址。 OpenDNS 提供了一种服务可以向你显示 VM 的公共 IP 地址。
使用 nslookup 命令，可以将名称 myip.opendns.com 的 DNS 查询发送到 OpenDNS 解析程序。 该服务返回用于发送此查询的源 IP 地址。 在 VM 中运行以下查询时，返回的是用于该 VM 的公共 IP。

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>是否可以将同一可用性集中的 VM 添加到负载均衡器的不同后端池？
不，这不可能。

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>可以通过 Azure 负载均衡器实现的最大数据吞吐量是多少？
由于 Azure LB 是直通网络负载均衡器，因此吞吐量限制由后端池中使用的虚拟机类型决定。 若要了解其他与网络吞吐量相关的信息，请参阅[虚拟机网络吞吐量](../virtual-network/virtual-machine-network-throughput.md)。

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>如何连接到同一区域中的 Azure 存储？
通过上述方案进行出站连接时不一定要连接到与 VM 位于同一区域的存储。 如果不想这样做，请按上述说明使用网络安全组 (NSG)。 若要连接到其他区域的存储，则需要使用出站连接。 请注意，当从同一区域中的虚拟机连接到存储时，存储诊断日志中的源 IP 地址将是内部提供程序地址，而不是虚拟机的公共 IP 地址。 如果要将对存储帐户的访问限制至同一区域中一个或多个虚拟网络子网中的 VM，请在配置存储帐户防火墙时使用[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)，而不是公共 IP 地址。 配置了服务终结点后，将在存储诊断日志中看到虚拟网络专用 IP 地址，而不是内部提供程序地址。

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>Azure 负载均衡器是否支持 TLS/SSL 终端？
不支持，Azure 负载均衡器目前不支持终端，因为它是直通网络负载均衡器。 如果你的应用程序需要解决方案，则应用程序网关可能是一个潜在解决方案。

## <a name="what-are-best-practices-with-respect-to-outbound-connectivity"></a>与出站连接有关的最佳做法是什么？
标准负载均衡器和标准公共 IP 为出站连接引入了功能和不同的行为。 它们不同于基本 SKU。 如果在使用标准 SKU 时需要出站连接，则必须使用标准公共 IP 地址或标准公共负载均衡器显式定义它。 这包括在使用内部标准负载均衡器时创建出站连接。 建议始终使用标准公共负载均衡器上的出站规则。 这意味着使用内部标准负载均衡器时，如果需要出站连接，则需要采取步骤为后端池中的 VM 创建出站连接。 在出站连接的上下文中，单个独立 VM、可用性集中的所有 VM、虚拟机规模集中的所有实例都表现得像一个组。 这意味着，如果可用性集中的单个 VM 与标准 SKU 关联，则该可用性集中的所有 VM 实例现在都遵循相同的规则，就好像这些 VM 实例与标准 SKU 相关联一样，即使单个实例与标准 SKU 没有直接关联。 如果独立 VM 有连接到负载均衡器的多个网络接口卡，也会出现此行为。 如果将一个 NIC 添加为独立 NIC，也会有相同的行为。 请仔细查看整个文档以了解整体概念，查看[标准负载均衡器](./load-balancer-overview.md)了解 SKU 之间的差异，并查看[出站规则](load-balancer-outbound-connections.md#outboundrules)。
使用出站规则可以对出站连接的所有方面进行细化管理控制。
 
 ## <a name="how-can-i-view-the-traffic-from-my-configured-health-probes"></a><a name="probes"></a> 如何查看来自我配置的运行状况探测的流量？
若要查看从运行状况探测发送到每个后端实例的流量，可以通过 netstat 之类的工具使用 IP 堆栈统计信息。 通过此工具查看时，运行状况探测流量将来自 168.63.129.16。
 
## <a name="next-steps"></a>后续步骤
如果上面未列出你的问题，请将有关本页的反馈与你的问题一起发送。 这会为产品团队创建 GitHub 问题，以确保所有有价值的客户问题都得到回答。
