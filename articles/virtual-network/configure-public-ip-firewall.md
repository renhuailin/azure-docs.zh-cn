---
title: 使用 Azure 防火墙管理公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解如何结合使用公共 IP 地址和 Azure 防火墙，以及如何更改配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 1ffb1242dfded6af9c869a53367016d30dd61f49
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439122"
---
# <a name="manage-a-public-ip-address-with-azure-firewall"></a>使用 Azure 防火墙管理公共 IP 地址

Azure 防火墙是基于云的网络安全服务，可保护 Azure 虚拟网络资源。 Azure 防火墙需要至少配置一个公共静态 IP 地址。 此 IP 或 IP 组用作防火墙的外部连接点。 Azure 防火墙支持标准 SKU 公共 IP 地址。 不支持基本 SKU 公共 IP 地址和公共 IP 前缀。 

在本文中，你将了解如何使用订阅中的现有公共 IP 创建 Azure 防火墙。 你将更改防火墙的 IP 配置。 最后，你将为防火墙添加 IP 配置。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的三个标准 SKU 公共 IP 地址。 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](create-public-ip-portal.md)。
    - 基于本文中示例的用途，请将新的公共 IP 地址命名为 myStandardPublicIP-1、myStandardPublicIP-2 和 myStandardPublicIP-3  。

## <a name="create-azure-firewall-existing-public-ip"></a>创建 Azure 防火墙现有公共 IP

在本部分中，你将创建一个 Azure 防火墙。 你将选择在先决条件中创建的 IP 地址作为防火墙的公共 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“防火墙”。

3. 在搜索结果中，选择“防火墙”。

4. 选择“+ 新建”。 

5. 在“创建防火墙”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“myResourceGroupFW”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 输入“myFirewall”。 |
    | 区域 | 选择“美国西部 2”  。 |
    | 可用性区域 | 保留默认值“无”。 |
    | 防火墙层  | 保留默认值“标准”。 |
    | 防火墙管理 | 保留默认值“使用防火墙策略管理此防火墙”。|
    | 防火墙策略 | 选择“添加新订阅”。 </br> 在“策略名称”中输入“myFirewallPolicy” 。 </br> 对于“区域”，请选择“美国西部 2”。  </br> 请选择“是”。 |
    | 虚拟网络名称 | 输入 **myVNet**。 |
    | 地址空间 | 输入 **10.0.0.0/16**。 |
    | 子网地址空间 | 输入 10.0.0.0/26。 |
    | 公共 IP 地址 | 选择“myStandardPublicIP-1”或你的公共 IP。 |
    | 强制隧道 | 保留默认值“禁用”。 |
    
 
6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。

7. 选择“创建”。

> [!NOTE]
> 这是一个简单的 Azure 防火墙部署。 有关高级配置和设置，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙和策略](../firewall/tutorial-firewall-deploy-portal-policy.md)。
>
> 有关 Azure 防火墙的详细信息，请参阅[什么是 Azure 防火墙？](../firewall/overview.md)。

## <a name="change-public-ip-address"></a>更改公共 IP 地址

在本部分中，你将更改与防火墙关联的公共 IP 地址。 防火墙必须至少有一个与其配置关联的公共 IP 地址。 

1. 在门户顶部的搜索框中，输入“防火墙”。

2. 在搜索结果中，选择“防火墙”。

3. 在“防火墙”中，选择“myFirewall” 。

4. 在“myFirewall”中的“设置”中，选择“公共 IP 配置”  。

5. 在“公共 IP 配置”中，选择“myStandardPublicIP-1”或你的 IP 地址 。

6. 在“编辑公共 IP 配置”的“公共 IP 地址”中，选择“myStandardPublicIP-2”  。

7. 选择“保存”  。

## <a name="add-public-ip-configuration"></a>添加公共 IP 配置

在本部分中，你将为 Azure 防火墙添加公共 IP 配置。 有关多个 IP 的详细信息，请参阅[多个公共 IP 地址](../firewall/features.md#multiple-public-ip-addresses)。  

1. 在门户顶部的搜索框中，输入“防火墙”。

2. 在搜索结果中，选择“防火墙”。

3. 在“防火墙”中，选择“myFirewall” 。

4. 在“myFirewall”中的“设置”中，选择“公共 IP 配置”  。

5. 选择“+ 添加公共 IP 配置”。

6. 在“名称”中，输入“myNewPublicIPconfig” 。

7. 在“公共 IP 地址”中，选择“myStandardPublicIP-3” 。

8. 选择 **添加** 。

## <a name="more-information"></a>详细信息

* Azure 防火墙可以与标准 SKU 负载均衡器集成，以保护后端池资源。  如果将防火墙与公共负载均衡器相关联，请将入口流量配置为定向到防火墙公共 IP 地址。 将出口流量配置为通过用户定义的路由流向防火墙公共 IP 地址。  有关详细信息和设置说明，请参阅[将 Azure 防火墙与 Azure 标准负载均衡器相集成](../firewall/integrate-lb.md)。 

* 还可以将 Azure 防火墙与 NAT 网关相关联，以扩展源网络地址转换 (SNAT) 的扩展性。 NAT 网关可防止配置允许来自与防火墙关联的大量公共 IP 的流量。 使用此配置时，所有入站流量都将使用 NAT 网关的公共 IP 地址。 流量通过 Azure 防火墙公共 IP 地址流出。  有关详细信息，请参阅[利用 Azure NAT 网关缩放 SNAT 端口](../firewall/integrate-with-nat-gateway.md)。

## <a name="caveats"></a>注意事项

* Azure 防火墙使用标准 SKU 负载均衡器。 网络筛选规则中除 TCP 和 UDP 以外的协议不支持将 SNAT 用于防火墙的公共 IP。 
## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何创建 Azure 防火墙和使用现有公共 IP。 你更改了默认 IP 配置的公共 IP。 最后，你为防火墙添加了公共 IP 配置。

- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](public-ip-addresses.md)。
- 若要详细了解 Azure 防火墙，请参阅[什么是 Azure 防火墙？](../firewall/overview.md)。
