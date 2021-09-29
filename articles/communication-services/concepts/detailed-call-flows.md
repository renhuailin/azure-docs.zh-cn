---
title: Azure 通信服务中的呼叫流拓扑
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务中的呼叫流拓扑。
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 064586d0fadac917359a7de70145a10ab6e2cb45
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672032"
---
# <a name="call-flow-topologies"></a>调用流拓扑
本文介绍 Azure 通信服务呼叫流拓扑。 如果你是一名企业客户，且你将通信服务集成到你管理的某个网络中，那么请查看本文。 若要简要了解通信服务呼叫流，请访问[呼叫流概念文档](./call-flows.md)。

## <a name="background"></a>背景

### <a name="network-concepts"></a>网络概念

查看呼叫流拓扑之前，请查看我们定义的在整个文章中会用到的一些术语。

客户网络包含你管理的任何网段。 这可能包括你的办公室，或者办公室、数据中心和 Internet 服务提供商之间的有线网络和无线网络。

客户网络通常具有多个网络边界，它们有防火墙和/或代理服务器来强制实施你组织的安全策略。 建议进行[完全网络评估](/microsoftteams/3-envision-evaluate-my-environment)，确保你的通信解决方案的性能和质量达到最优。

通信服务网络是支持 Azure 通信服务的网段。 此网络由 Microsoft 进行管理，使用靠近大多数客户网络的边缘在全球各地分布。 此网络负责传输中继、群组通话的媒体处理，以及其他支持丰富实时媒体通信的组件。

### <a name="types-of-traffic"></a>流量类型

通信服务主要以两种类型的流量进行构建：实时媒体和信令 。

实时媒体通过实时传输协议 (RTP) 进行传输。 此协议支持音频、视频和视频共享数据传输。 此数据对网络延迟问题很敏感。 尽管可使用 TCP 或 HTTP 传输实时媒体，但建议使用 UDP 作为传输层协议来支持高性能的最终用户体验。 通过 RTP 传输的媒体有效负载使用 SRTP 进行保护。

你的通信服务解决方案的用户将通过其客户端服务连接到你的服务。 这些设备与你的服务器之间的通信通过信令来进行处理。 例如，通过设备与你的服务之间的信令来支持呼叫启动和实时聊天。 大多数信令流量使用 HTTPS REST，不过在某些情况下，SIP 可用作信令流量协议。 虽然这类流量对延迟的敏感性更低，但低延迟信令将使你的解决方案的用户获得愉悦的最终用户体验。

### <a name="interoperability-restrictions"></a>互操作性限制

通过通信服务的媒体流存在以下限制：

**第三方媒体中继。** 不支持与第三方媒体中继之间的互操作性。 如果你的某个媒体终结点是通信服务，那么你的媒体仅可遍历 Microsoft 原生媒体中继，包括支持 Microsoft Teams 和 Skype for Business 的媒体中继。

带有 PSTN 的边界上的第三方会话边界控制器 (SBC) 应确定 RTP/RTCP 流，它们使用 SRTP 进行保护且不中继到下一跃点。 如果将流中继到下一跃点，则系统可能无法理解该流。

**第三方 SIP 代理服务器。** 带有第三方 SBC 和/或网关的通信服务信令 SIP 对话框可能会遍历 Microsoft 原生 SIP 代理（就像 Teams 一样）。 不支持与第三方 SIP 代理之间的互操作性。

**第三方 B2BUA（或 SBC）。** 流入和流出 PSTN 的通信服务媒体通过第三方 SBC 进行传输。 不支持与通信服务网络中的第三方 SBC 之间的互操作性（其中，第三方 SBC 会调解两个通信服务终结点）。

### <a name="unsupported-technologies"></a>不支持的技术

**VPN 网络。** 通信服务不支持通过 VPN 传输媒体。 如果用户使用的是 VPN 客户端，那么该客户端应按照[启用 Lync 媒体以绕过 VPN 隧道](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)中的说明，通过非 VPN 连接来拆分和路由媒体流量。

*请注意，虽然标题说的是 Lync，但它适用于 Azure 通信服务和 Teams。*

**数据包整形程序。** 不支持使用数据包剪裁、数据包检查或数据包整形设备，它们可能会使质量显著下降。

### <a name="call-flow-principles"></a>呼叫流原则

有 4 项一般原则来巩固通信服务呼叫流：

