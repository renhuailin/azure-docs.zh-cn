---
title: 实现适用于 Azure 虚拟桌面的服务质量 (QoS)（预览版）
titleSuffix: Azure
description: 如何设置适用于 Azure 虚拟桌面的 QoS（预览版）。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c90811009a38db0874589dc828059277b9ae285c
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753029"
---
# <a name="implement-quality-of-service-qos-for-azure-virtual-desktop-preview"></a>实现适用于 Azure 虚拟桌面的服务质量 (QoS)（预览版）

> [!IMPORTANT]
> 适用于 Azure 虚拟桌面的服务质量 (QoS) 策略支持目前提供公共预览版。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[RDP 短路径](./shortpath.md)可在远程桌面客户端和会话主机之间建立基于 UDP 的直接传输。 RDP 短路径支持对 RDP 数据启用服务质量 (QoS) 策略的配置。
通过使用 Azure 虚拟桌面中的 QoS，对网络延迟敏感的实时 RDP 流量可以“插队”到不那么敏感的流量前面。 此类不太敏感的流量的示例会下载一个新应用，在这种情况下，下载额外的应用并不是什么大问题。 QoS 使用 Windows 组策略对象来识别并标记实时流中的所有数据包，帮助网络为 RDP 流量分配一部分专用带宽。

如果你支持一大批用户遇到本文中所述的任何问题，则可能需要实现 QoS。 只有少量用户的小型企业可能不需要 QoS，即便这样，实现 QoS 也是有用的。

如果没有某种形式的 QoS，可能会出现以下问题：

* 抖动 - RDP 数据包以不同速率到达，这可能会导致视频和音频出现小故障
* 数据包丢失 - 数据包被删除，这会导致需要额外时间重新进行传输
* 延迟往返时间 (RTT) - RDP 数据包需要很长时间才能到达其目的地，这会导致远程应用程序的输入和反应之间出现明显延迟。

解决这些问题最简单的方法是内外同时增加与 Internet 的数据连接大小。 由于修复这些问题通常成本高昂，因此 QoS 提供了一种方法来管理你手头的资源，而不是更有效地增加带宽。 为了解决质量问题，我们建议你首先使用 QoS，然后只在必要时增加带宽。

要使 QoS 有效，必须在整个组织内应用一致的 QoS 设置。 路径中无法支持 QoS 优先级的任何部分都可能会降低 RDP 会话的质量。

## <a name="introduction-to-qos-queues"></a>QoS 队列简介

若要提供 QoS，网络设备必须能够对流量进行分类，并且必须能够将 RDP 与其他网络流量区分开来。

网络流量进入路由器后就会加入到队列中。 如果未配置 QoS 策略，则仅有一个队列，并且所有数据都会被视为具有相同优先级，按照先进先出的原则进行处理。 这意味着，如果多延迟几毫秒也没有关系，RDP 流量可能会卡在其他流量后面。

