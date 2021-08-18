---
title: 使用运行状况探测进行缩放，并为服务提供高可用性
titleSuffix: Azure Load Balancer
description: 本文介绍如何使用运行状况探测监视 Azure 负载均衡器背后的实例
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: c6dbfd0020598c280e006e863467a4dfa9b29a0c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284516"
---
# <a name="load-balancer-health-probes"></a>负载均衡器运行状况探测

在 Azure 负载均衡器中使用负载均衡规则时，需要指定运行状况探测，使负载均衡器能够检测后端终结点状态。  运行状况探测的配置和探测响应确定哪个后端池实例要接收新流。 可以使用运行状况探测来检测后端终结点上应用程序的故障。 还可以对运行状况探测生成自定义响应，并使用用于控制流的运行状况探测来管理负载或计划内停机。 运行状况探测失败时，负载均衡器停止向各个不正常的实例发送新流。 出站连接不受影响，仅入站连接受到影响。

运行状况探测支持多个协议。 可用的具体运行状况探测协议因负载均衡器 SKU 而异。  此外，服务的行为也因负载均衡器 SKU 而异，如下表所述：

| | 标准 SKU | 基本 SKU |
| --- | --- | --- |
| **[探测类型](#types)** | TCP、HTTP、HTTPS | TCP、HTTP |
| **[探测停止行为](#probedown)** | 所有探测停止，所有 TCP 流继续。 | 所有探测停止，所有 TCP 流过期。 | 


>[!IMPORTANT]
>请通篇查看本文档，包括下面有关创建可靠服务的重要[设计指导原则](#design)。

>[!IMPORTANT]
>负载均衡器运行状况探测源自 IP 地址 168.63.129.16，要使探测将实例标记为运行，不得阻止这些探测。  有关详细信息，请查看[探测源 IP 地址](#probesource)。 若要查看后端实例中的该探测流量，请查看[此常见问题解答](/azure/load-balancer/load-balancer-faqs#probes)。

>[!IMPORTANT]
>无论配置的超时阈值如何，HTTP(S) 负载均衡器运行状况探测都会自动探测实例，前提是服务器返回的状态代码不是“HTTP 200 正常”，或者连接通过 TCP 重置终止。

## <a name="probe-configuration"></a><a name="probes"></a>探测配置

运行状况探测配置由以下元素组成：

- 各个探测的间隔持续时间
- 在探测过渡到其他状态之前必须观测到的探测响应数
- 探测的协议
- 探测的端口
- 使用 HTTP(S) 探测时用于 HTTP GET 的 HTTP 路径

>[!NOTE]
>使用 Azure PowerShell、Azure CLI、模板或 API 时，不强制使用或检查探测定义。 仅在使用 Azure 门户时才进行探测验证测试。

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>了解应用程序信号、信号检测和平台反应

探测响应数适用于以下两项：

- 允许将实例标记为运行所要成功的探测次数
- 导致将实例标记为关闭的超时探测数目。

指定的超时和间隔值确定实例是标记为运行还是停止。  将间隔持续时间与探测响应数相乘可以得出必须检测探测响应的持续时间。  实现所需的探测后，服务将做出反应。

可以通过一个示例来进一步演示该行为。 如果将探测响应数设置为 2，将间隔设置为 5 秒，则意味着，必须在 10 秒间隔内观测到 2 次探测超时失败。  由于在应用程序更改状态时发送探测的时间未同步，因此我们可以分两种情况来界定检测时间：

1. 如果应用程序在第一个探测抵达之前的那一刻开始生成超时探测响应，则这些事件的检测需要 10 秒（2 x 5 秒间隔），加上应用程序发出超时信号到第一个探测抵达时的持续时间。  可以假设此项检测花费的时间略微超过 10 秒。
2. 如果应用程序在第一个探测抵达之后的那一刻开始生成超时的探测响应，则这些事件的检测只会在下一个探测（和超时）抵达时加上额外 10 秒（2 x 5 秒间隔）持续时间之后才开始。  可以假设此项检测花费的时间略少于 15 秒。

对于此示例，一旦发生检测，平台就会花费少量的时间对此更改做出反应。  这意味着，根据以下条件 

1. 当应用程序开始更改状态时，并且
2. 此项更改已检测到并满足所需条件（按指定的间隔发送的探测次数）时，并且
3. 在整个平台中传达检测时 

可以假设对超时探测响应做出反应的时间介于对应用程序信号中的更改做出反应的最短时间（略微超过 10 秒）与最长时间（略微超过 15 秒）之间。  此示例旨在演示发生的情况，但是，无法准确预测超出此示例所示大致指导范围的持续时间。

>[!NOTE]
>运行状况探测将探测后端池中所有正在运行的实例。 如果某个实例已停止，则只有重启该实例才会对其进行探测。

## <a name="probe-types"></a><a name="types"></a>探测类型

可将运行状况探测使用的协议配置为下列其中一项：

- [TCP 侦听器](#tcpprobe)
- [HTTP 终结点](#httpprobe)
- [HTTPS 终结点](#httpsprobe)

可用的协议取决于所用的负载均衡器 SKU：

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **标准 SKU** |    &#9989; |   &#9989; |   &#9989; |
| **基本 SKU** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>TCP 探测

TCP 探测通过使用定义的端口执行三方开放式 TCP 握手来初始化连接。  TCP 探测使用四向闭合式 TCP 握手来终止连接。

最小探测间隔为 5 秒，不正常响应的最小数目为 2。  所有间隔的总持续时间不能超过 120 秒。

如果出现以下情况，TCP 探测将会失败：
* 实例上的 TCP 侦听器在超时期限内根本未做出响应。  根据超时的探测请求（配置为标记探测停止之前未获应答的请求）数目，已将探测标记为停止。
* 探测从实例接收 TCP 重置。

下面演示了如何在资源管理器模板中表达此类探测配置：

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP/HTTPS 探测

>[!NOTE]
>HTTPS 探测仅适用于[标准负载均衡器](./load-balancer-overview.md)。

HTTP 和 HTTPS 探测构建在 TCP 探测的基础之上，发出包含指定路径的 HTTP GET。 这两个探测都支持 HTTP GET 的相对路径。 与 HTTP 探测一样，HTTPS 探测中也添加了传输层安全性（TLS，前称为 SSL）包装器。 如果实例在超时期限内做出响应并返回 HTTP 状态 200，则将运行状况探测标记为运行。  默认情况下，运行状况探测每隔 15 秒尝试检查配置的运行状况探测端口。 最小探测间隔为 5 秒。 所有间隔的总持续时间不能超过 120 秒。

如果探测端口也是服务本身的侦听器，则也可以使用 HTTP/HTTPS 探测来实现你自己的逻辑，以便从负载均衡器轮转中删除实例。 例如，如果实例的 CPU 利用率超过 90% 并返回非 200 HTTP 状态，则你可以决定删除该实例。 

> [!NOTE] 
> HTTPS 探测要求使用基于证书的证书，该证书在整个链中的最小签名哈希为 SHA256。

如果使用云服务，并且现有的 Web 角色使用 w3wp.exe，则你还可以实现自动网站监视。 网站代码中的错误会将非 200 状态返回给负载均衡器探测。

如果出现以下情况，HTTP/HTTPS 探测将会失败：
* 探测终结点返回非 200 的 HTTP 响应代码（例如，403、404 或 500）。 这会立即标记运行状况探测停止。 
* 探测终结点在最小探测间隔和 30 秒超时时间内根本不响应。 在探测标记为未运行并且达到所有超时间隔的总和之前，多个探测请求可能无应答。
* 探测终结点通过 TCP 重置关闭连接。

下面演示了如何在资源管理器模板中表达此类探测配置：

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>来宾代理探测（仅限经典模式）

云服务角色（辅助角色和 Web 角色）默认使用来宾代理进行探测监视。  来宾代理探测是最后一道配置。  请始终对 TCP 或 HTTP 探测显式使用运行状况探测。 对于大多数应用程序方案而言，来宾代理探测的有效性不如显式定义的探测。

来宾代理探测是对 VM 中来宾代理执行的检查。 仅当实例处于“就绪”状态时，负载均衡器才侦听并响应“HTTP 200 正常”响应。 （其他状态包括“繁忙”、“正在回收”或“正在停止”。）

有关详细信息，请参阅[配置运行状况探测的服务定义文件 (csdef)](/previous-versions/azure/reference/ee758710(v=azure.100)) 或[开始为云服务创建公共负载均衡器](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services)。

如果来宾代理无法使用“HTTP 200 正常”响应，则负载均衡器会将实例标记为无响应。 然后停止向该实例发送流。 负载均衡器继续检查实例。 

如果来宾代理使用 HTTP 200 做出响应，则负载均衡器会再次向该实例发送新流。

使用 Web 角色时，网站代码通常在不受 Azure 结构或来宾代理监视的 w3wp.exe 中运行。 这系统不会向来宾代理报告 w3wp.exe 中的失败（例如，HTTP 500 响应）。 因此，负载均衡器不会将该实例退出轮转。

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>探测行为

在以下情况下，会将 TCP、HTTP 和 HTTPS 运行状况探测视为正常，并将后端终结点标记为正常：

* VM 启动后，运行状况探测成功。
* 已达到将后端终结点标记为正常所需的指定探测次数。

已达到正常状态的任何后端终结点都符合接收新流的条件。  

> [!NOTE]
> 如果运行状况探测出现波动，负载均衡器会等待更长时间，然后将后端终结点重新置于正常状态。 这段额外的等待时间可保护用户和基础结构，是在策略中有意指定的。

## <a name="probe-down-behavior"></a><a name="probedown"></a>探测停止行为

### <a name="tcp-connections"></a>TCP 连接

与剩余正常后端终结点建立新的 TCP 连接将会成功。

如果后端终结点的运行状况探测失败，与此后端终结点建立的 TCP 连接会继续。

如果后端池中所有实例的所有探测都失败，则不会将任何新流发送到后端池。 标准负载均衡器将允许已建立的 TCP 流继续。  基本负载均衡器会终止发往后端池的所有现有 TCP 流。
 
负载均衡器是一个直通服务（不终止 TCP 连接），流始终在客户端与 VM 的来宾 OS 和应用程序之间传送。 所有探测均停止的池会导致前端不会对 TCP 连接打开尝试 (SYN) 做出响应，因为没有任何正常的后端终结点可以接收流和响应 SYN-ACK。

### <a name="udp-datagrams"></a>UDP 数据报

UDP 数据报将传送到正常的后端终结点。

UDP 是无连接的，并且系统不会跟踪 UDP 的流状态。 如果任何后端终结点的运行状况探测失败，则现有的 UDP 流将移到后端池中的另一个运行正常的实例。

如果后端池中所有实例的所有探测都失败，则基本和标准负载均衡器的现有 UDP 流将会终止。

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>探测源 IP 地址

负载均衡器对其内部运行状况模型使用分布式探测服务。 探测服务驻留在每个 VM 主机上，可按需编程，以根据客户的配置生成运行状况探测。 运行状况探测流量直接在生成运行状况探测的探测服务与客户 VM 之间传送。 所有负载均衡器运行状况探测源自 IP 地址 168.63.129.16（源）。  可以使用 VNet 中不属于 RFC1918 空间的 IP 地址空间。  使用全局保留的、由 Microsoft 拥有的 IP 地址可以减少某个 IP 地址与 VNet 中使用的 IP 地址空间发生冲突的可能性。  此 IP 地址在所有区域中相同且不会改变，不会造成安全风险，因为只有内部 Azure 平台组件可以从此 IP 地址探寻数据包。 

AzureLoadBalancer 服务标记在[网络安全组](../virtual-network/network-security-groups-overview.md)中标识此源 IP 地址，默认允许运行状况探测流量。

除了负载均衡器运行状况探测外，[以下操作也使用此 IP 地址](../virtual-network/what-is-ip-address-168-63-129-16.md)：

- 使 VM 代理能够与平台通信，以表明它处于“就绪”状态
- 启用与 DNS 虚拟服务器的通信，以便为未定义自定义 DNS 服务器的客户提供筛选的名称解析。  此筛选可确保客户只能解析其部署的主机名。
- 使 VM 能够从 Azure 中的 DHCP 服务获取动态 IP 地址。

## <a name="design-guidance"></a><a name="design"></a>设计指导

运行状况探测使服务具有复原能力，并使其可缩放。 错误的配置或不当的设计模式可能会影响服务的可用性与可伸缩性。 请通篇阅读本文档，并考虑在将此探测响应标记为停机或运行时对方案造成的影响，以及对应用程序方案可用性造成的影响。

为应用程序设计运行状况模型时，应该探测后端终结点上可以反映该实例以及所提供应用程序服务的运行状况的端口。  应用程序端口和探测端口不必相同。  在某些方案中，探测端口可能需要不同于应用程序提供服务所用的端口。  

有时，有效的方案是让应用程序生成运行状况探测响应，以便不仅检测应用程序的运行状况，而且还直接让负载均衡器知道实例是否要接收新流。  可以通过使运行状况探测发生故障来操控探测响应，使应用程序能够创建反压并限制向某个实例传送新流；或者，可以准备维护应用程序并开始清理方案。  使用标准负载均衡器时，[探测停止](#probedown)信号始终允许继续处理 TCP 流，直到达到空闲超时或连接关闭为止。 

对于 UDP 负载均衡，应从后端终结点生成自定义运行状况探测信号，并使用面向相应侦听器的 TCP、HTTP 或 HTTPS 运行状况探测来反映 UDP 应用程序的运行状况。

对[标准负载均衡器](./load-balancer-overview.md)使用 [HA 端口负载均衡规则](load-balancer-ha-ports-overview.md)时，将对所有端口进行负载均衡个运行状况探测响应必须反映整个实例的状态。

不要通过接收运行状况探测的实例在 VNet 中的另一个实例上转换或代理某个运行状况探测，因为此配置可能导致方案中出现连锁故障。  考虑以下方案：在负载均衡器资源后端池中部署一组第三方设备，以便为设备提供可伸缩性和冗余；配置运行状况探测来探测由第三方设备代理或转换成设备后面的其他虚拟机的端口。  如果探测用于将请求转换或代理到设备后面的其他虚拟机的同一端口，来自设备后面单个虚拟机的任何探测响应会将设备本身标记为完全停止。 此配置可能导致整个应用程序方案因设备后面的单个后端终结点而发生连锁故障。  触发器可能是一种间歇性探测故障，该故障导致负载均衡器将原始目标（设备实例）标记为停止，从而可能禁用整个应用程序方案。 请改为探测设备本身的运行状况。 选择用于确定运行状况信号的探测是网络虚拟设备 (NVA) 方案的重要考虑因素，必须咨询应用程序供应商，以了解哪种运行状况信号适合此类方案。

如果在防火墙策略中不允许探测的[源 IP](#probesource)，运行状况探测将会失败，因为它无法访问实例。  而由于发生运行状况探测失败，负载均衡器会将实例标记为关闭。  这种错误的配置可能导致负载均衡的应用程序方案失败。

要使负载均衡器的运行状况探测将实例标记为运行，**必须** 在任何 Azure [网络安全组](../virtual-network/network-security-groups-overview.md)和本地防火墙策略中允许此 IP 地址。  默认情况下，每个网络安全组都包含[服务标记](../virtual-network/network-security-groups-overview.md#service-tags) AzureLoadBalancer，以允许运行状况探测流量。

若要测试运行状况探测故障或者将单个实例标记为停止，可以使用[网络安全组](../virtual-network/network-security-groups-overview.md)显式阻止该运行状况探测（目标端口或[源 IP](#probesource)），并模拟探测故障。

不要使用包含 168.63.129.16 的、使用 Microsoft 拥有的 IP 地址范围来配置 VNet。  这种配置与运行状况探测的 IP 地址冲突，可能导致方案失败。

如果 VM 上有多个接口，则需要确保能够响应收到请求的接口上的探测。  可能需要根据每个接口，对 VM 中的此地址进行源网络地址转换。

不要启用 [TCP 时间戳](https://tools.ietf.org/html/rfc1323)。  启用 TCP 时间戳可能导致运行状况探测因 VM 来宾 OS TCP 堆栈删除 TCP 数据包而失败，从而导致负载均衡器将相应的终结点标记为停止。  默认情况下，TCP 时间戳在安全强化的 VM 映像中定期启用，必须将其禁用。

## <a name="monitoring"></a>监视

公共和内部[标准负载均衡器](./load-balancer-overview.md)通过 Azure Monitor 将每个终结点和后端终结点运行状况探测状态公开为多维指标。 这些指标可由其他 Azure 服务或合作伙伴应用程序使用。 

基本公共负载均衡器通过 Azure Monitor 日志公开针对每个后端池汇总的运行状况探测状态。  Azure Monitor 日志不适用于内部基本负载均衡器。  可以使用 [Azure Monitor 日志](./monitor-load-balancer.md)来检查公共负载均衡器探测运行状况和探测计数。 可以配合 Power BI 或 Azure Operation Insights 使用日志记录，以提供有关负载均衡器运行状况的统计信息。

## <a name="limitations"></a>限制

- HTTPS 探测不支持使用客户端证书的相互身份验证。
- 应假定启用 TCP 时间戳后，运行状况探测会失败。

## <a name="next-steps"></a>后续步骤

- 详细了解[标准负载均衡器](./load-balancer-overview.md)
- [使用 PowerShell 在资源管理器中开始创建公共负载均衡器](quickstart-load-balancer-standard-public-powershell.md)
- [运行状况探测的 REST API](/rest/api/load-balancer/loadbalancerprobes/)
- 通过[负载均衡器的 Uservoice](https://aka.ms/lbuservoice) 请求新的运行状况探测功能