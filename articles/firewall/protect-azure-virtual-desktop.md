---
title: 使用 Azure 防火墙保护 Azure 虚拟桌面
description: 了解如何使用 Azure 防火墙保护 Azure 虚拟桌面部署
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 08/09/2021
ms.author: victorh
ms.openlocfilehash: 254a81e9fe5f3f0d3e98d7db6a7f70778f9746a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698955"
---
# <a name="use-azure-firewall-to-protect-azure-virtual-desktop-deployments"></a>使用 Azure 防火墙保护 Azure 虚拟桌面部署

Azure 虚拟桌面是在 Azure 运行的桌面和应用虚拟化服务。 当最终用户连接到 Azure 虚拟桌面环境时，其会话由主机池运行。 主机池是注册到 Azure 虚拟桌面作为会话主机的 Azure 虚拟机的集合。 这些虚拟机在虚拟网络中运行，并受虚拟网络安全控制的限制。 它们需要对 Azure 虚拟桌面服务的出站 Internet 访问权限才能正常运行，并且可能还需要最终用户的出站 Internet 访问权限。 Azure 防火墙可以帮助你锁定环境并筛选出站流量。

[ ![Azure 虚拟桌面体系结构](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

请参阅本文中的指南，使用 Azure 防火墙为 Azure 虚拟桌面主机池提供额外保护。

## <a name="prerequisites"></a>先决条件


 - 已部署的 Azure 虚拟桌面环境和主机池。
 - 至少部署了一个防火墙管理器策略的 Azure 防火墙 

   有关详细信息，请参阅[教程：使用 Azure 门户创建主机池](../virtual-desktop/create-host-pools-azure-marketplace.md)

若要详细了解 Azure 虚拟桌面环境，请参阅 [Azure 虚拟桌面环境](../virtual-desktop/environment-setup.md)。

## <a name="host-pool-outbound-access-to-azure-virtual-desktop"></a>主机池对 Azure 虚拟桌面的出站访问权限

为 Azure 虚拟桌面创建的 Azure 虚拟机必须有权访问多个完全限定的域名 (FQDN) 才能正常工作。 Azure 防火墙提供 Azure 虚拟桌面 FQDN 标记以简化此配置。 使用以下步骤允许出站 Azure 虚拟桌面平台流量：

需要创建 Azure 防火墙策略，并为网络规则和应用程序规则创建规则集合。 为规则集合设置优先级，并指定允许或拒绝操作。 

### <a name="create-network-rules"></a>创建网络规则

| “属性” | 源类型 | 源 | 协议 | 目标端口 | 目标类型 | 目标 |
| --- | --- | --- | --- | --- | --- | --- |
| 规则名称 | IP 地址 | VNet 或子网 IP 地址 | 80 | TCP |  IP 地址 | 169.254.169.254、168.63.129.16 |
| 规则名称 | IP 地址 | VNet 或子网 IP 地址 | 443 | TCP | 服务标记 | AzureCloud、WindowsVirtualDesktop |
| 规则名称 | IP 地址 | VNet 或子网 IP 地址 | 53 | TCP、UDP | IP 地址 | * |

> [!NOTE]
> 某些部署可能不需要 DNS 规则。 例如，Azure Active Directory 域控制器会将 DNS 查询转发到位于 168.63.129.16 的 Azure DNS。

### <a name="create-application-rules"></a>创建应用程序规则 

| “属性” | 源类型 | 源 | 协议 | 目标类型 | 目标|
| --- | --- | --- | --- | --- | --- |
| 规则名称 | IP 地址 | VNet 或子网 IP 地址 | Https:443 | FQDN 标记 | WindowsVirtualDesktop、WindowsUpdate、Windows 诊断、MicrosoftActiveProtectionService |
| 规则名称 | IP 地址 | VNet 或子网 IP 地址 | Https:1688 | FQDN | kms.core.windows.net |


> [!IMPORTANT]
> 建议不要将 TLS 检查用于 Azure 虚拟桌面。 有关详细信息，请参阅[代理服务器准则](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server)。

## <a name="host-pool-outbound-access-to-the-internet"></a>主机池对 Internet 的出站访问

视组织的需求而定，你可能想要为最终用户启用安全的出站 Internet 访问。 如果允许的目标列表定义完善（例如 [Microsoft 365 访问](/microsoft-365/enterprise/microsoft-365-ip-web-service)），可以使用 Azure 防火墙应用程序和网络规则来配置所需的访问权限。 这会将最终用户流量直接路由到 Internet，以获得最佳性能。 如果需要允许 Windows 365 或 Intune 的网络连接，请参阅 [Windows 365 的网络要求](/windows-365/requirements-network#allow-network-connectivity)和 [Intune 的网络终结点](/mem/intune/fundamentals/intune-endpoints)。

如果要使用现有的本地安全 Web 网关筛选出站用户 Internet 流量，可以使用显式代理配置来配置 Web 浏览器或 Azure 虚拟桌面主机池上运行的其他应用程序。 例如，请参阅[如何使用 Microsoft Edge 命令行选项配置代理设置](/deployedge/edge-learnmore-cmdline-options-proxy-settings)。 这些代理设置只影响最终用户的 Internet 访问，从而允许 Azure 虚拟桌面平台出站流量直接通过 Azure 防火墙。 

## <a name="control-user-access-to-the-web"></a>控制用户对 Web 的访问

管理员可以允许或拒绝用户访问不同的网站类别。 将规则添加到应用程序集合中，可以从特定 IP 地址到要允许或拒绝的 Web 类别。 查看所有 [Web 类别](web-categories.md)。 

## <a name="additional-considerations"></a>其他注意事项

你可能需要配置其他防火墙规则，具体取决于你的要求：

- NTP 服务器访问

  默认情况下，运行 Windows 的虚拟机通过 UDP 端口 123 连接到 `time.windows.com` 进行时间同步。 创建一个网络规则来允许此访问，或者为在环境中使用的时间服务器创建一个网络规则。

## <a name="next-steps"></a>后续步骤

- 了解有关 Azure 虚拟桌面的详细信息：[什么是 Azure 虚拟桌面？](../virtual-desktop/overview.md)
