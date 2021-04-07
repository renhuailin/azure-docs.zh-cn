---
title: 排查网络链路性能问题：Azure
description: 此页提供一种标准化方法来测试 Azure 网络链接性能。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027462"
---
# <a name="troubleshooting-network-performance"></a>网络性能故障排除
## <a name="overview"></a>概述
Azure 提供了一种稳定、快速地从本地网络连接到 Azure 的方法。 站点到站点 VPN 和 ExpressRoute 等方法被大小客户成功用于在 Azure 中运行业务。 但是，若性能不满足期望或先前经验，会发生什么呢？ 本文可帮助对测试特定环境和为其设置基线时使用的方法进行规范。

你将了解如何采用一致的方法轻松测试两台主机之间的网络延迟和带宽。 本文还就如何查看 Azure 网络以帮助隔离问题点提供了一些建议。 所讨论的 PowerShell 脚本和工具需要网络上有两台主机（在所测试链接的任一端）。 一台主机必须是 Windows Server 或 Desktop，另一台可以是 Windows 或 Linux。 

>[!NOTE]
>故障排除方法、工具和所用方法都可根据个人喜好挑选。 本文介绍了我通常使用的方法和工具。 你所用方法可能有所不同，不过解决问题的方式不同并无大碍。 但是，如果没有一个确定的方法，则可以使用本文来帮助构建自己的方法、工具和首选项，解决网络问题。
>
>

## <a name="network-components"></a>网络要素
深入了解故障排除之前，先来讨论一些常用术语和要素。 这可确保我们思考 Azure 中支持连接的端到端链中的每个要素。
![1][1]

概括来看，我先介绍三个主要的网络路由域：

- Azure 网络（右侧蓝色云朵）
- Internet 或 WAN（中间绿色云朵）
- 公司网络（左侧桃色云朵）

从右到左查看关系图，我们来简要讨论一下每个要素：
 - **虚拟机** - 服务器可以有多个 NIC。 请确保任何静态路由、默认路由和操作系统设置都按照预想的方式发送和接收流量。 此外，每个 VM SKU 具有带宽限制。 如果使用较小的 VM SKU，流量会受到 NIC 可用带宽的限制。 我通常使用 DS5v2 来进行测试（然后在测试完成后删除，节省资金），确保 VM 有足够的带宽。
 - **NIC** - 确保你知道分配给问题 NIC 的专用 IP。
 - **NIC NSG** - 可能会有特定的 NSG 应用于 NIC 级别，确保 NSG 规则集适用于正尝试传递的流量。 例如，确保已打开 iPerf 的 5201 端口、RDP 的 3389 或 SSH 22，可让测试流量通过。
 - **VNet 子网** - NIC 已分配给特定子网，确保你知道该子网及与该子网关联的规则。
 - **子网 NSG** - NSG 与 NIC 一样可应用于子网。 确保 NSG 规则集适合于正尝试传递的流量。 （对于 NIC 的入站流量，将首先应用子网 NSG，然后应用 NIC NSG。 当流量从 VM 出站时，首先应用 NIC NSG，然后应用子网 NSG）。
 - **子网 UDR** - 用户定义的路由可将流量定向到中间跃点（如防火墙或负载均衡器）。 确保你知道是否存在 UDR 可用于你的流量。 如果存在，请了解它指向何处，以及下一跃点将如何影响你的流量。 例如，防火墙可在两台相同主机之间传递一些流量，并拒绝其他一些流量。
 - **网关子网/NSG/UDR** - 网关子网与 VM 子网一样可具有 NSG 和 UDR。 确保你知道是否存在这两者，以及它们对流量的影响。
 - **VNet 网关 (ExpressRoute)** - 启用对等互连 (ExpressRoute) 或 VPN 后，不会有许多设置会影响到流量路由方式及是否进行流量路由。 如果你有一个 VNet 网关连接到多个 ExpressRoute 线路或 VPN 隧道，则应注意连接权重设置。 连接权重会影响连接首选项，并确定流量所采用的路径。
 - **路由筛选器**（未显示）- 在通过 ExpressRoute 使用 Microsoft 对等互连时，需要使用路由筛选器。 如果未收到任何路由，请检查路由筛选器是否已正确配置且已正确应用到线路。

此时，你处于链接的 WAN 部分。 此路由域可以是服务提供商、公司 WAN 或 Internet。 这些链路涉及很多跃点、设备和公司，这可能会使故障排除变得很困难。 你需要首先排除 Azure 和公司网络，然后才能调查这两者之间的跃点。

上图中，最左侧是公司网络。 根据公司大小，此路由域可以是你和 WAN 之间的一些网络设备，或是校园/企业网络中的多层设备。

