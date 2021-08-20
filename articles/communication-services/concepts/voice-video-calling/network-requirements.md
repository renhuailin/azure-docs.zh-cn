---
title: 为 Azure 通信服务准备你的组织的网络
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务语音和视频通话的网络要求
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3a4f842f096f6f8c283934b67808bb33b20f8fc1
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360192"
---
# <a name="network-recommendations"></a>网络建议

本文档总结了网络环境如何影响语音和视频呼叫质量。 有许多不同的因素会影响 Azure 通信服务实时媒体（音频、视频和应用程序共享）的质量。 这些因素包括网络质量、带宽、防火墙、主机和设备配置。


### <a name="network-quality"></a>网络质量

基于 IP 的实时媒体的质量受基础网络连接质量的显著影响，但尤其受以下各项的数值的影响：
* **滞后时间**。 这是 IP 数据包从网络上的 A 点到达 B 点所需的时间。 此网络传播延迟取决于两点之间的物理距离和流量流经的设备产生的任何额外开销。 延迟以单程时间或往返时间 (RTT) 来衡量。
* **数据包丢失**。 在给定的时间窗口内丢失的数据包的百分比。 数据包丢失直接影响音频质量 - 少量的个别数据包丢失几乎没有影响，但连续的数据包突发丢失会导致音频完全中断。
* **数据包间到达抖动或单纯抖动**。 这是连续数据包之间的延迟的平均变化。 通过缓冲，Azure 通信服务可以适应某些级别的抖动。 只有当抖动超过缓冲时，参与者才会注意到它的影响。

### <a name="network-bandwidth"></a>网络带宽

确保你的网络配置为支持并发 Azure 通信服务媒体会话和其他业务应用程序所需的带宽。 测试端到端网络路径的带宽瓶颈对于成功部署多媒体通信服务解决方案至关重要。

下面是 JavaScript SDK 的带宽要求：

|带宽|方案|
|:--|:--|
|40 kbps|点对点音频通话|
|500 kbps|点对点音频通话和屏幕共享|
|500 kbps|点对点标清视频通话，360p，每秒 30 帧|
|1.2 Mbps|点对点高清视频通话，分辨率为高清 720p，每秒 30 帧|
|500 kbps|群视频通话，360p，每秒 30 帧|
|1.2 Mbps|高清群视频通话，分辨率为高清 720p，每秒 30 帧| 

下面是原生 Windows、Android 和 iOS SDK 的带宽要求：

|带宽|方案|
|:--|:--|
|30 kbps|点对点音频通话 |
|130 kbps|点对点音频通话和屏幕共享|
|500 kbps|点对点标清视频通话，360p，每秒 30 帧|
|1.2 Mbps|点对点高清视频通话，分辨率为高清 720p，每秒 30 帧|
|1.5 Mbps|点对点高清视频通话，分辨率为高清 1080p，每秒 30 帧 |
|500kbps/1Mbps|群视频通话|
|1Mbps/2Mbps|高清群视频通话（1080p 屏幕上的 540p 视频）|

### <a name="firewalls-configuration"></a>防火墙配置

为了提供高质量的多媒体体验，在进行 Azure 通信服务连接时，需要通过 Internet 连接到特定端口和 IP 地址。 如果不能访问这些端口和 IP 地址，Azure 通信服务仍然可以工作。 但是，当打开推荐的端口和 IP 范围后，可提供最佳体验。

