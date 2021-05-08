---
title: 使用 Azure 防火墙保护 Windows 虚拟桌面
description: 了解如何使用 Azure 防火墙保护 Windows 虚拟桌面部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: d5320f44aa5d922cea852ab09e5141fad277e2b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566020"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>使用 Azure 防火墙保护 Windows 虚拟桌面部署

Windows 虚拟桌面是在 Azure 运行的桌面和应用虚拟化服务。 当最终用户连接到 Windows 虚拟桌面环境时，其会话由主机池运行。 主机池是注册到 Windows 虚拟桌面作为会话主机的 Azure 虚拟机的集合。 这些虚拟机在虚拟网络中运行，并受虚拟网络安全控制的限制。 它们需要对 Windows 虚拟桌面服务的出站 Internet 访问权限才能正常运行，并且可能还需要最终用户的出站 Internet 访问权限。 Azure 防火墙可以帮助你锁定环境并筛选出站流量。

[ ![Windows 虚拟桌面体系结构](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

请参阅本文中的指南，使用 Azure 防火墙为 Windows 虚拟桌面主机池提供额外保护。

## <a name="prerequisites"></a>先决条件


 - 已部署的 Windows 虚拟桌面环境和主机池。

   有关详细信息，请参阅[教程：使用 Azure 市场创建主机池](../virtual-desktop/create-host-pools-azure-marketplace.md)和[使用 Azure 资源管理器模板创建主机池](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md)。

如要了解有关 Windows 虚拟桌面环境，请参阅[Windows 虚拟桌面环境](../virtual-desktop/environment-setup.md)。

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>主机池对 Windows 虚拟桌面的出站访问权限

为 Windows 虚拟桌面创建的 Azure 虚拟机必须有权访问多个完全限定域名 (Fqdn) 才能正常工作。 Azure 防火墙提供 Windows 虚拟桌面 FQDN 标记以简化此配置。 使用以下步骤允许出站 Windows 虚拟桌面平台流量：

- 部署 Azure 防火墙并配置 Windows 虚拟桌面主机池子网“用户定义的路由”(UDR)，以通过 Azure 防火墙路由所有流量。 默认路由现在指向防火墙。
- 创建应用程序规则集合，并启用 *WindowsVirtualDesktop* FQDN 标记的规则。 源 IP 地址范围为主机池虚拟网络，协议为 **https**，目标为 **WindowsVirtualDesktop**。

- Windows 虚拟桌面主机池所需的存储和服务总线帐户集是特定于部署的，因此尚未在 WindowsVirtualDesktop FQDN 标记中捕获。 可以通过以下方式之一来解决此问题：

   - 允许从主机池子网访问 https 到 *xt.blob.core.windows.net、*eh.servicebus.windows.net 和 *xt.table.core.windows.net。 这些通配符 FQDN 会允许所需的访问，但限制更少。
   - 使用以下日志分析查询列出所需的确切 FQDN，然后在防火墙应用程序规则中显式允许这些 FQDN：
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- 创建网络规则集合添加以下规则：

   - 允许 DNS – 允许从 ADDS 专用 IP 地址到 * 的 TCP 和 UDP 端口 53 的流量。
   - 允许 KMS –允许从 Windows 虚拟桌面虚拟机到 Windows 激活服务 TCP 端口 1688 的流量。 有关目标 IP 地址的详细信息，请参阅[强制隧道方案中的 Windows 激活失败](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution)。

> [!NOTE]
> 某些部署可能不需要 DNS 规则，例如 Azure Active Directory 域控制器将 DNS 查询转发到位于 168.63.129.16 的 Azure DNS。

## <a name="host-pool-outbound-access-to-the-internet"></a>主机池对 Internet 的出站访问

根据你的组织需求，可能想要为最终用户启用安全的出站 Internet 访问。 如果允许的目标列表定义完善（例如 [Microsoft 365 访问](/microsoft-365/enterprise/microsoft-365-ip-web-service)），则可以使用 Azure 防火墙应用程序和网络规则来配置所需的访问权限。 这会将最终用户流量直接路由到 Internet，以获得最佳性能。

如果要使用现有的本地安全 web 网关筛选出站用户 Internet 流量，则可以使用显式代理配置来配置 web 浏览器或 Windows 虚拟桌面主机池上运行的其他应用程序。 例如，请参阅[如何使用 Microsoft Edge 命令行选项配置代理设置](/deployedge/edge-learnmore-cmdline-options-proxy-settings)。 这些代理设置只影响最终用户的 Internet 访问，从而允许 Windows 虚拟桌面平台出站流量直接通过 Azure 防火墙。

## <a name="additional-considerations"></a>其他注意事项

根据你的要求，你可能需要配置其他防火墙规则：

- NTP 服务器访问

   默认情况下，运行 Windows 的虚拟机通过 UDP 端口 123 连接到 time.windows.com 进行时间同步。 创建一个网络规则来允许此访问，或者为在环境中使用的时间服务器创建一个网络规则。


## <a name="next-steps"></a>后续步骤

- 了解有关 Windows 虚拟桌面的详细信息：[什么是 Windows 虚拟桌面？](../virtual-desktop/overview.md)