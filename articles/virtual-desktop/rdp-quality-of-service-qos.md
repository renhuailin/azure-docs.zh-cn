---
title: '为 Windows 虚拟桌面 (预览实现服务质量 (QoS) ) '
titleSuffix: Azure
description: 如何为 Windows 虚拟桌面设置 QoS (预览) 。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639191"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>为 Windows 虚拟桌面 (预览实现服务质量 (QoS) ) 

> [!IMPORTANT]
> Windows 虚拟桌面的服务质量 (QoS) 策略支持目前为公共预览版。
> 此预览版在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[RDP Shortpath](./shortpath.md) 在远程桌面客户端和会话主机之间提供基于 UDP 的直接传输。 RDP Shortpath 为 RDP 数据启用服务质量 (QoS) 策略的配置。
Windows 虚拟桌面中的 QoS 允许实时 RDP 流量，这对网络延迟很敏感，使其在不太敏感的流量之前 "在行中剪切"。 此类不太敏感的流量的示例是下载新的应用程序，在这种情况下，下载额外的应用程序并不是一个大的处理。 QoS 使用 Windows 组策略对象来识别和标记实时流中的所有数据包，并帮助您的网络为 RDP 流量分配一个专用的带宽部分。

如果你支持一大组用户遇到本文中所述的任何问题，则可能需要实现 QoS。 只有少量用户的小型企业可能不需要 QoS，但在此情况下，它应该非常有用。

如果没有某种形式的 QoS，可能会出现以下问题：

* 抖动-采用不同速率的 RDP 数据包，这可能会导致视觉和音频故障
* 数据包丢失–丢弃数据包，这将导致需要额外时间的重新传输
* 延迟往返时间 (RTT) – RDP 数据包需要很长时间才能到达其目标，这会导致远程应用程序的输入和反应之间出现明显的延迟。

解决这些问题的最简单的方法是将数据连接的大小从内部和传出增加到 internet。 由于这通常是成本不高的，因此 QoS 提供一种方法来管理您所拥有的资源，而不是更有效地增加带宽。 为了解决质量问题，我们建议你首先使用 QoS，并仅在必要时添加带宽。

为了使 QoS 有效，必须在整个组织内应用一致的 QoS 设置。 路径中无法支持 QoS 优先级的任何部分都可能会降低高质量的 RDP 会话。

## <a name="introduction-to-qos-queues"></a>QoS 队列简介

为了提供 QoS，网络设备必须有一种方法来分类流量，并且必须能够将 RDP 与其他网络流量区分开来。

网络流量进入路由器时，会将流量放入队列中。 如果未配置 QoS 策略，则仅有一个队列，所有数据将被视为具有相同优先级的第一项。 这意味着，RDP 流量可能会停滞在通信量之后，这几个额外的毫秒延迟不会造成问题。

实现 QoS 时，可以使用几个拥塞管理功能中的一种来定义多个队列，如 Cisco 的优先级队列和 [基于类的加权公平队列 (CBWFQ) ](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) 和拥塞回避功能，如 [加权随机检测 (WRED) ](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html)。

一个简单的类比是，QoS 在数据网络中创建虚拟 "合用车道"。 因此，某些类型的数据永远不会出现延迟。 创建这些车道后，可以调整它们的相对大小，并更有效地管理所拥有的连接带宽，同时还为组织的用户提供业务级体验。

## <a name="qos-implementation-checklist"></a>QoS 实现清单

在高级别中，执行以下操作来实现 QoS：

