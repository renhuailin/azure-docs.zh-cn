---
title: 如何配置虚拟 WAN 中心路由策略
titleSuffix: Azure Virtual WAN
description: 了解如何配置虚拟 WAN 路由策略
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/01/2021
ms.author: wellee
ms.openlocfilehash: d61e6c3847d9ce64f9c3f17da1300b32a1a8e643
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122206092"
---
# <a name="how-to-configure-virtual-wan-hub-routing-intent-and-routing-policies"></a>如何配置虚拟 WAN 中心路由意向和路由策略

>[!NOTE] 
> 中心路由意向目前处于封闭公共预览阶段。 
> 
> 此预览版在提供时未附带服务级别协议，建议不要用于生产工作负载。 部分功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 
> 若要获取对此预览版的访问权限，请向 previewinterhub@microsoft.com 发送邮件并提供你要配置路由意向的虚拟 WAN ID、订阅 ID 和 Azure 区域。 通常你会在 48 个工作小时（周一至周五）内收到确认启用功能的回复。

## <a name="background"></a>背景 

使用 Azure 防火墙管理器为公共和专用流量设置策略的客户现在可以使用路由意向和路由策略，以简单得多的方式设置其网络。

路由意向和路由策略用于指定虚拟 WAN 中心如何转发 Internet 绑定和专用（点到站点、站点到站点、ExpressRoute、虚拟 WAN 中心和虚拟网络中的网络虚拟设备）流量。 有两种类型的路由策略：Internet 流量路由策略和专用流量路由策略。 每个虚拟 WAN 中心最多可以有一个 Internet 流量路由策略和一个专用流量路由策略，每个策略都有一个“下一个跃点”资源。 

虽然专用流量同时包含分支和虚拟网络地址前缀，但路由策略将它们视为路由意向概念中的一个实体。

>[!NOTE]
> 在虚拟 WAN 中心路由策略的封闭公共预览版中，如果虚拟 WAN 中心位于同一区域，则仅由 Azure 防火墙检查中心间流量。 


* **Internet 流量路由策略**：在虚拟 WAN 中心配置 Internet 流量路由策略时，到该虚拟 WAN 中心的所有分支（用户 VPN [点到站点 VPN]、站点到站点 VPN 和 ExpressRoute）连接以及虚拟网络连接都会将 Internet 绑定流量转发到在路由策略中指定的 Azure 防火墙资源或第三方安全提供程序。
 

* **专用流量路由策略**：在虚拟 WAN 中心配置专用流量路由策略时，进出虚拟 WAN 中心的所有分支和虚拟网络流量（包括中心间流量）都会转发到已在专用流量路由策略中指定的“下一个跃点”Azure 防火墙资源。 

    换句话说，在虚拟 WAN 中心配置专用流量路由策略时，所有分支到分支、分支到虚拟网络、虚拟网络到分支和中心间流量都将通过 Azure 防火墙发送。


## <a name="key-considerations"></a>重要注意事项
* 如果配置了现有的自定义路由表，或者默认路由表中配置了静态路由，则无法在部署中启用路由策略。
* 目前，具有加密 ExpressRoute 连接（通过 ExpressRoute 专用连接运行的站点到站点 VPN 隧道）的中心不支持专用流量路由策略。 
* 在虚拟 WAN 中心路由策略的封闭公共预览版中，如果虚拟 WAN 中心位于同一区域，则仅由 Azure 防火墙检查中心间流量。
* 目前，必须通过“先决条件”步骤 3 中提供的自定义门户链接配置路由意向和路由策略。 Terraform、PowerShell 和 CLI 不支持路由意向和策略。 

