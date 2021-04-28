---
title: 为 ISE 设置公共出站 IP 地址
description: 了解如何在 Azure 逻辑应用中为集成服务环境 (ISE) 设置单个公共出站 IP 地址
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874182"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>在 Azure 逻辑应用中为一个或多个集成服务环境设置单个 IP 地址

使用 Azure 逻辑应用时，可以设置一个[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，用于承载需要访问 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中的资源的逻辑应用。 如果你有多个 ISE 实例需要访问具有 IP 限制的其他终结点，请将 [Azure 防火墙](../firewall/overview.md)或[网络虚拟设备](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虚拟网络中，并通过该防火墙或网络虚拟设备路由出站流量。 然后，可以让虚拟网络中的所有 ISE 实例使用单个可预测的公共静态 IP 地址来与所需的目标系统通信。 这样就无需在目标系统上为每个 ISE 设置其他防火墙开放端口。

本主题介绍如何通过 Azure 防火墙路由出站流量，但你可以将类似的概念应用于网络虚拟设备，例如 Azure 市场中提供的第三方防火墙。 尽管本主题重点介绍多个 ISE 实例的设置，但如果你的方案要求限制需要访问权限的 IP 地址数，你也可以对单个 ISE 使用此方法。 考虑防火墙或虚拟网络设备的额外费用是否适合你的场景。 详细了解 [Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)。

## <a name="prerequisites"></a>必备条件

* 一个在 ISE 所在的同一虚拟网络中运行的 Azure 防火墙。 如果你没有防火墙，请先将一个名为 `AzureFirewallSubnet` 的[子网添加](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)到虚拟网络。 然后，可以在虚拟网络中[创建并部署防火墙](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

* 一个 Azure [路由表](../virtual-network/manage-route-table.md)。 如果你没有路由表，请先[创建一个路由表](../virtual-network/manage-route-table.md#create-a-route-table)。 有关路由的详细信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="set-up-route-table"></a>设置路由表

1. 在 [Azure 门户](https://portal.azure.com)中选择路由表，例如：

   ![选择包含用于定向出站流量的规则的路由表](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 若要[添加新路由](../virtual-network/manage-route-table.md#create-a-route)，请在路由表菜单中选择“路由” > “添加” 。

   ![添加用于定向出站流量的路由](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 在“添加路由”窗格中[设置新路由](../virtual-network/manage-route-table.md#create-a-route)，其中包含的某个规则指定发往目标系统的所有出站流量都遵循此行为：

   * 使用“[虚拟设备](../virtual-network/virtual-networks-udr-overview.md#user-defined)”作为下一跃点类型。

   * 转到防火墙实例的、用作下一跃点地址的专用 IP 地址。

     若要找到此 IP 地址，请在防火墙菜单中选择“概述”，并在“专用 IP 地址”下找到该地址，例如 ：

     ![查找 Azure 防火墙专用 IP 地址](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   以下示例演示了此类规则的大致内容：

   ![设置用于定向出站流量的规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **路由名称** | <*unique-route-name*> | 路由在路由表中的唯一名称 |
   | **地址前缀** | <*destination-address*> | 要将出站流量定向到的目标系统的地址前缀。 请确保使用[无类域间路由 (CIDR) 表示法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)指定此地址。 在此示例中，此地址前缀用于[设置网络规则](#set-up-network-rule)部分中所述的 SFTP 服务器。 |
   | **下一跃点类型** | **虚拟设备** | 出站流量使用的[跃点类型](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **下一跃点地址** | <*firewall-private-IP-address*> | 防火墙的专用 IP 地址 |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>设置网络规则

1. 在 Azure 门户中，找到并选择你的防火墙。 在防火墙菜单中的“设置”下，选择“规则” 。 在规则窗格中，选择“网络规则集合” > “添加网络规则集合” 。

   ![将网络规则集合添加到防火墙](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 在集合中，添加一个允许将流量发往目标系统的规则。

   例如，假设你的逻辑应用在 ISE 中运行，并需要与 SFTP 服务器通信。 可以创建名为 `LogicApp_ISE_SFTP_Outbound` 的网络规则集合，其中包含名为 `ISE_SFTP_Outbound` 的网络规则。 此规则允许使用防火墙的专用 IP 地址，将来自虚拟网络中运行 ISE 的任何子网的 IP 地址的流量，发送到目标 SFTP 服务器。

   ![设置防火墙的网络规则](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **网络规则集合属性**

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | <*network-rule-collection-name*> | 网络规则集合的名称 |
   | **Priority** | <*priority-level*> | 用于运行规则集合的优先顺序。 有关详细信息，请参阅 [Azure 防火墙的概念有哪些](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)？ |
   | **操作** | **允许** | 要对此规则执行的操作类型 |
   |||

   **网络规则属性**

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **名称** | <*network-rule-name*> | 网络规则的名称 |
   | 协议  | <*connection-protocols*> | 要使用的连接协议。 例如，如果你使用 NSG 规则，请同时选择“TCP”和“UDP”，而不要仅选择“TCP”  。 |
   | **源地址** | <*ISE-subnet-addresses*> | 运行 ISE 的，以及逻辑应用中的流量来源于的子网 IP 地址 |
   | **目标地址** | <*destination-IP-address*> | 要将出站流量定向到的目标系统的 IP 地址。 在此示例中，此 IP 地址用于 SFTP 服务器。 |
   | **目标端口** | <*destination-ports*> | 由目标系统用来进行入站通信的任何端口 |
   |||

   有关网络规则的详细信息，请参阅以下文章：

   * [配置网络规则](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure 防火墙规则处理逻辑](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure 防火墙常见问题解答](../firewall/firewall-faq.yml)
   * [Azure PowerShell：New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI：az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>后续步骤

* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
