---
title: Windows 虚拟桌面 RDP 短路径（预览版）
titleSuffix: Azure
description: 如何设置适用于 Windows 虚拟桌面的 RDP 短路径（预览版）。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 295a46f6d1074ddf8422233ea3ccfa4d65c28fd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571596"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows 虚拟桌面 RDP 短路径（预览版）

> [!IMPORTANT]
> RDP 短路径目前以公共预览版提供。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

RDP 短路径是 Windows 虚拟桌面的一项功能，它可在远程桌面客户端和会话主机之间建立基于 UDP 的直接传输。 RDP 使用此传输实现远程桌面和 RemoteApp，同时提供更好的可靠性和一致的延迟。

## <a name="key-benefits"></a>主要优点

* RDP 短路径传输基于高效率的[通用速率控制协议 (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)。 URCP 通过主动监视网络状况来增强 UDP，并提供合理且完整的链接利用率。 URCP 以远程桌面需要的低延迟和低损耗级别运行。 它还通过动态地学习网络参数并提供具有速率控制机制的协议实现最佳性能。
* RDP 短路径在远程桌面客户端和会话主机之间建立直接连接。 直接连接可以减少对 Windows 虚拟桌面网关的依赖，提高连接可靠性，并增加每个用户会话可用的带宽。
* 消除额外的中继可减少往返时间，从而提高对延迟敏感的应用程序和输入法的用户体验。
* RDP 短路径支持通过差分服务代码点 (DSCP) 标记为 RDP 连接[配置服务质量 (QoS)](./rdp-quality-of-service-qos.md) 优先级。
* RDP 短路径传输允许通过为每个会话指定限制速率来[限制出站网络流量](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate)。

## <a name="connection-security"></a>连接安全性

RDP 短路径正在扩展 RDP 多传输功能。 它不会取代反向连接传输，而是对其进行补充。 所有初始会话中转都是通过 Windows 虚拟桌面基础结构管理的。

UDP 端口 3390 仅适用于通过反向连接传输进行身份验证的传入短路径流量。 RDP 短路径侦听器会忽略与该侦听器的所有连接尝试，除非它们匹配反向连接会话。

RDP 短路径借助会话主机证书在客户端和会话主机之间使用 TLS 连接。 默认情况下，用于 RDP 加密的证书是 OS 在部署过程中自行生成的。 如有需要，客户可以部署由企业证书颁发机构颁发的集中管理的证书。 有关证书配置的详细信息，请参阅 [Windows Server 文档](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)。

## <a name="rdp-shortpath-connection-sequence"></a>RDP 短路径连接顺序

安装[反向连接传输](./network-connectivity.md)之后，客户端和会话主机会建立 RDP 连接并协商多传输功能。 其他步骤如下所述：

1. 会话主机将其专用和公用 IPv4 和 IPv6 地址的列表发送到客户端。
2. 客户端启动后台线程，以与主机的某个 IP 地址直接建立基于 UDP 的并行传输。
3. 当客户端探测提供的 IP 地址时，它继续通过反向连接传输建立初始连接，以确保用户连接没有延迟。
4. 如果客户端有直接连接并且防火墙配置正确，客户端会建立与会话主机的安全 TLS 连接。
5. 建立短路径传输后，RDP 将包括远程图形、输入和设备重定向在内的所有动态虚拟通道 (DVC) 移到新传输。
6. 如果防火墙或网络拓扑阻止客户端建立直接 UDP 连接，RDP 通过反向连接传输继续。

下图简要概述了 RDP 短路径网络连接。

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP 短路径网络连接示意图" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>要求

若要支持 RDP 短路径，Windows 虚拟桌面客户端需要与会话主机的直接连接。 你可以使用以下技术之一实现直接连接：

* [ExpressRoute 专用对等互连](../expressroute/expressroute-circuit-peerings.md)
* [站点到站点 VPN（基于 IPsec）](../vpn-gateway/tutorial-site-to-site-portal.md)
* [点到站点 VPN（基于 IPsec）](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [公共 IP 地址分配](../virtual-network/virtual-network-public-ip-address.md)

如果要使用其他类型的 VPN 连接 Azure 虚拟网络，我们建议使用基于 UDP 的 VPN 以获得最好的结果。 尽管大多数基于 TCP 的 VPN 解决方案封装了所有 IP 数据包（包括 UDP），但它们增加了继承的会降低 RDP 性能的 TCP 拥塞控制开销。

直接连接意味着防火墙不会阻止 UDP 端口 3390，并且客户端可直接连接会话主机。

## <a name="enabling-rdp-shortpath-preview"></a>启用 RDP 短路径预览版

若要参与 RDP 短路径预览，你需要在会话主机上启用 RDP 短路径侦听器。 可以对环境中使用的任意数量的会话主机启用 RDP 短路径。 无需对池中的所有主机都启用 RDP 短路径。
若要启用短路径侦听器，需要配置以下注册表值：

> [!WARNING]
> 如果没有使用注册表编辑器或其他方法正确修改注册表，则可能会出现严重问题。 这些问题可能要求重新安装操作系统。 Microsoft 无法保证这些问题可得到解决。 修改注册表的风险自负。

1. 在会话主机上，启动 Regedit.exe，然后导航到以下位置：

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. 新建名为 fUseUdpPortRedirector 的 DWORD 值，并将其设置为 1（十进制）  
3. 新建名为 UdpPortNumber 的 DWORD 值，并将其设置为 3390（十进制）  
4. 退出注册表编辑器。
5. 重启会话主机

你还可以通过在提升的 PowerShell 窗口中运行以下 cmdlet 来设置上述注册表值：

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

还可以使用 PowerShell 来配置组策略

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>配置高级安全 Windows Defender 防火墙

若要允许 RDP 短路径的入站网络流量，请使用组策略管理 MMC 管理单元中的“高级安全 Windows Defender 防火墙”节点创建防火墙规则。

1. 打开[高级安全 Windows 防火墙](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)的组策略管理控制台。
2. 在导航窗格中，选择“入站规则”。
3. 选择“操作”，然后选择“新建规则” 。
4. 在“新建入站规则向导”的“规则类型”页中，选择“自定义”，然后选择“下一步”  。
5. 在“程序”页中，选择“此程序路径”，键入“%SystemRoot%\system32\svchost.exe”，然后选择“下一步”  。
6. 在“协议和端口”页中，选择 UDP 协议类型。 在“本地端口”中，选择“特定端口”并键入 3390。
7. 在“作用域”页面上，你可以指定该规则仅适用于在此页面上输入的 IP 地址中进出的网络流量。 根据设计需要进行配置，然后选择“下一步”。
8. 在“操作”页中，选择“允许连接”，然后选择“下一步”  。
9. 在“配置文件”页中，选择应用此规则的网络位置类型，然后选择“下一步” 。
10. 在“名称”页中，键入你的规则的名称和说明，然后选择“完成” 。

可以验证新规则是否与以下屏幕截图匹配：:::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="RDP 短路径网络连接的防火墙配置的“常规”选项卡的屏幕截图" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="RDP 短路径网络连接的防火墙配置的“程序和服务”选项卡的屏幕截图" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="RDP 短路径网络连接的防火墙配置的“协议和端口”选项卡的屏幕截图" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

还可以使用 PowerShell 配置 Windows 防火墙：

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>使用 PowerShell 配置 Windows Defender 防火墙

你还可以使用 PowerShell 来配置组策略

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>配置 Azure 网络安全组

如果要允许跨网络安全边界访问 RDP 短路径侦听器，你需要将 Azure 网络安全组配置为允许入站 UDP 端口 3390。
按照[网络安全组文档](../virtual-machines/windows/nsg-quickstart-portal.md)所述，创建允许带以下参数的流量的入站安全规则：

* 源  -  任何源或客户端所在的 IP 范围 
* 源端口范围  -  \* 
* 目标  -  任何目标 
* 目标端口范围  -  3390 
* 协议  -  UDP 
* 操作  -  允许 
* 可以更改“优先级”。 优先级会影响应用规则的顺序：数值越小，越先应用规则。
* 名称 - RDP 短路径 

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>对特定子网禁用 RDP 短路径

如果需要阻止特定子网使用 RDP 短路径传输，可以配置指定源 IP 范围的额外网络安全组。

## <a name="verifying-the-connectivity"></a>验证连接

### <a name="using-connection-information-dialog"></a>使用“连接信息”对话框

若要验证连接是否正在使用 RDP 短路径，请单击连接工具栏中的天线图标，以打开“连接信息”对话框。

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="远程桌面连接工具栏的图像":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="远程桌面连接信息对话框的图像":::

### <a name="using-event-logs"></a>使用事件日志

验证会话是否正在使用 RDP 短路径传输：

1. 使用 Windows 虚拟桌面客户端连接到 VM 的桌面。
2. 启动“事件查看器”并导航到以下节点：“应用程序和服务日志”>“Microsoft”>“Windows”>“RemoteDesktopServices-RdpCoreCDV”>“Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational”
3. 若要确定是否使用了 RDP 短路径传输，请查找事件 ID 131。

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>使用 Log Analytics 验证短路径连接

如果正在使用 [Azure Log Analytics](./diagnostics-log-analytics.md)，则可以通过查询 [WVDConnections 表](/azure/azure-monitor/reference/tables/wvdconnections)来监视连接。 名为 UdpUse 的列指示 Windows 虚拟桌面 RDP 堆栈是否对当前用户连接使用 UDP 协议。
可能的值包括：

* 0 - 用户连接未使用 RDP 短路径
* 1 - 用户连接使用的是 RDP 短路径
  
可通过下面的查询列表查看连接信息。 可以在 [Log Analytics 查询编辑器](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)中运行此查询。 对于每个查询，请将 `userupn` 替换为要查找的用户的 UPN。

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>疑难解答

### <a name="verify-shortpath-listener"></a>验证短路径侦听器

若要验证是否已启用 UDP 侦听器，请对会话主机使用以下 PowerShell 命令：

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

如果已启用，你会看到如下所示的输出

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

如果出现冲突，可以使用以下命令确定占用该端口的进程

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>禁用 RDP 短路径

在某些情况下，可能需要禁用 RDP 短路径传输。 可以通过使用组策略来禁用 RDP 短路径。

### <a name="disabling-rdp-shortpath-on-the-client"></a>对客户端禁用 RDP 短路径

若要对特定客户端禁用 RDP 短路径，可使用以下组策略禁用 UDP 支持：

1. 对客户端运行 gpedit.msc。
2. 导航到“计算机配置”->“管理模板”->“Windows 组件”->“远程桌面服务”->“远程桌面连接客户端”。
3. 将“对客户端关闭 UDP”设置设为“已启用” 

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>对会话主机禁用 RDP 短路径

若要对特定会话主机禁用 RDP 短路径，可使用以下组策略禁用 UDP 支持：

1. 在会话主机上运行 gpedit.msc。
2. 导航到“计算机配置”->“管理模板”->“Windows 组件”->“远程桌面服务”->“远程桌面连接主机”>“连接”。
3. 将“选择 RDP 传输协议”设置设为“仅 TCP” 

## <a name="public-preview-feedback"></a>公共预览版反馈

我们想要倾听你对此公共预览版的体验反馈！
* 如有问题、请求、意见和其他反馈，请[使用此反馈表单](https://aka.ms/RDPShortpathFeedback)。

## <a name="next-steps"></a>后续步骤

* 若要了解 Windows 虚拟桌面网络连接，请参阅[了解 Windows 虚拟桌面网络连接](network-connectivity.md)。
* 若要开始使用 Windows 虚拟桌面的服务质量 (QoS)，请参阅[实现 Windows 虚拟桌面的服务质量 (QoS)](rdp-quality-of-service-qos.md)。