鉴于这三种不同的高级网络环境的复杂性， 通常最好从边缘开始，试着显示性能良好的位置和性能下降的位置。 此方法可帮助识别这三种环境的问题路由域。 然后，可将故障排除重点放在该特定环境上。

## <a name="tools"></a>工具
可使用 ping 和 traceroute 等基本工具对大多数网络问题进行分析及隔离。 基本上不需要像使用 Wireshark 等工具进行数据包分析那样深入。 

为了帮助进行故障排除，开发了 Azure 连接工具包 (AzureCT) 将这些工具中的一部分放入简单的包中。 对于性能测试，iPerf 和 PSPing 之类的工具可为你提供网络相关信息。 iPerf 是用于基本性能测试的常用工具，使用起来很方便。 PSPing 是由 SysInternals 开发的 ping 工具。 PSPing 可执行 ICMP 和 TCP ping 来连接到远程主机。 这两个工具都是轻量级的，只需将文件复制到主机的目录便可轻松“安装”。

这些工具和方法已打包到一个 PowerShell 模块 (AzureCT) 中，可供你安装和使用。

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - Azure 连接工具包
AzureCT PowerShell 模块有两个组成部分 - [可用性测试][Availability Doc]和[性能测试][Performance Doc]。 本文只涉及性能测试，所以，我们一起来看看 PowerShell 模块中的两个链接性能命令。

使用此工具包进行性能测试有三个基本步骤。 1) 安装 PowerShell 模块，2) 安装支持性应用程序 iPerf 和 PSPing，3) 运行性能测试。

1. 安装 PowerShell 模块

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    此命令用于下载 PowerShell 模块，并将其安装在本地。

2. 安装支持性应用程序
    ```powershell
    Install-LinkPerformance
    ```
    此 AzureCT 命令将 iPerf 和 PSPing 安装在新目录“C:\ACTTools”中，还会打开 Windows 防火墙端口以允许 ICMP、端口 5201 (iPerf) 流量。

3. 运行性能测试

    首先，iPerf 须在远程主机上安装，在服务器模式下运行。 另外，请确保远程主机在 3389 (RDP for Windows) 或 22 (SSH for Linux) 上进行侦听，并在 iPerf 端口 5201 上允许流量。 如果远程主机为 Windows，则安装 AzureCT 并运行 Install-LinkPerformance 命令。 该命令将设置 iPerf，还将设置在服务器模式下成功启动 iPerf 所需的防火墙规则。 
    
    远程计算机准备就绪后，在本地计算机上打开 PowerShell 并启动测试：
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    此命令运行一系列的并发负荷和延迟测试，帮助估计带宽容量和网络链接延迟。

4. 查看测试的输出

    PowerShell 输出格式看起来类似于：

    ![4][4]

    所有 iPerf 和 PSPing 测试的详细结果都位于“C:\ACTTools”上 AzureCT 工具目录中的单个文本文件中。

## <a name="troubleshooting"></a>疑难解答
如果性能测试没能提供预期结果，请弄清楚为何要循序渐进逐步操作。 考虑到路径中的组件数量，系统性的方法比到处查找能更快地解决问题。 通过系统性地故障排除，你可避免多次执行不必要的测试。

>[!NOTE]
>此处的情况是出现了性能问题而非连接问题。 如果流量根本不传递，则需要采取不同的措施。
>
>