1. [请确保网络已准备就绪](#make-sure-your-network-is-ready)
2. [确保已启用 RDP Shortpath](./shortpath.md) -对于反向连接传输，不支持 QoS 策略
3. 在会话主机上[实现 DSCP 标记的插入](#insert-dscp-markers)

准备实现 QoS 时，请记住以下准则：

* 最短路径到会话主机
* 不建议在代理或数据包检查设备等之间进行任何障碍物

## <a name="make-sure-your-network-is-ready"></a>请确保网络已准备就绪

如果你正在考虑 QoS 实现，则应该已确定带宽要求和其他 [网络要求](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)。
  
跨网络的流量拥塞会显著影响媒体质量。 缺少带宽会导致性能下降，并导致用户体验不佳。 随着 Windows 虚拟桌面的采用和使用量的发展，请使用 [Log Analytics](./diagnostics-log-analytics.md) 来确定问题，然后使用 QoS 和选择性的带宽增加进行调整。

### <a name="vpn-considerations"></a>VPN 注意事项

QoS 仅在客户端和会话主机之间的所有链接上实现时按预期方式工作。 如果在内部网络上使用 QoS 并且用户从远程位置登录，则只能在内部托管网络内设置优先级。 尽管远程位置可以通过实现 (VPN) 的虚拟专用网络来接收托管连接，但 VPN 原本会增加数据包开销，并在实时流量中产生延迟。

在具有跨洲的托管链接的全球组织中，强烈建议使用 QoS，因为与 LAN 相比，这些链接的带宽受到限制。

## <a name="insert-dscp-markers"></a>插入 DSCP 标记

你可以使用组策略对象 (GPO) 实现 QoS，以指示会话主机在 IP 数据包标头中插入一个 DSCP 标记，将其标识为特定类型的流量。 可以将路由器和其他网络设备配置为识别这些标记，并将流量放在一个单独的更高优先级的队列中。

您可以将 DSCP 标记与 "邮资" 标记进行比较，以向邮政标志表明传递的紧急程度，以及如何最好地对其进行排序以实现快速交付。 将网络配置为为 RDP 流设置优先级后，丢失的数据包和后期数据包应该会显著降低。

一旦所有网络设备使用相同的分类、标记和优先级，就可以减少或消除延迟、丢弃数据包和抖动。 从 RDP 的角度来看，基本配置步骤是数据包的分类和标记。 但是，若要成功完成端到端 QoS，还需要仔细地将 RDP 配置与基础网络配置进行对齐。
DSCP 值会告知相应配置的网络为数据包或流指定的优先级。

建议使用 DSCP 值46，将其映射到 **加急转发 (EF)** DSCP 类。

### <a name="implement-qos-on-session-host-using-group-policy"></a>使用组策略在会话主机上实施 QoS

可以在组策略内使用基于策略的服务质量 (QoS) 来设置预定义的 DSCP 值。

若要为已加入域的会话主机创建 QoS 策略，请首先登录到安装了组策略管理的计算机。 打开组策略管理 (选择 "开始"，指向 "管理工具"，然后选择 "组策略管理) "，然后完成以下步骤：

1. 在组策略管理 "中，找到要在其中创建新策略的容器。 例如，如果所有会话主机计算机都位于名为 **"session 主机"** 的 OU 中，则应在 "主持 Ou" 会话中创建新策略。

2. 右键单击相应的容器，然后选择 " **在这个域中创建 GPO 并在此处链接"** 。

3. 在 " **新建 GPO** " 对话框中，在 " **名称** " 框中键入新组策略对象的名称，然后选择 **"确定"** 。

4. 右键单击新创建的策略，然后选择 " **编辑** "。

5. 在组策略管理编辑器中，展开 " **计算机配置** "，展开 " **Windows 设置** "，右键单击 " **基于策略的 QoS** "，然后选择 " **创建新策略** "。

6. 在 " **基于策略的 QoS** " 对话框中，在 "开始" 页上的 " **名称** " 框中键入新策略的名称。 选择 " **指定 DSCP 值** " 并将值设置为 **46** 。 保留 "指定未选择的 **出站中止速率** "，然后选择 " **下一步** "。

7. 在下一页上，选择 " **仅限具有此可执行文件名称的应用程序** " 并输入 **svchost.exe** 的名称，然后选择 " **下一步** "。 此设置指示策略仅确定来自远程桌面服务的匹配流量的优先级。

8. 在第三页上，确保选择了 " **所有源 ip 地址** 和 **目标 ip 地址** "，然后选择 " **下一步** "。 这两个设置可确保无论哪个计算机 (IP 地址) 发送数据包，以及哪台计算机 (IP 地址) 将接收数据包，都将对数据包进行管理。

9. 在第四页上，从 " **选择此 QoS 策略应用** 于的协议" 下拉列表中选择 " **UDP** "。

10. 在 " **指定源端口号** " 标题下，选择 " **从此源端口或范围** "。 在随附的文本框中，键入 **3390** 。 选择“完成”  。

在会话主机计算机上刷新组策略之前，你创建的新策略将不会生效。 尽管组策略会定期定期进行刷新，但你可以通过以下步骤强制立即刷新：

1. 在要刷新组策略的每个会话主机上，以管理员身份打开命令提示符 (" *以管理员身份运行* ) "。

1. 在命令提示符下，输入

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>使用 PowerShell 在会话主机上实施 QoS

可以使用以下 PowerShell cmdlet 为 RDP Shortpath 设置 QoS：

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>相关文章

* [服务质量 (QoS) 策略](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Windows 虚拟桌面的带宽要求，请参阅 [了解远程桌面协议 (RDP) Windows 虚拟桌面的带宽要求](rdp-bandwidth.md)。
* 若要了解 Windows 虚拟桌面网络连接，请参阅 [了解 Windows 虚拟桌面网络连接](network-connectivity.md)。