实现 QoS 后，可使用若干拥塞管理功能中的一种来定义多个队列，例如 Cisco 的优先级队列和[基于类的加权公平队列 (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) 以及拥塞避免功能，例如[加权随机早期检测 (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html)。

打一个简单的比方，QoS 就好比在数据网络中创建虚拟的“拼车道”。 因此，某些类型的数据从不或者很少会出现延迟。 创建这些通道后，可以调整它们的相对大小，更有效地管理所拥有的连接带宽，同时为组织的用户提供企业级体验。

## <a name="qos-implementation-checklist"></a>QoS 实现清单

概括地说，实现 QoS 需要执行以下操作：

1. [确保网络已准备就绪](#make-sure-your-network-is-ready)
2. [确保已启用 RDP 短路径](./shortpath.md) - 反向连接传输不支持 QoS 策略
3. 在会话主机上[实现 DSCP 标记插入](#insert-dscp-markers)

准备实现 QoS 时，请记住以下准则：

* 到会话主机的路径越短越好
* 不建议在代理或数据包检查设备等之间设立任何障碍物

## <a name="make-sure-your-network-is-ready"></a>确保网络已准备就绪

如果你要考虑实现 QoS，则应已确定带宽要求和其他[网络要求](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)。
  
跨网络的流量拥塞会显著影响媒体质量。 缺少带宽会导致性能下降，使用户体验不佳。 随着 Azure 虚拟桌面的采用和使用的增长，请使用 [Log Analytics](./diagnostics-log-analytics.md) 确定问题所在，然后通过 QoS 和选择性增加带宽进行调整。

### <a name="vpn-considerations"></a>VPN 注意事项

QoS 仅在客户端和会话主机之间的所有链接上实现时才会按预期运行。 如果对内部网络使用 QoS 并且用户从远程位置进行登录，则只能在内部托管网络中设置优先级。 尽管远程位置可以通过实现虚拟网络 (VPN) 来接收托管连接，但 VPN 本身就会增加数据包开销并使实时流量出现延迟。

对于托管链接横跨几大洲的全球性组织，我们强烈建议实现 QoS，因为与 LAN 相比，用于这些链接的带宽非常有限。

## <a name="insert-dscp-markers"></a>插入 DSCP 标记

可使用组策略对象 (GPO) 实现 QoS，指示会话主机在 IP 数据包标头中插入一个 DSCP 标记，并将其标识为特定类型的流量。 可配置路由器和其他网络设备，以识别这些标记并将流量放置在一个单独的具有更高优先级的队列中。

可将 DSCP 标记比作邮票，用于向邮递工作人员表明递送的紧急程度，以及如何通过最佳方法对其进行排序以实现快速递送。 配置网络以向 RDP 流分配优先级后，丢失的数据包和延迟的数据包应会显著减少。

如果所有网络设备都使用相同的分类、标记和优先级，就可以减少或消除延迟、数据包删除和抖动的情况。 从 RDP 的角度来看，对数据包进行分类并添加标记是基本的配置步骤。 但是，若要成功实现端到端 QoS，还需要仔细将 RDP 配置与基础网络配置对齐。
DSCP 值会告知相应的已配置网络应为数据包或流分配的优先级。

建议使用 DSCP 值 46，它可映射到加速转发 (EF) DSCP 类。

### <a name="implement-qos-on-session-host-using-group-policy"></a>使用组策略在会话主机上实现 QoS

可在组策略中使用基于策略的服务质量 (QoS) 来设置预定义的 DSCP 值。

若要为已加入域的会话主机创建 QoS 策略，请先登录到装有组策略管理的计算机。 打开组策略管理（选择“开始”，指向“管理工具”，并选择“组策略管理”），然后完成以下步骤：

1. 在组策略管理中，找到要在其中创建新策略的容器。 例如，如果你的所有会话主机计算机都位于名为“会话主机”的 OU 中，则应在“会话主机”OU 中创建新策略。

2. 右键单击相应的容器，然后选择“在此域中创建 GPO 并将其链接到此处”。

3. 在“新建 GPO”对话框中的“名称”框内键入新组策略对象的名称，然后选择“确定”  。

4. 右键单击新建的策略，然后选择“编辑”。

5. 在组策略管理编辑器中，依次展开“计算机配置”、“Windows 设置”，右键单击“基于策略的 QoS”，然后选择“创建新策略”   。

6. 在打开的页上，在“基于策略的 QoS”对话框中的“名称”框内键入新策略的名称 。 选择“指定 DSCP 值”，然后将值设为“46” 。 使“指定出站限制速率”保持未选择状态，然后选择“下一步” 。

7. 在下一页上，选择“仅限具有此可执行文件名称的应用程序”，输入名称 svchost.exe，然后选择“下一步”  。 此设置将指示策略仅优先处理来自远程桌面服务的匹配流量。

8. 在第三页上，确保已同时选择“任何源 IP 地址”和“任何目标 IP 地址”，然后选择“下一步”  。 这两个设置确保无论数据包由哪个计算机（IP 地址）发送、由哪个计算机（IP 地址）接收，都会对数据包进行管理。

9. 在第四页上，从“选择要将此 QoS 策略应用到的协议”下拉列表中选择“UDP” 。

10. 在“指定源端口号”标题下，选择“从此源端口或范围” 。 在附带的文本框中，键入 3390。 选择“完成”  。

只有在会话主机计算机上刷新组策略之后，创建的新策略才会生效。 尽管组策略会定期自行刷新，但你可以通过以下步骤强制立即刷新：

1. 在要刷新组策略的每个会话主机上，以管理员身份打开命令提示符（“以管理员身份运行”）。

1. 在命令提示符下，输入：

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>在会话主机上使用 PowerShell 实现 QoS

可使用以下 PowerShell cmdlet 为 RDP 短路径设置 QoS：

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>相关文章

* [服务质量 (QoS) 策略](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure 虚拟桌面的带宽要求，请参阅[了解 Azure 虚拟桌面的远程桌面协议 (RDP) 带宽要求](rdp-bandwidth.md)。
* 若要了解 Azure 虚拟桌面网络连接，请参阅[了解 Azure 虚拟桌面网络连接](network-connectivity.md)。