* 通信服务群组通话的第一位参与者确定托管该通话的区域。 在某些拓扑中，此规则存在一些例外，如下所述。
* 根据媒体处理需求来选择用于支持通信服务呼叫的媒体终结点，并且此选择不受通话中参与者的人数影响。 例如，点对点呼叫可能会使用云中的媒体终结点处理媒体来进行转录或录制，而有两位参与者的通话可能不会使用任何媒体终结点。 群组通话会使用媒体终结点来实现混合和路由目的。 此终结点根据托管通话的区域进行选择。 从客户端发送至媒体终结点的媒体流量可能会被直接路由；如果客户网络防火墙限制有规定，它也可能会使用 Azure 中传输中继。
* 用于对等呼叫的媒体流量采用所提供的最直接的路由，假设呼叫无需使用云中的媒体终结点。 首选路由是直接到远程对等（客户端）。 如果直接路由不可用，则会使用一个或多个传输中继来中继流量。 如果服务器充当数据包整形程序、VPN 服务器或其他可能会延迟处理并降低最终用户体验的函数，那么媒体流量不得遍历这些服务器。
* 信令流量始终流向最靠近用户的服务器。

若要详细了解所选媒体路径的详情，请查看[呼叫流概念文档](./call-flows.md)。


## <a name="call-flows-in-various-topologies"></a>各种拓扑中的呼叫流

### <a name="communication-services-internet"></a>通信服务 (Internet)

此拓扑供从云中使用通信服务的客户使用，无需进行任何本地部署（例如 Azure 直接路由）。 在此拓扑中，进出通信服务的流量会流经 Internet。

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure 通信服务拓扑。":::

*图 1 - 通信服务拓扑*

上图中箭头的方向是指会影响企业外围的连接的通信的初始方向。 对于媒体的 UDP，第一个数据包可能按相反的方向流动，但这些数据包可能会被阻止，直到其他方向的数据包正在流动为止。

流说明：
* 流 2* - 表示用户在客户网络上发起的流向 Internet 的流，这属于用户通信服务体验。 DNS 和对等媒体传输都属于这类流。
* 流 2 - 表示远程移动通信服务用户通过 VPN 发起的流向客户网络的流。
* 流 3 - 表示远程移动通信服务用户发起的流向通信服务终结点的流。
* 流 4* - 表示用户在客户网络上发起的流向通信服务的流。
* 流 5 - 表示客户网络中两位通信服务用户之间的对等媒体流。
* 流 6 - 表示两位远程移动通信服务用户之间通过 Internet 的对等媒体流。

### <a name="use-case-one-to-one"></a>用例：一对一

一对一通话会使用通用模型；在该模型中，呼叫方将获得一组由 IP 地址/端口构成的候选项，包括本地候选项、中继候选项和反射候选项（在中继内看到的客户端的公共 IP 地址）。 呼叫方会将这些候选项发送给被叫方；被叫方也会获得一组类似的候选项，并将它们发送给呼叫方。 STUN 连接性检查消息用于查找哪些呼叫方/被叫方媒体路径有效，系统会选择最优的工作路径。 然后，会使用所选的候选项对来发送媒体（即使用 SRTP 保护的 RTP/RTCP 数据包）。 传输中继被部署为 Azure 通信服务的一部分。

如果本地 IP 地址/端口候选项或反射候选项具有连接，则会为媒体选择客户端之间的直接路径（或使用 NAT）。 如果这两个客户端都在客户网络上，则应选择直接路径。 这要求在客户网络中具有直接 UDP 连接。 如果客户端都是游牧云用户，则根据 NAT/防火墙，媒体可能会使用直接连接。

如果一个客户端是客户网络上的内部客户端，另一个是外部客户端（例如移动云用户），那么不太可能在本地候选项或反射候选项之间实现直接连接。 在这种情况下，一种方式是使用任一客户端中的某个传输中继候选项（例如，从 Azure 中的传输中继获取中继候选项的内部客户端；外部客户端需要能够将 STUN/RTP/RTCP 数据包发送至传输中继）。 还有一种方式是内部客户端发送到移动云客户端获取的中继候选项。 虽然强烈建议对媒体使用 UDP 连接，但也支持 TCP。

**大致步骤：**
1.  通信服务用户 A 使用流 2 解析 URL 域名 (DNS)。
2.  用户 A 使用流 4 在 Teams 传输中继上分配一个媒体中继端口。
3.  通信服务用户 A 使用流 4 向通信服务发送一个带有 ICE 候选项的“邀请”。
4.  通信服务使用流 4 通知用户 B。
5.  用户 B 使用流 4 在 Teams 传输中继上分配一个媒体中继端口。
6.  用户 B 使用流 4 发送带有 ICE 候选项的“答复”，该答复会使用流 4 转发回到用户 A。
7.  用户 A 和用户 B 调用 ICE 连接测试，并且系统选择可用的最优媒体路径（查看下图了解各种用例）。
8.  这两位用户使用流 4 将遥测数据发送至通信服务。

### <a name="customer-network-intranet"></a>客户网络 (Intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="客户网络中的流量流。":::