## <a name="prerequisites"></a>先决条件
1. 创建虚拟 WAN。 确保在同一区域至少创建两个虚拟中心。 例如，可以在美国东部创建一个包含两个虚拟中心的虚拟 WAN。 
2. 通过将 Azure 防火墙部署到所选区域中的虚拟中心，将虚拟 WAN 中心转换为安全虚拟 WAN 中心。 有关将虚拟 WAN 中心转换为安全虚拟 WAN 中心的详细信息，请参阅此[文档](howto-firewall.md)。
3. 向 **previewinterhub@microsoft.com** 发送邮件并提供你要配置路由策略的虚拟 WAN 资源 ID 和 Azure 虚拟中心区域 。 若要查找虚拟 WAN ID，请打开 Azure 门户，导航到虚拟 WAN 资源并选择“设置”>“属性”>“资源 ID”。 例如： 
```
    /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>
```
4.  通常你会在 24-48 小时内收到确认启用功能的回复。 
5. 确保虚拟中心没有任何自定义路由表或任何可能已添加到 defaultRouteTable 的路由。 如果配置了现有的自定义路由表，或者默认路由表中配置了静态路由，则无法在部署中启用路由策略。 

## <a name="configuring-routing-policies"></a>配置路由策略
1. 从“先决条件”部分的步骤 3 的确认电子邮件中提供的自定义门户链接，导航到要配置路由策略的虚拟 WAN 中心。
1. 在“安全性”下，选择“安全虚拟中心设置”，然后选择“在 Azure 防火墙管理器中管理此安全虚拟中心的安全提供程序和路由设置”
:::image type="content" source="./media/routing-policies/secured-hub-settings.png"alt-text="显示如何修改安全中心设置的屏幕截图。"lightbox="./media/routing-policies/secured-hub-settings.png":::
1. 从菜单中选择要配置路由策略的中心。
1. 在“设置”下，选择“安全配置” 
1. 如果要配置 Internet 流量路由策略，请从“Internet 流量”的下拉列表中选择“Azure 防火墙”或相关的 Internet 安全提供程序。 否则，请选择“无”
1. 如果要通过 Azure 防火墙配置专用流量路由策略（针对分支和虚拟网络流量），请从“专用流量”的下拉列表中选择“Azure 防火墙”。 否则，请选择“绕过 Azure 防火墙”。

:::image type="content" source="./media/routing-policies/configuring-intents.png"alt-text="显示如何配置路由策略的屏幕截图。"lightbox="./media/routing-policies/configuring-intents.png":::

7. 如果要配置专用流量路由策略，并让分支或虚拟网络播发非 IANA RFC1918 前缀，请选择“专用流量前缀”，并在随即显示的文本框中指定非 IANA RFC1918 前缀范围。 选择“完成”。 

:::image type="content" source="./media/routing-policies/private-prefixes.png"alt-text="显示如何编辑专用流量前缀的屏幕截图。"lightbox="./media/routing-policies/private-prefixes.png":::

8. 将“中心间”设置为“已启用”。 启用此选项可确保路由策略应用于此虚拟 WAN 中心的路由意向。 
9. 选择“保存”。 此操作需要大约 10 分钟才能完成。 
10. 对要配置路由策略的其他安全虚拟 WAN 中心重复步骤 2-8。 
 
## <a name="routing-policy-configuration-examples"></a>路由策略配置示例 

以下部分介绍客户将路由策略应用于安全虚拟 WAN 中心的两种常见方案。

### <a name="all-virtual-wan-hubs-are-secured-deployed-with-azure-firewall"></a>所有虚拟 WAN 中心都受到保护（部署有 Azure 防火墙）

在此方案中，所有虚拟 WAN 中心都部署了 Azure 防火墙。 在此方案中，你可以在每个虚拟 WAN 中心配置 Internet 流量路由策略和/或专用流量路由策略。 

:::image type="content" source="./media/routing-policies/two-secured-hubs-diagram.png"alt-text="显示具有两个安全中心的体系结构的屏幕截图。"lightbox="./media/routing-policies/two-secured-hubs-diagram.png":::

以下面的配置为例，其中，中心 1 和中心 2 具有用于专用流量和 Internet 流量的路由策略。 

**中心 1 配置：**
* 使用下一个跃点中心 1 Azure 防火墙的专用流量策略
* 使用下一个跃点中心 1 Azure 防火墙的 Internet 流量策略 

**中心 2 配置：**
* 使用下一个跃点中心 2 Azure 防火墙的专用流量策略
* 使用下一个跃点中心 2 Azure 防火墙的 Internet 流量策略 

下面是这种配置产生的流量流。 

> [!NOTE]
> Internet 流量必须通过本地 Azure 防火墙流出，因为默认路由 (0.0.0.0/0) 不跨中心传播。