| 类别 | IP 范围或 FQDN | 端口 | 
| :-- | :-- | :-- |
| 媒体流量 | [Azure 公有云 IP 地址范围](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 到 3481；TCP 端口 443 |
| 信号、遥测、注册| *.skype.com、*.microsoft.com、*.azure.net、*.azureedge.net、*.office.com、*.trouter.io | TCP 443、80 |

### <a name="network-optimization"></a>网络优化

以下任务是可选的，不是推出 Azure 通信服务所必需的。 如果要优化网络和 Azure 通信服务性能，或者你知道存在一些网络限制，请使用本指南。
如果存在以下情况，你可能希望进一步进行优化：
* Azure 通信服务运行缓慢（带宽可能不足）
* 通话总是断开（可能是由于防火墙或代理阻止程序）
* 通话有静电干扰和中断，或者声音听起来像机器人（可能是由于抖动或数据包丢失）

| 网络优化任务 | 详细信息 |
| :-- | :-- |
| 网络规划 | 在本文档中，你可以找到通话的最低网络要求。 请参阅[有关规划网络的 Teams 示例](/microsoftteams/tutorial-network-planner-example) |
| 外部名称解析 | 确保运行 Azure 通信服务 SDK 的所有计算机都可以解析外部 DNS 查询，以发现 Azure 通信服务提供的服务，并确保防火墙不会阻止访问。 请确保 SDK 可以解析以下地址：*.skype.com、*.microsoft.com、*.azure.net、*.azureedge.net、*.office.com、*.trouter.io  |
| 维护会话持久性 | 确保防火墙不会为 UDP 更改映射网络地址转换 (NAT) 地址或端口
验证 NAT 池大小 | 验证用户连接所需的网络地址转换 (NAT) 池大小。 当多个用户和设备使用[网络地址转换 (NAT) 或端口地址转换 (PAT)](/office365/enterprise/nat-support-with-office-365) 访问 Azure 通信服务时，请确保在每个可公开路由的 IP 地址后面隐藏的设备不超过支持的数量。 请确保为 NAT 池分配足够的公共 IP 地址，以防止端口耗尽。 端口耗尽会导致内部用户和设备无法连接到 Azure 通信服务 |
| 入侵检测和防护指南 | 如果你的环境为出站连接部署了[入侵检测](../../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)或预防系统 (IDS/IPS) 以实现额外的安全层，请允许所有 Azure 通信服务 URL |
| 配置拆分隧道 VPN | 我们建议你为绕过虚拟专用网络 (VPN) 的 Teams 流量提供备用路径（通常称为[拆分隧道 VPN](/windows/security/identity-protection/vpn/vpn-routing)）。 拆分隧道意味着 Azure 通信服务的流量不会通过 VPN，而是直接发往 Azure。 绕过 VPN 会对媒体质量产生积极影响，并减少来自 VPN 设备和组织网络的负载。 若要实现拆分隧道 VPN，请与你的 VPN 供应商合作。 建议绕过 VPN 的其他原因： <ul><li> VPN 的设计或配置通常不支持实时媒体。</li><li> VPN 可能也不支持 UDP（Azure 通信服务需要 UDP）</li><li>VPN 还在已经加密的媒体流量之上引入了额外的加密层。</li><li>由于存在流经 VPN 设备的发夹流量，与 Azure 通信服务的连接可能不够高效。</li></ul>|
| 实施 QoS | [使用服务质量 (QoS)](/microsoftteams/qos-in-teams) 配置数据包优先级。 这将提高通话质量，帮助你监视和排查通话质量问题。 应在托管网络的所有段上实施 QoS。 即使已经充分预配了网络来提供足够的带宽，QoS 也可以在发生意外的网络事件时缓解风险。 使用 QoS 时，语音流量优先，因此这些意外事件不会对质量产生负面影响。 | 
| 优化 WiFi | 与 VPN 类似，WiFi 网络并不一定设计或配置为支持实时媒体。 规划或优化 WiFi 网络以支持 Azure 通信服务是进行高质量部署的一个重要注意事项。 请考虑以下因素： <ul><li>实施 QoS 或 WiFi 多媒体 (WMM)，以确保通过 WiFi 网络正确地设置媒体流量的优先级。</li><li>规划并优化 WiFi 频段和接入点放置。 2\.4 GHz 范围可能会提供足够的体验，具体取决于接入点放置，但接入点通常受在该范围内运行的其他使用者设备的影响。 5 GHz 范围因其范围的密集性而更适合实时媒体，但它需要更多的接入点才能实现足够的覆盖。 端点还需要支持该范围，并相应地配置为利用这些频段。</li><li>如果你使用双频段 WiFi 网络，请考虑实施频段控制。 频段控制是 WiFi 供应商实施的一项技术，旨在促使双频段客户端使用 5GHz 范围。</li><li>当同一信道的接入点靠得太近时，它们可能会导致信号重叠和无意中竞争，使用户体验降级。 请确保相邻的接入点位于不重叠的信道上。</li></ul> 每个无线供应商都有其自己的无线解决方案部署建议。 请咨询你的 WiFi 供应商以获取具体指导。|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>操作系统和浏览器（适用于 JavaScript SDK）

Azure 通信服务语音/视频 SDK 支持某些操作系统和浏览器。
请在[通话概念文档](./calling-sdk-features.md)中了解通话 SDK 支持的操作系统和浏览器。

## <a name="next-steps"></a>后续步骤

你可能会对下列文档感兴趣：

- 详细了解[通话库](./calling-sdk-features.md)
- 了解[客户端-服务器体系结构](../client-and-server-architecture.md)
- 了解[呼叫流拓扑](../call-flows.md)
