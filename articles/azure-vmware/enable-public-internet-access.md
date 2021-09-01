---
title: 为 Azure VMware 解决方案工作负载启用公共 Internet
description: 本文介绍如何使用 Azure 虚拟 WAN 中的公共 IP 功能。
ms.topic: how-to
ms.date: 06/25/2021
ms.openlocfilehash: bae760da5da39118f32b5d0b4dfa661a81727f3c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322007"
---
# <a name="enable-public-internet-for-azure-vmware-solution-workloads"></a>为 Azure VMware 解决方案工作负载启用公共 Internet

公共 IP 是 Azure VMware 解决方案连接性中的一项新功能。 它使 Web 服务器、虚拟机 (VM) 等资源以及主机可通过公用网络访问。 

可以通过两种方式实现公共 Internet 访问。 

- 在应用程序网关负载均衡器下托管和发布应用程序，以获取 HTTP/HTTPS 流量。

- 通过 Azure 虚拟 WAN 中的公共 IP 功能发布。

作为 Azure VMware 解决方案私有云部署的一部分，启用公共 IP 功能时，将创建并启用具有自动化功能的必需组件：

-  虚拟 WAN

-  具有 ExpressRoute 连接的虚拟 WAN 中心

-  具有公共 IP 的 Azure 防火墙服务

本文详细介绍如何使用虚拟 WAN 中的公共 IP 功能。

## <a name="prerequisites"></a>先决条件

- Azure VMware 解决方案环境

- 在 Azure VMware 解决方案环境中运行的 Web 服务器。

- 虚拟 WAN 中心部署的新的非重叠 IP 范围，通常为 `/24`。

## <a name="reference-architecture"></a>参考体系结构

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="显示 Azure VMware 解决方案的公共 IP 体系结构的图。" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

体系结构关系图显示了在 Azure VMware 解决方案环境中托管并配置了 RFC1918 专用 IP 地址的 Web 服务器。  Web 服务通过虚拟 WAN 公共 IP 功能提供给 Internet。  公共 IP 通常是在 Azure 防火墙中转换的目标 NAT。 使用 DNAT 规则时，防火墙策略将公共 IP 地址请求转换为带有端口的专用地址（Web 服务器）。

用户请求命中公共 IP 上的防火墙，然后使用 Azure 防火墙中的 DNAT 规则将该公共 IP 转换为专用 IP。 防火墙检查 NAT 表，如果请求与某个条目匹配，则会将流量转发到 Azure VMware 解决方案环境中的已转换地址和端口。

Web 服务器接收请求，并向防火墙回复请求的信息或页面，然后防火墙将信息转发给公共 IP 地址上的用户。

## <a name="test-case"></a>测试用例
在此方案中，你要将 IIS Web 服务器发布到 Internet。 使用 Azure VMware 解决方案中的公共 IP 功能将网站发布到公共 IP 地址上。  你还将在防火墙上配置 NAT 规则，并使用公共 IP 访问 Azure VMware 解决方案资源（具有的 Web 服务器的 VM）。

>[!TIP]
>若要启用出口流量，必须将“安全配置”下的“Internet 流量”设置为“Azure 防火墙”。

## <a name="deploy-virtual-wan"></a>部署虚拟 WAN

1. 登录到 Azure 门户，然后搜索并选择“Azure VMware 解决方案”。

1. 选择 Azure VMware 解决方案私有云。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware 解决方案私有云的屏幕截图。" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. 在“管理”下，选择“连接”。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="“连接”部分的屏幕截图。" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. 选择“公共 IP”选项卡，然后选择“配置”。

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="显示开始配置公共 IP 的位置的屏幕截图。" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. 接受默认值或对其进行更改，然后选择“创建”。

   - 虚拟 WAN 资源组

   - 虚拟 WAN 名称

   - 虚拟中心地址块（使用新的非重叠 IP 范围）

   - 公共 IP 数（1-100 个）

完成所有组件的部署大约需要一小时。 此部署只需要发生一次即可支持此 Azure VMware 解决方案环境的所有未来公共 IP。  

>[!TIP]
>可以从“通知”区域监视状态。 

## <a name="view-and-add-public-ip-addresses"></a>查看和添加公共 IP 地址

我们可以按照以下步骤检查并添加更多公共 IP 地址。

1. 在 Azure 门户中，搜索并选择“防火墙”。

1. 选择已部署的防火墙，然后选择“访问 Azure 防火墙管理器以配置和管理此防火墙”。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="显示用于配置和管理防火墙的选项的屏幕截图。" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. 选择“安全虚拟中心”，然后从列表中选择一个虚拟中心。

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="防火墙管理器的屏幕截图。" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. 在虚拟中心页上，选择“公共 IP 配置”并添加更多公共 IP 地址，然后选择“添加”。 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="如何在防火墙管理器中添加公共 IP 配置的屏幕截图。" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. 提供所需的 IP 数量，然后选择“添加”。

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="添加指定数量的公共 IP 配置的屏幕截图。":::


## <a name="create-firewall-policies"></a>创建防火墙策略

部署所有组件后，即可在添加的资源组中看到它们。 下一步是添加防火墙策略。

1. 在 Azure 门户中，搜索并选择“防火墙”。

1. 选择已部署的防火墙，然后选择“访问 Azure 防火墙管理器以配置和管理此防火墙”。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="显示用于配置和管理防火墙的选项的屏幕截图。" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. 选择“Azure 防火墙策略”，然后选择“创建 azure 防火墙策略”。

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="如何在防火墙管理器中创建防火墙策略的屏幕截图。" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. 在“基本信息”选项卡下，提供所需的详细信息并选择“下一步: DNS 设置”。 

1. 在“DNS”选项卡下，选择“禁用”，然后选择“下一步: 规则”。

1. 选择“添加规则集合”，提供以下详细信息并选择“添加”。 然后选择“下一步: 威胁情报”。

   -  名称
   -  规则集合类型 - DNAT
   -  优先级
   -  规则集合操作 - 允许
   -  规则的名称
   -  源类型 - IPaddress
   -  源 - \*
   -  协议 - TCP
   -  目标端口 - 80
   -  目标类型 - IP 地址
   -  目标 - 公共 IP 地址
   -  已转换地址 - Azure VMware 解决方案 Web 服务器专用 IP 地址
   -  已转换端口 - Azure VMware 解决方案 Web 服务器端口

1. 保留默认值，然后选择“下一步: 中心”。

1. 选择“关联虚拟中心”。

1. 从列表中选择一个中心，然后选择“添加”。

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="显示将转换为安全虚拟中心的选定中心的屏幕截图。" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. 在完成时选择“下一步:  标记”。 

1. （可选）创建用于对资源进行分类的名称和值对。 

1. 选择“下一步: 审查并创建”，然后选择“创建”。

## <a name="limitations"></a>限制

每个私有云可以有 100 个公共 IP。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure VMware 解决方案中的公共 IP 功能，你可能想要了解：

- 将公共 IP 地址用于 [Azure 虚拟 WAN](../virtual-wan/virtual-wan-about.md)。
- [创建接入到 Azure VMware 解决方案的 IPSec 隧道](./configure-site-to-site-vpn-gateway.md)。