| 从 |   功能 |  中心 1 VNet | 中心 1 分支 | 中心 2 VNet | 中心 2 分支| Internet|
| -------------- | -------- | ---------- | ---| ---| ---| ---|
| 中心 1 VNet     | &#8594;| 中心 1 AzFW |   中心 1 AzFW    | 中心 1 和 2 AzFW | 中心 1 和 2 AzFW | 中心 1 AzFW |
| 中心 1 分支   | &#8594;|  中心 1 AzFW  |   中心 1 AzFW    | 中心 1 和 2 AzFW | 中心 1 和 2 AzFW | 中心 1 AzFW|
| 中心 2 VNet     | &#8594;| 中心 1 和 2 AzFW |   中心 1 和 2 AzFW    | 中心 2 AzFW  | 中心 2 AzFW | 中心 2 AzFW|
| 中心 2 分支   | &#8594;|   中心 1 和 2 AzFW  |    中心 1 和 2 AzFW    | 中心 2 AzFW |  中心 2 AzFW | 中心 2 AzFW|


### <a name="mixture-of-secured-and-regular-virtual-wan-hubs"></a>混合使用安全虚拟 WAN 中心和常规虚拟 WAN 中心 

在此方案中，并非所有虚拟 WAN 中心都部署了 Azure 防火墙。 在此方案中，你可以在安全虚拟 WAN 中心配置 Internet 流量路由策略和专用流量路由策略。 

以下面的配置为例，其中，中心 1（标准）和中心 2（安全）部署在虚拟 WAN 中。 中心 2 具有用于专用流量和 Internet 流量的路由策略。 

**中心 1 配置：**
* 不适用（如果中心未部署 Azure 防火墙，则无法配置路由策略）

**中心 2 配置：**
* 使用下一个跃点中心 2 Azure 防火墙的专用流量策略
* 使用下一个跃点中心 2 Azure 防火墙的 Internet 流量策略 


:::image type="content" source="./media/routing-policies/one-secured-one-normal-diagram.png"alt-text="显示具有一个安全中心和一个标准中心的体系结构的屏幕截图。"lightbox="./media/routing-policies/one-secured-one-normal-diagram.png":::


 下面是这种配置产生的流量流。 连接到中心 1 的分支和虚拟网络无法通过中心的 Azure 防火墙访问 Internet，因为默认路由 (0.0.0.0/0) 不跨中心传播。

| 从 |   功能 |  中心 1 VNet | 中心 1 分支 | 中心 2 VNet | 中心 2 分支| Internet |
| -------------- | -------- | ---------- | ---| ---| ---| --- |
| 中心 1 VNet     | &#8594;| 直接 |   直接   | 中心 2 AzFW | 中心 2 AzFW | - |
| 中心 1 分支   | &#8594;|  直接 |   直接    | 中心 2 AzFW | 中心 2 AzFW | - |
| 中心 2 VNet     | &#8594;| 中心 2 AzFW |   中心 2 AzFW    | 中心 2 AzFW  | 中心 2 AzFW | 中心 2 AzFW|
| 中心 2 分支   | &#8594;|   中心 2 AzFW  |    中心 2 AzFW    | 中心 2 AzFW |  中心 2 AzFW | 中心 2 AzFW|


## <a name="troubleshooting"></a>故障排除

以下部分介绍在虚拟 WAN 中心配置路由策略时遇到的常见问题。  阅读以下部分，如果你的问题仍未解决，请联系 previewinterhub@microsoft.com 寻求支持。 通常你会在 48 个工作小时（周一至周五）内收到回复。 

### <a name="troubleshooting-configuration-issues"></a>排查配置问题
*  确保你已从 previewinterhub@microsoft.com 获得确认，你的订阅和所选区域已经获得访问封闭公共预览版的权限。 你无法在没有获得预览版访问权限的情况下配置路由策略。
* 在部署中启用路由策略功能后，请确保仅使用确认电子邮件中提供的自定义门户链接。 请勿使用 PowerShell、CLI 或 REST API 调用来管理虚拟 WAN 部署。  这包括创建新的分支（站点到站点 VPN、点到站点 VPN 或 ExpressRoute）连接。 