*图 2 - 在客户网络中*

在步骤 7 中选择了对等媒体流 5。

此媒体传输是双向的。 流 5 的方向表示一端从连接的角度来发起通信。 在这种情况下，由于两个终结点都在客户网络中，因此使用哪个方向不重要。

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>客户网络到外部用户（媒体由 Teams 传输中继进行中继）

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="通过中继实现的一对一呼叫流。":::

*图 3 - 客户网络到外部用户（媒体由 Azure 传输中继进行中继）*

在步骤 7 中，选择了流 4（从客户网络到通信服务）和流 3（从远程移动通信服务用户到 Azure 通信服务）。

这些流通过 Azure 中的 Teams 传输中继进行中继。

此媒体传输是双向的。 方向指出哪一端从连接的角度发起通信。 在这种情况下，这些流通过不同的传输协议和地址用于信令和媒体。

### <a name="customer-network-to-external-user-direct-media"></a>客户网络到外部用户（直接媒体）

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="与外部用户的一对一呼叫流。":::

*图 4 - 客户网络到外部用户（直接媒体）*

在步骤 7 中，选择了流 2（通过 Internet 从客户网络到客户端的对等项）。

可选择与远程移动用户直接传输媒体（不通过 Azure 进行中继）。 也就是说，你可阻止此路径，强制通过 Azure 中的传输中继使用媒体路径。

此媒体传输是双向的。 流 2 到远程移动用户的方向表示一端从连接的角度来发起通信。

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>VPN 用户到外部用户（媒体由 Teams 传输中继进行中继）

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="通过中继与 VPN 用户的一对一呼叫流。":::

*图 5 - VPN 用户到外部用户（媒体由 Azur 中继进行中继）*

VPN 与客户网络之间的信令使用流 2*。 客户网络与 Azure 之间的信令使用流 4。 不过，媒体会绕过 VPN，使用流 3 和流 4 通过 Azure 媒体中继进行路由。

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN 用户到内部用户（直接媒体）

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="使用 VPN 与直接媒体的一对一呼叫流（内部用户）":::

*图 6 - VPN 用户到内部用户（直接媒体）*

VPN 与客户网络之间的信令使用流 2'。 客户网络与 Azure 之间的信令正在使用流 4。 不过，媒体会绕过 VPN，使用流 2 从客户网络路由到 Internet。

此媒体传输是双向的。 流 2 到远程移动用户的方向表示一端从连接的角度来发起通信。

### <a name="vpn-user-to-external-user-direct-media"></a>VPN 用户到外部用户（直接媒体）

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="使用 VPN 与直接媒体的一对一呼叫流（外部用户）":::

*图 7 - VPN 用户到外部用户（直接媒体）*

VPN 用户与客户网络之间的信令使用流 2* 和流 4 流向 Azure。 不过，媒体会绕过 VPN，使用流 6 进行路由。

此媒体传输是双向的。 流 6 到远程移动用户的方向表示一端从连接的角度来发起通信。

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>用例：通过通信服务 Trunk 从通信服务客户端到 PSTN

可通过通信服务从公用电话交换网 (PSTN) 发出和接收呼叫。 如果 PSTN Trunk 使用通信服务提供的电话号码进行连接，则没有任何针对此用例的特殊连接要求。 如果要将你自己的本地 PSTN Trunk 连接到 Azure 通信服务，可使用 Azure 直接路由（在 2021 年中提供）。

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="与 PSTN 参与者的一对一呼叫":::

*图 8 - 通过 Azure Trunk 从通信服务用户到 PSTN*

在这种情况下，从客户网络到 Azure 的信令和媒体使用流 4。

### <a name="use-case-communication-services-group-calls"></a>用例：通信服务群组通话

音频/视频/屏幕共享 (VBSS) 服务是 Azure 通信服务的一部分。 它有一个公共 IP 地址；必须可从客户网络和游牧云客户端访问此地址。 每个客户端/终结点需要能够连接到该服务。

内部客户端将按与一对一呼叫相关的方式获取本地候选项、反射候选项和中继候选项。 这些客户端会随附邀请将这些候选项发送服务。 该服务不使用中继，这是因为它有一个可公开访问的 IP 地址，所以它使用本地 IP 地址候选项进行响应。 客户端和服务将按与一对一呼叫相同的方式检查连接性。

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS 群组通话":::

*图 9 - 通信服务群组通话*

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [呼叫入门](../quickstarts/voice-video-calling/getting-started-with-calling.md)

你可能会对下列文档感兴趣：

- 详细了解[呼叫类型](../concepts/voice-video-calling/about-call-types.md)
- 了解[客户端-服务器体系结构](./client-and-server-architecture.md)