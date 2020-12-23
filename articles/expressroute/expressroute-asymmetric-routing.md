---
title: Azure ExpressRoute：非对称路由
description: 本文详细介绍在与一个目标建立有多个链接的网络中使用非对称路由时可能会遇到的问题。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560502"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>非对称路由与多个网络路径
本文说明当网络源和目标之间有多个路由可用时，网络流量可能采用不同的路径。

了解非对称路由需要了解两个概念。 第一种是多个网络路径的影响。 另一种方法是设备（如防火墙）如何保持状态。 这种类型的设备称为有状态设备。 当这两个因素结合使用时，它们可以创建一个方案，在此方案中，有状态设备丢弃网络流量。  流量被丢弃，因为它未检测到来自其自身的流量。

## <a name="multiple-network-paths"></a>多个网络路径
当企业网络通过 internet 服务提供商仅有一个到 internet 的链接时，流向 internet 的所有流量都将传播到同一路径。 通常，公司会购买多个线路来创建冗余路径，以改善网络运行时间。 使用这种类型的配置，可以使流量进入 internet，并通过不同的链接返回。 此方案通常称为非对称路由。 在非对称路由中，返回的网络流量从初始流中提取不同的路径。

![具有多个路径的网络](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

尽管在转到 internet 时通常会发生非对称路由。 当引入多个路径的组合时，也会发生这种情况。 第一个示例是具有指向同一目标的 internet 路径和专用路径。 第二个示例是，有多个专用路径也会转到相同的目标。

源和目标之间路径上的每个路由器都将计算到达目标所需的最佳路径。 路由器根据两个主要因素确定可能的最佳路径：

* 外部网络之间的路由基于边界网关协议 (BGP) 路由协议。 BGP 接受邻居的播发并通过一连串的步骤来运行这些播发，确定抵达预定目标的最佳路径。 它在其路由表中存储最佳路径。
* 与路由关联的子网掩码的长度会影响路由路径。 如果路由器接收到相同 IP 地址的多个播发，路由器将使用较长的子网掩码选择路径，因为它被视为更具体的路由。

## <a name="stateful-devices"></a>有状态设备
为了路由，路由器将查看数据包的 IP 标头。 某些设备看起来在数据包的更深处。 通常，这些设备查看第4层-传输控制协议 (TCP) 或用户数据报协议 (UDP) ，甚至第7层 (应用程序层) 标头。 这几种设备要么是安全设备，要么是带宽优化设备。 

防火墙是有状态设备的常见示例。 防火墙允许或拒绝数据包通过其接口，具体取决于各种条件。 这些条件包括但不限于协议、TCP/UDP 端口和 URL 标头。 此级别的数据包检查可以在设备上施加大量处理负载。 

为了改善性能，防火墙会检查流程的第一个数据包。 如果允许数据包通过其接口，它会将流信息保留在其状态表中。 然后，将根据初始确定允许所有与此流相关的后续数据包。 作为现有流一部分的数据包可能会到达其不是源自的防火墙。 由于它没有与初始流有关的以前的状态信息，因此防火墙会丢弃数据包。

## <a name="asymmetric-routing-with-expressroute"></a>非对称路由与 ExpressRoute
通过 Azure ExpressRoute 连接到 Microsoft 时，网络会发生以下变化：

* 与 Microsoft 建立多个链接。 一个链接是现有的 Internet 连接，另一个是通过 ExpressRoute 连接。 发往 Microsoft 的某些流量可能会经历 internet 连接，但会返回到 ExpressRoute 连接。 当流量超过 ExpressRoute 但通过 internet 路径返回时，也会发生这种情况。
* 从 ExpressRoute 线路接收了更具体的 IP 地址。 因此，当来自网络的流量通过 ExpressRoute 向 Microsoft 发送服务时，路由器将始终优先使用 ExpressRoute 连接。

若要了解这两种更改对网络的影响，请考虑一些方案。 例如，你有一个到 internet 的线路，你通过 internet 使用所有 Microsoft 服务。 从你的网络发往和发往 Microsoft 的流量将遍历同一 internet 链接，并通过防火墙传递。 防火墙在看到第一个数据包时记录流。 允许该会话的每个后续数据包，因为该流存在于状态表中。

![非对称路由与 ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

然后，你将打开一个 ExpressRoute 线路，以使用 Microsoft 通过 ExpressRoute 提供的服务。 Microsoft 的所有其他服务都通过 internet 使用。 在连接到 ExpressRoute 连接的边缘部署单独的防火墙。 Microsoft 通过 ExpressRoute 为某些服务向网络广告更具体的前缀。 路由基础结构选择 ExpressRoute 作为这些前缀的首选路径。 

如果不通过 ExpressRoute 向 Microsoft 公布公共 IP 地址。 Microsoft 将通过 internet 与你的公共 IP 地址进行通信。 从网络发送到 Microsoft 的流量使用 ExpressRoute 连接，但从 Microsoft 返回的流量将使用 internet 路径。 当边缘的防火墙看到它不知道的流的响应数据包时，它将丢弃这些数据包。

如果选择 (NAT) 池为 ExpressRoute 和 internet 播发相同的网络地址转换。 专用 IP 地址上的网络中的客户端会出现类似的问题。 Windows 更新的服务请求将通过 internet，因为这些服务的 IP 地址不通过 ExpressRoute 播发。 但是，返回流量将通过 ExpressRoute 返回。 由于 Microsoft 从 internet 和 ExpressRoute 收到具有相同子网掩码的 IP 地址，因此首选路径始终为 ExpressRoute。 如果网络边缘上的防火墙或其他有状态设备面向 ExpressRoute 连接，则会丢弃这些数据包。

## <a name="asymmetric-routing-solutions"></a>非对称路由解决方案
有两个可用的选项可以解决非对称路由问题。 第一种方法是通过路由，第二种方法是使用基于源的 NAT (SNAT) 。

### <a name="routing"></a>路由
请确保将公共 IP 地址播发到适当的广域网 (WAN) 链接。 例如，如果想要将 internet 用于身份验证流量，并将 ExpressRoute 用于邮件流量。 不要通过 ExpressRoute 播发 Active Directory 联合身份验证服务 (AD FS) 公共 IP 地址。 同时，请确保不要向路由器通过 ExpressRoute 收到的 IP 地址公开本地 AD FS 服务器。 通过 ExpressRoute 收到的路由更加明确，因此可让 ExpressRoute 成为 Microsoft 身份验证流量的首选路径。 如果你没有注意到如何在网络中进行路由，则可能会出现不对称路由问题。

如果要使用 ExpressRoute 进行身份验证，请确保在没有 NAT 的情况下通过 ExpressRoute AD FS 的公共 IP 地址进行播发。 如果以这种方式进行配置，来自 Microsoft 的流量将转到本地 AD FS 服务器将通过 ExpressRoute。 发往 Microsoft 的网络返回流量将使用 ExpressRoute，因为这是通过 internet 的首选路由。

### <a name="source-based-nat"></a>基于源的 NAT
解决非对称路由问题的另一种方法是使用 SNAT。 例如，选择不通过 ExpressRoute 播发本地简单邮件传输协议的公共 IP 地址 (SMTP) 服务器。 相反，你打算将 internet 用于这种类型的通信。 发往 Microsoft 的请求将通过 internet 遍历 internet。 通过 SNAT 将传入请求传递到内部 IP 地址。 从 SMTP 服务器返回的流量将转到边缘防火墙 (用于 NAT) ，而不是通过 ExpressRoute。 因此，返回流量将采用 internet 路径。

![基于源的 NAT 网络配置](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>非对称路由检测
跟踪路由是确保网络流量遍历预期路径的最佳方式。 如果希望从本地 SMTP 服务器到 Microsoft 的流量获得 internet 路径，则预期的 traceroute 是从 SMTP 服务器到 Microsoft 365。 结果会验证流量是否确实会将网络留给 internet 而不是 ExpressRoute。

