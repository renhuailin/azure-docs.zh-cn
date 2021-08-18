---
title: 代理服务器准则 Azure 虚拟桌面 - Azure
description: 在 Azure 虚拟桌面部署中使用代理服务器的一些准则和建议。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
ms.reviewer: denisgun
manager: femila
ms.openlocfilehash: 41ee917602ed67af40f3244ad7215cae4d3a93c8
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113032446"
---
# <a name="proxy-server-guidelines-for-azure-virtual-desktop"></a>Azure 虚拟桌面的代理服务器准则

本文介绍如何在 Azure 虚拟桌面上使用代理服务器。 本文中的建议仅适用于 Azure 虚拟桌面基础结构、客户端和会话主机代理之间的连接。 本文内容不涉及适用于 Office、Windows 10、FSLogix 或其他 Microsoft 应用程序的网络连接。

## <a name="what-are-proxy-servers"></a>什么是代理服务器？

我们建议绕过 Azure 虚拟桌面流量的代理。 由于流量已加密，因此代理不会提高 Azure 虚拟桌面的安全性。 若要了解有关连接安全性的详细信息，请参阅[连接安全性](network-connectivity.md#connection-security)。 

大多数代理服务器都不是专为支持长期运行的 WebSocket 连接而设计，因此可能会影响连接稳定性。 由于 Azure 虚拟桌面使用多个长期连接，因此代理服务器的可伸缩性还会导致出现问题。 如果一定要使用代理服务器，则必须使用大小合适的服务器来运行这些连接。

如果代理服务器的地理位置距离主机很远，则此距离将导致用户连接出现更多延迟。 更多延迟意味着连接时间变慢且用户体验更糟糕，尤其是遇到需要图形、音频或与输入设备建立低延迟交互的方案。 如果你必须使用代理服务器，请记住，你需要将此服务器与 Azure 虚拟桌面代理和客户端置于同一地理位置。

如果你将代理服务器配置为 Azure 虚拟桌面流量的唯一通过路径，则远程桌面协议 (RDP) 数据将强制通过传输控制协议 (TCP)，而不是用户数据报协议 (UDP)。 此举将会降低远程连接的视觉质量并减弱响应能力。

总之，我们不建议在 Azure 虚拟桌面上使用代理服务器，因为它们会导致延迟降低和数据包丢失等与性能相关的问题。 

## <a name="bypassing-a-proxy-server"></a>绕过代理服务器

如果组织的网络和安全策略需要利用代理服务器通过 Web 流量，则可以将环境配置为绕过 Azure 虚拟桌面连接，但仍通过代理服务器路由流量。 但是，每个组织的策略都是唯一的，因此某些方法的部署效果可能比其他方法更好。 下方提供一些配置方法，你可以通过这些方法来试着防止环境中的性能下降及可靠性降低：

- Azure 防火墙上的 Azure 服务标记
- 使用代理自动配置 (.PAC) 文件绕过代理服务器
- 本地代理配置中的绕过列表 
- 使用代理服务器完成每用户配置 
- 使用 RDP 短路径完成 RDP 连接，同时保持服务流量通过代理 

## <a name="recommendations-for-using-proxy-servers"></a>代理服务器的使用建议

有些组织会要求所有用户流量都通过代理服务器，以完成跟踪或数据包检查。 我们将在本部分介绍在这些情况下配置环境的建议方法。

### <a name="use-proxy-servers-in-the-same-azure-geography"></a>在同一 Azure 地域中使用代理服务器

使用代理服务器时，它会处理与 Azure 虚拟桌面基础结构的所有通信，并对最近的 Azure Front Door 执行 DNS 解析和选播路由。 在整个 Azure 地域中，如果代理服务器使用距离较远或呈分布状，则地理解析的准确性将会降低。 地理解析的准确性降低意味着连接将会路由到距离更远的 Azure 虚拟桌面群集。 若要避免这一问题，应仅使用地理位置靠近 Azure 虚拟桌面群集的代理服务器。

### <a name="use-rdp-shortpath-for-desktop-connectivity"></a>使用桌面连接专用的 RDP 短路径

启用 RDP 短路径时，RDP 数据会绕过代理服务器（如有可能）。 使用 UDP 传输时，绕过代理服务器可确保达到最佳路由效果。 其他 Azure 虚拟桌面流量（如代理、编排和诊断）仍将通过代理服务器。 

### <a name="dont-use-ssl-termination-on-the-proxy-server"></a>不要在代理服务器上使用 SSL 终止

安全套接字层 (SSL) 终止会将 Azure 虚拟桌面组件的安全证书替换为代理服务器生成的证书。 此代理服务器功能会为代理服务器上的 HTTPS 流量启用数据包检查。 但是，数据包检查还将延长服务响应时间，从而导致用户的登录时间变长。 如果是反向连接方案，则不需要使用 RDP 流量数据包检查，因为反向连接 RDP 流量是二进制的，并且使用额外的加密级别。

如果将代理服务器配置为使用 SSL 检查，请记住，你将无法在 SSL 检查发生更改后将服务器还原至其原始状态。 如果启用 SSL 检查后，Azure 虚拟桌面环境中的某些功能停止运作，则必须禁用 SSL 检查，然后重试，最后再打开支持案例。 SSL 检查还可能会导致 Azure 虚拟桌面代理停止工作，因为它会干扰代理和服务之间的可信连接。

### <a name="dont-use-proxy-servers-that-need-authentication"></a>不要使用需要验证身份的代理服务器

会话主机上的 Azure 虚拟桌面组件是在其操作系统的上下文中运行，因此它们不支持需要验证身份的代理服务器。 如果代理服务器要求验证身份，则连接将会失败。

### <a name="plan-for-the-proxy-server-network-capacity"></a>代理服务器网络容量规划

代理服务器设有容量限制。 与常规 HTTP 流量不同，RDP 流量的双向连接可长期运行且需消耗大量带宽。 在设置代理服务器之前，请先咨询代理服务器供应商，了解相关服务器的具体吞吐量。 此外，请确保问清楚供应商一次能够运行的代理会话数量。 部署代理服务器后，请仔细监视其资源使用情况，以应对 Azure 虚拟桌面流量的瓶颈。

### <a name="proxy-servers-for-windows-7-session-hosts"></a>适用于 Windows 7 会话主机的代理服务器

在 Windows 7 上运行的会话主机不支持通过代理服务器连接来反向连接 RDP 数据。 如果会话主机无法直接连接到 Azure 虚拟桌面网关，则连接将不起作用。

### <a name="proxy-servers-and--teams-optimization"></a>代理服务器和团队优化

Azure 虚拟桌面不支持用于团队优化的代理服务器。

## <a name="session-host-configuration-recommendations"></a>会话主机配置建议

若要配置会话主机级别代理服务器，则需要启用全系统代理。 请记住，全系统配置会影响在会话主机上运行的所有 OS 组件和应用程序。 以下部分是有关配置全系统代理的建议。
 
### <a name="use-the-web-proxy-auto-discovery-wpad-protocol"></a>使用 Web 代理自动发现 (WPAD) 协议

Azure 虚拟桌面代理会使用 Web 代理自动发现 (WPAD) 协议自动尝试查找网络上的代理服务器。 在尝试查找位置的过程中，代理会在域名服务器 (DNS) 中搜索名为 domainsuffix 的文件。 如果代理在 DNS 中找到该文件，则会对名为 wpad.dat 的文件发出 HTTP 请求。 而响应则会成为选择出站代理服务器的代理配置脚本。

若要将网络配置为对 WPAD 使用 DNS 解析，请遵循[自动检测 Internet Explorer 11 设置](/internet-explorer/ie11-deploy-guide/auto-detect-settings-for-ie11)中的操作指示。 请遵循 [Set-DnsServerGlobalQueryBlockList](/powershell/module/dnsserver/set-dnsserverglobalqueryblocklist?view=windowsserver2019-ps&preserve-view=true) 中的操作说明，确保 DNS 服务器全局查询阻止列表允许执行 WPAD 解析。

### <a name="manually-set-a-device-wide-internet-explorer-proxy"></a>手动设置设备范围的 Internet Explorer 代理

你可以使用[网络代理 CSP](/windows/client-management/mdm/networkproxy-csp) 设置适用于所有交互性、LocalSystem 和 NetworkService 用户的设备范围代理或代理自动配置 (.PAC) 文件。 

你还可以通过运行以下 bitsadmin 命令为本地系统帐户配置代理服务器，如以下示例中所示： 

```console
bitsadmin /util /setieproxy LOCALSYSTEM AUTOSCRIPT http://server/proxy.pac 
```

## <a name="client-side-proxy-support"></a>客户端代理支持

Azure 虚拟桌面客户端支持使用系统设置或[网络代理 CSP](/windows/client-management/mdm/networkproxy-csp) 进行配置的代理服务器。

### <a name="support-for-clients-running-on-windows-7"></a>支持在 Windows 7 上运行的客户端

在 Windows 7 上运行的客户端不支持通过代理服务器连接来反向连接 RDP 数据。 如果客户端无法直接连接到 Azure 虚拟桌面网关，则连接将不起作用。

### <a name="azure-virtual-desktop-client-support"></a>Azure 虚拟桌面客户端支持

下表显示的是支持代理服务器的 Azure 虚拟桌面客户端：

| 客户端名称 | 代理服务器支持 |
|---|---|
| Windows 桌面 | 是 |
| Web 客户端 | 是 |
| Android | 否 |
| iOS | 是 |
| macOS | 是 |
| Windows 应用商店 | 是 |

有关 Linux 上基于瘦客户端的代理支持的详细信息，请参阅[瘦客户端支持](./user-documentation/linux-overview.md)。

## <a name="support-limitations"></a>支持限制

许多第三方服务和应用程序都可充当代理服务器。 这些第三方服务包括分布式下一代防火墙、Web 安全系统和基本代理服务器。 我们无法保证每个配置都与 Azure 虚拟桌面兼容。 Microsoft 仅对通过代理服务器建立的连接提供有限支持。 如果你在使用代理服务器时遇到连接问题，Microsoft 支持建议你配置代理绕过，然后尝试重新生成该问题。

## <a name="next-steps"></a>后续步骤

有关保持 Azure 虚拟桌面部署安全的详细信息，请查看我们的[安全指南](security-guide.md)。