首先，怀疑假设。 期望是否合理？ 例如，如果你有一个 1 Gbps ExpressRoute 线路，延迟为 100 毫秒。 考虑到高延迟链路上 TCP 的性能特征，期望流量完全达到 1 Gbps 是不合理的。 有关更多性能假设信息，请参阅[引用部分](#references)。

接下来，建议从路由域之间的边缘开始，试着将问题隔离到一个主要的路由域。 可从公司网络、WAN 或 Azure 网络开始。 人们常常会责怪路径中的“黑盒”。 责怪黑盒很容易，但这可能大幅拖慢解决速度， 尤其是当问题出现在你可进行更改来解决的方面时。 请确保移交给服务提供商或 ISP 之前已竭尽全力。

确定了可能存在问题的主要路由域之后，应创建一个问题区域的关系图。 通过在白板、记事本或 Visio 上绘制图示，你可以系统性地查找原因，隔离问题。 可以规划测试点，在清除区域时更新“作战图”，或在测试进行过程中深入挖掘。

形成关系图后，开始将网络分成几个部分，缩小问题范围。 找出其起作用的地方和不起作用的地方。 不断移动测试点，隔离有问题的要素。

此外，也请记得查看 OSI 模型的其他层。 注意到网络和第 1 - 3 层（物理层、数据层和网络层）很容易，但是问题也可能出现在应用程序层的第 7 层。 保持开放的心态，验证假设。

## <a name="advanced-expressroute-troubleshooting"></a>高级 ExpressRoute 故障排除
如果不确定云边缘的实际所在，那么隔离 Azure 要素便是一个难题。 使用 ExpressRoute 时，边缘是名为 Microsoft 企业边缘 (MSEE) 的网络要素。 使用 ExpressRoute 时，MSEE 是进入 Microsoft 网络的第一个接触点，也是离开 Microsoft 网络时的最后一个跃点。 在 VNet 网关和 ExpressRoute 线路之间创建连接对象时，实际上正在连接 MSEE。 根据流量的方向确定是将 MSEE 识别为第一个跃点还是最后一个跃点，这种方法对隔离 Azure 网络问题来说非常重要。 知道流量方向将证实问题是在 Azure 中，还是在 WAN 或企业网络中的更下游。 

![2][2]

>[!NOTE]
> 请注意，MSEE 不在 Azure 云中。 ExpressRoute 实际是在 Microsoft 网络边缘，而不是在 Azure 中。 通过 ExpressRoute 连接到 MSEE 后，你就连接到了 Microsoft 网络，然后便可转到任何云服务，例如 Microsoft 365（使用 Microsoft 对等互连）或 Azure（使用专有和/或 Microsoft 对等互连）。
>
>

如果两个 Vnet 连接到同一个 ExpressRoute 线路，你可执行一系列测试来隔离 Azure 中的问题。
 
### <a name="test-plan"></a>测试计划
1. 在 VM1 和 VM2 之间运行 Get-LinkPerformance 测试。 此测试可让你了解到问题是否是出在本地。 如果此测试带来了可接受的延迟和带宽结果，则可将本地 VNet 网络标记为良好。
2. 如果本地 VNet 流量状态良好，则在 VM1 和 VM3 之间运行 Get-LinkPerformance 测试。 此测试通过 Microsoft 网络实现连接到 MSEE ，然后再连回 Azure。 如果此测试带来了可接受的延迟和带宽结果，则可将 Azure 网络标记为良好。
3. 如果已排除 Azure，你可在公司网络上执行一系列类似的测试。 如果此测试结果也是良好，则应对服务提供商或 ISP 执行测试来诊断 WAN 连接状态。 示例：在两个分支机构之间运行此测试，或在桌面和数据中心服务器之间进行测试。 根据测试内容，找到可实现该路径的终结点（例如服务器和客户端电脑）。

>[!IMPORTANT]
> 对于每次测试，都务必标记运行测试时的时间，并将测试结果记录在共同位置（我喜欢记录在 OneNote 或 Excel 上）。 每次测试运行应有相同的输出，以便在测试运行之间比较结果数据，并在数据中没有“遗漏”。 我使用 AzureCT 进行故障排除主要是因为多个测试间的一致性。 “魔力”在于从每个测试中获取的一致测试结果和数据输出，而非我所运行的精确负载方案。 如果稍后发现问题是偶尔发生的，每次记录时间并获得一致的数据特别有用。 要勤于采集数据，这样可避免重复测试相同的方案（多年前我了解到这不容易）。
>
>

## <a name="the-problem-is-isolated-now-what"></a>隔离了问题，然后呢？
你越使问题孤立，就能越快找到解决方案。 有时，你会遇到没法继续进行故障排除的情况。 例如，你看到服务提供商中的链路采用通过欧洲的跃点，但你预期路径全部在亚洲。 这时，你该向他人寻求帮助。 你要向谁寻求帮助取决于你将问题隔离到路由域。 如果能将问题范围缩小到一个特定组件，那就更好了。

对于公司网络问题，内部 IT 部门或服务提供商可帮助进行设备配置或硬件修复。

对于 WAN，与服务提供商或 ISP 分享测试结果有助于他们开始操作。 分享结果还可避免重复你已完成的工作。 如果他们想亲自验证你的结果，也不要生气。 基于他人的报告结果进行故障排除时，“信任但验证”是不错的信条。

使用 Azure，尽可能详细地隔离问题后，可以查看 [Azure 网络文档][Network Docs]，随后[打开支持票证][Ticket Link]（如仍有需要）。

## <a name="references"></a>参考
### <a name="latencybandwidth-expectations"></a>延迟/带宽预期
>[!TIP]
> 正在测试的端点之间的地理延迟（英里或公里）是迄今为止最大的延迟要素。 虽然涉及设备延迟（物理和虚拟要素、跃点数等），但在处理 WAN 连接时，已证明地理位置是整体延迟的最大要素。 还务必注意此距离是光纤行程的距离，而不是直线或路线图的距离。 要准确获得此距离十分困难。 因此，我通常在 Internet 上使用城市距离计算器，也知道此方法测量结果很不准确，但对设立一般预期还是足够了。
>
>

我已在美国西雅图、美国华盛顿设置了 ExpressRoute。 下表显示的是我测试不同 Azure 位置时所看到的延迟和带宽。 我已对每个测试端点之间的距离进行了预估。

测试设置：
 - 运行 Windows Server 2016 的物理服务器，其 NIC 为 10 Gbps 且连接到 ExpressRoute 线路。
 - 启用了专用对等互连的区域中的 10Gbps 高级 ExpressRoute 电路。
 - 指定的区域中具有 UltraPerformance 网关的 Azure VNet。
 - VNet 上的运行 Windows Server 2016 的 DS5v2 VM。 VM 未加入域，基于已安装 AzureCT 的默认（没有优化或自定义）Azure 映像构建。
 - 所有测试都使用了 AzureCT Get-LinkPerformance 命令，而且 6 个测试运行都有 5 分钟的负载测试。 例如：

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - 每个测试的数据流都有从本地物理服务器（西雅图的 iPerf 客户端）流向 Azure VM（所列 Azure 区域中的 iPerf 服务器）的负载。
 - “延迟”列数据来自无负载测试（iPerf 未运行情况下的 TCP 延迟测试）。
 - “最大带宽”列数据来自窗口大小为 1 Mb 的 16 TCP 流负载测试。

![3][3]

### <a name="latencybandwidth-results"></a>延迟/带宽结果
>[!IMPORTANT]
> 这些数字仅供一般参考。 许多因素会影响延迟，尽管在一段时间内这些值通常是一致的，但 Azure 或服务提供商网络中的条件可在任何时间通过不同路径发送流量，因此会对延迟和带宽造成影响。 通常，这些更改的影响不会带来显著差异。
>
>

| ExpressRoute<br/>位置|Azure<br/>区域 | 估计<br/>距离 (km) | 延迟|1 会话<br/>带宽 | 最大值<br/>带宽 |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| 西雅图 | 美国西部 2        |    191 km |   5 ms | 262.0 Mbits/sec |  3.74 Gbits/sec |
| 西雅图 | 美国西部          |  1,094 km |  18 ms |  82.3 Mbits/sec |  3.70 Gbits/sec |
| 西雅图 | 美国中部       |  2,357 km |  40 ms |  38.8 Mbits/sec |  2.55 Gbits/sec |
| 西雅图 | 美国中南部 |  2,877 km |  51 ms |  30.6 Mbits/sec |  2.49 Gbits/sec |
| 西雅图 | 美国中北部 |  2,792 km |  55 ms |  27.7 Mbits/sec |  2.19 Gbits/sec |
| 西雅图 | 美国东部 2        |  3,769 km |  73 ms |  21.3 Mbits/sec |  1.79 Gbits/sec |
| 西雅图 | 美国东部          |  3,699 km |  74 ms |  21.1 Mbits/sec |  1.78 Gbits/sec |
| 西雅图 | 日本东部       |  7,705 km | 106 ms |  14.6 Mbits/sec |  1.22 Gbits/sec |
| 西雅图 | 英国南部         |  7,708 km | 146 ms |  10.6 Mbits/sec |   896 Mbits/sec |
| 西雅图 | 西欧      |  7,834 km | 153 ms |  10.2 Mbits/sec |   761 Mbits/sec |
| 西雅图 | 澳大利亚东部   | 12,484 km | 165 ms |   9.4 Mbits/sec |   794 Mbits/sec |
| 西雅图 | 东南亚   | 12,989 km | 170 ms |   9.2 Mbits/sec |   756 Mbits/sec |
| 西雅图 | 巴西南部*   | 10,930 km | 189 ms |   8.2 Mbits/sec |   699 Mbits/sec |
| 西雅图 | 印度南部      | 12,918 km | 202 ms |   7.7 Mbits/sec |   634 Mbits/sec |

\* 巴西的延迟是一个很好的例子，其中的直线距离明显不同于光纤运行距离。 预期延迟会在 160 毫秒左右，但实际是 189 毫秒。 延迟时间不同似乎表明某个位置出现了网络问题。 但事实是，这条光纤线路并不是直线通往巴西的。 因此，从西雅图到巴西的距离预计会多出 1,000 千米左右。

## <a name="next-steps"></a>后续步骤
1. 从 GitHub 中下载 Azure 连接工具包，地址为 [https://aka.ms/AzCT][ACT]
2. 按照[链接性能测试][Performance Doc]的说明进行操作

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure 网络组件"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute 故障排除"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "性能测试环境"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell 输出"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT