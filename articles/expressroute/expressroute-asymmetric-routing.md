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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97560502"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>具有多个网络路径的非对称路由
本文介绍当网络源和网络目标之间有多个路径可用时，流量如何采用不同的路线。

要理解非对称路由，需要理解两个概念。 第一个概念是多个网络路径的影响。 另一个概念是设备（如防火墙）如何保持状态。 这种类型的设备称为有状态设备。 这两个因素的组合会造成流量被有状态设备删除。  流量被删除是因为有状态设备未检测到源自设备本身的流量。

## <a name="multiple-network-paths"></a>多个网络路径
如果企业网络的 Internet 服务提供商只提供一条链路来连接 Internet，那么进出 Internet 的所有流量都会通过同一个路径传输。 通常，公司会购买多条线路来创建冗余路径，以改善网络运行时间。 使用这种类型的配置时，流量可以通过一条链路流入 Internet，并通过不同的链路返回。 这种情形通常称为非对称路由。 在非对称路由中，流出的原始流量与返回的流量采用不同的路径。

![具有多个路径的网络](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

尽管非对称路由通常发生在流量流入 Internet 时， 但在引入多个路径组合的情况下也会发生。 例如，让一个 Internet 路径和一个专用路径通向同一个目标。 或者，让多个专用路径也通向同一目标。

源到目标的路径上的每个路由器都将计算抵达目标所需的最佳路径。 路由器根据两个主要因素确定可能的最佳路径：

* 外部网络之间的路由基于边界网关协议 (BGP) 路由协议。 BGP 接受邻居的播发并通过一连串的步骤来运行这些播发，确定抵达预定目标的最佳路径。 它在其路由表中存储最佳路径。
* 与路由关联的子网掩码的长度会影响路由路径。 如果路由器收到 IP 地址相同的多个播发，路由器会选择子网掩码更长的路径，因为路由器认为它是更具体的路线。

## <a name="stateful-devices"></a>有状态设备
为了路由，路由器将查看数据包的 IP 标头。 某些设备看起来在数据包的更深处。 这些设备通常会查看第 4 层（传输控制协议 (TCP) 或用户数据报协议 (UDP)），甚至第 7 层（应用程序层）标头。 这几种设备要么是安全设备，要么是带宽优化设备。 

防火墙是有状态设备的常见示例。 防火墙根据不同的条件允许或拒绝数据包通过其接口。 这些条件包括但不限于协议、TCP/UDP 端口和 URL 标头。 这一级别的数据包检查会大幅增加设备的处理负载。 

为了改善性能，防火墙会检查流程的第一个数据包。 如果防火墙允许数据包通过其接口，它会将流信息保存在其状态表中。 然后根据初步判定，允许后续与此网络流相关的任何数据包。 属于现有网络流的数据包可能会抵达并非其来源的防火墙。 由于它没有任何有关初始流的先前状态信息，因此防火墙会删除数据包。

## <a name="asymmetric-routing-with-expressroute"></a>非对称路由与 ExpressRoute
通过 Azure ExpressRoute 连接到 Microsoft 时，网络会发生以下变化：

* 与 Microsoft 建立多个链接。 一条链路是现有的 Internet 连接，另一条是通过 ExpressRoute 连接。 流向 Microsoft 的某些流量可能会通过 Internet 连接，但会通过 ExpressRoute 连接返回。 当流量通过 ExpressRoute 传输但通过 Internet 路径返回时，也会发生这种情况。
* 通过 ExpressRoute 线路会接收到更明确的 IP 地址。 因此，对于通过 ExpressRoute 提供的服务，将来自网络的流量传入 Microsoft 时，路由器将始终首选 ExpressRoute。

下面我们通过举例来了解这两项更改对网络产生的影响。 假设你有一条线路连接到 Internet，并通过 Internet 使用所有 Microsoft 服务。 流量通过同一条 Internet 链路，穿过防火墙往返于网络和 Microsoft 之间。 防火墙在看到第一个数据包时记录网络流。 允许该会话后续的每个数据包，因为网络流存在于状态表中。

![非对称路由与 ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

然后，打开一条 ExpressRoute 线路，以使用 Microsoft 通过 ExpressRoute 提供的服务。 Microsoft 提供的所有其他服务均通过 Internet 使用。 在连接到 ExpressRoute 连接的边缘部署单独的防火墙。 Microsoft 通过 ExpressRoute，针对特定服务向网络播发更明确的前缀。 路由基础结构选择 ExpressRoute 作为这些前缀的首选路径。 

如果不通过 ExpressRoute 将公共 IP 地址播发到 Microsoft， Microsoft 将通过 Internet 与公共 IP 地址通信。 从网络发送到 Microsoft 的流量使用 ExpressRoute 连接，但从 Microsoft 返回的流量将使用 Internet 路径。 如果边缘的防火墙发现未知的网络流响应数据包，就会删除这些流量。

如果选择为 ExpressRoute 和 Internet 播发同一网络地址转换 (NAT) 池， 则会发现网络中专用 IP 地址上的客户端有类似的问题。 服务请求（如 Windows 更新）将通过 Internet 传输，因为这些服务的 IP 地址不会通过 ExpressRoute 播发。 但是，返回流量将通过 ExpressRoute 返回。 由于 Microsoft 从 Internet 和 ExpressRoute 收到子网掩码相同的 IP 地址，因此首选路径始终为 ExpressRoute。 如果在网络边缘上面向 ExpressRoute 连接的防火墙或其他有状态设备没有任何有关网络流的先前信息，就会删除这些数据包。

## <a name="asymmetric-routing-solutions"></a>非对称路由解决方案
解决非对称路由问题的方法有两种。 第一种是通过路由，第二种是使用基于源的 NAT (SNAT)。

### <a name="routing"></a>路由
确保已向相应的广域网 (WAN) 链路播发公共 IP 地址。 例如，如果要将 Internet 用于身份验证流量，将 ExpressRoute 用于邮件流量， 请不要通过 ExpressRoute 播发 Active Directory 联合身份验证服务 (AD FS) 公共 IP 地址。 另外，请确保不要向路由器通过 ExpressRoute 收到的 IP 地址公开本地 AD FS 服务器。 通过 ExpressRoute 收到的路由更加明确，因此可让 ExpressRoute 成为 Microsoft 身份验证流量的首选路径。 如果没有注意到网络中的路由方式，则可能会出现非对称路由问题。

若要使用 ExpressRoute 来传输身份验证流量，请确保通过 ExpressRoute 播发 AD FS 公共 IP 地址，而不使用 NAT。 如果以这种方式进行配置，源自 Microsoft 的流量将通过 ExpressRoute 流向本地 AD FS 服务器。 源自网络的流量使用 ExpressRoute 返回 Microsoft，因为相较于 Internet 而言，ExpressRoute 是首选路径。

### <a name="source-based-nat"></a>基于源的 NAT
解决非对称路由问题的另一种方法是使用 SNAT。 例如，你选择不通过 ExpressRoute 播发本地简单邮件传输协议 (SMTP) 服务器的公共 IP 地址。 而是打算使用 Internet 进行此类通信。 源自 Microsoft 的请求通过 Internet 流入本地 SMTP 服务器。 通过 SNAT 将传入请求传递到内部 IP 地址。 来自 SMTP 服务器的返回流量将抵达边缘防火墙（用于 NAT），而不通过 ExpressRoute。 因此，返回流量将采用 Internet 路径。

![基于源的 NAT 网络配置](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>非对称路由检测
跟踪路由是确保网络流量遍历预期路径的最佳方式。 如果希望流量通过 Internet 路径从本地 SMTP 服务器传输到 Microsoft，预期的路由跟踪应是从 SMTP 服务器到 Microsoft 365。 结果证明流量确实从网络流向 Internet，而非 ExpressRoute。