>[!NOTE]
> 如果使用 Terraform，则当前不支持路由策略。

*  确保虚拟中心没有任何自定义路由表或在 defaultRouteTable 中没有任何静态路由。 如果配置了自定义路由表或 defaultRouteTable 中有静态路由，则无法从虚拟 WAN 中心的防火墙管理器中选择“启用中心间”。 

### <a name="troubleshooting-data-path"></a>排查数据路径问题 

* 目前，使用 Azure 防火墙检查中心间流量仅适用于部署在同一 Azure 区域中的虚拟 WAN 中心。 
* 目前，具有加密 ExpressRoute 连接（通过 ExpressRoute 专用连接运行的站点到站点 VPN 隧道）的中心不支持专用流量路由策略。 
* 可以通过检查 DefaultRouteTable 的有效路由来验证路由策略是否已正确应用。 如果配置了专用路由策略，你应该在 DefaultRouteTable 中看到与专用流量前缀匹配且包含“下一个跃点”Azure 防火墙的路由。 如果配置了 Internet 流量路由策略，你应该在 DefaultRouteTable 中看到包含“下一个跃点”Azure 防火墙的默认 (0.0.0.0/0) 路由。

### <a name="troubleshooting-azure-firewall"></a>排查 Azure 防火墙问题

* 如果在分支/虚拟网络中使用非 [IANA RFC1918](https://datatracker.ietf.org/doc/html/rfc1918) 前缀，请确保你已在防火墙管理器的“专用前缀”文本框中指定这些前缀。
* 如果在防火墙管理器的“专用流量前缀”文本框中指定了非 RFC1918 地址，你可能需要在防火墙上配置 SNAT 策略，以便对非 RFC1918 专用流量禁用 SNAT。 有关详细信息，请参阅以下[文档](../firewall/snat-private-range.md)。 
* 考虑配置和查看 Azure 防火墙日志，以帮助对网络流量进行故障排除和分析。 有关如何设置 Azure 防火墙监视的详细信息，请参阅以下[文档](../firewall/firewall-diagnostics.md)。 有关不同类型的防火墙日志的概述，请参阅[此处](../firewall/logs-and-metrics.md)。
* 有关 Azure 防火墙的详细信息，请查看 [Azure 防火墙文档](../firewall/overview.md)。



## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-cant-i-edit-the-defaultroutetable-from-the-custom-portal-link-provided-by-previewinterhubmicrosoftcom"></a>为什么我不能从 previewinterhub@microsoft.com 提供的自定义门户链接编辑 defaultRouteTable？

在路由策略的封闭公共预览版中，虚拟 WAN 中心路由完全由防火墙管理器管理。 此外，路由策略的托管预览版不支持与自定义路由一起使用。 以后将支持使用路由策略的自定义路由。 

但是，你仍然可以通过导航到“有效路由”选项卡来查看 DefaultRouteTable 的有效路由。

### <a name="can-i-configure-a-routing-policy-for-private-traffic-and-also-send-internet-traffic-00000-via-a-network-virtual-appliance-in-a-spoke-virtual-network"></a>能否为专用流量配置路由策略并通过分支虚拟网络中的网络虚拟设备发送 Internet 流量 (0.0.0.0/0)？

封闭公共预览版不支持此方案。 但是，可以向 previewinterhub@microsoft.com 发送邮件，表达你对实施此方案的兴趣。 

### <a name="does-the-default-route-00000-propagate-across-hubs"></a>默认路由 (0.0.0.0/0) 是否跨中心传播？

否。 目前，分支和虚拟网络使用 Azure 防火墙流出到 Internet，该防火墙部署在分支和虚拟网络连接到的虚拟 WAN 中心内。 你无法配置一个通过远程中心防火墙来访问 Internet 的连接。

### <a name="why-do-i-see-rfc1918-prefixes-advertised-to-my-on-premises-devices"></a>为什么我会看到 RFC1918 前缀播发到本地设备？

配置专用流量路由策略时，除了显式分支和虚拟网络前缀外，虚拟 WAN 网关还会自动播发默认路由表中的静态路由（RFC1918 前缀：10.0.0.0/8,172.16.0.0/12,192.168.0.0/16）。

## <a name="next-steps"></a>后续步骤

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
