---
title: 使用 VPN 网关管理公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解公共 IP 地址如何与 VPN 网关结合使用，以及如何更改配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 5333408e59edfacd34d1e4dd4b14d0c598f8f268
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438964"
---
# <a name="manage-a-public-ip-address-with-a-vpn-gateway"></a>使用 VPN 网关管理公共 IP 地址

公共 IP 地址可在两个 SKU 中使用：标准和基本。 所选的 SKU 确定了 IP 地址的功能。 SKU 确定了 IP 地址可以关联到的资源。 

VPN 网关是一种虚拟网络网关，用于通过公共 Internet 在 Azure 虚拟网络和本地位置之间发送加密的流量。 也可使用 VPN 网关在基于 Microsoft 网络的 Azure 虚拟网络之间发送加密流量。 每个虚拟网络只能有一个 VPN 网关。 VPN 网关支持标准和基本 SKU 公共 IP 地址，具体取决于 VPN 网关的 SKU。 不支持公共 IP 前缀。

VPN 网关需要将公共 IP 地址用于其配置。 公共 IP 地址用作 VPN 的外部连接点。 

本文介绍如何使用订阅中的现有公共 IP 创建 VPN 网关。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的一个标准 SKU 公共 IP 地址。 该 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](create-public-ip-portal.md)。
    - 为实现本文中的示例目的，请将新的公共 IP 地址命名为“myStandardPublicIP”。

## <a name="create-vpn-gateway-existing-public-ip"></a>创建 VPN 网关现有公共 IP

在此部分，需创建一个 VPN 网关， 并选择在先决条件中创建的 IP 地址作为 VPN 网关的公共 IP。

### <a name="create-virtual-network"></a>创建虚拟网络

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“虚拟网络”。

3. 在搜索结果中，选择“虚拟网络”。

4. 选择“+ 新建”。 

5. 在“创建虚拟网络”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“myResourceGroupVPN”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 输入 **myVNet**。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    
6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。

7. 选择“创建”。

8. 在门户顶部的搜索框中，输入“虚拟网络”。

9. 在搜索结果中，选择“虚拟网络”。

10. 在“虚拟网络”中，选择“myVNET”。

11. 在 myVNET 的“设置”中，选择“子网”  。

12. 选择“+ 网关子网”。

13. 在“添加子网”中，将“子网地址范围”从“/24”更改为“/27”。   

14. 选择“保存”。

### <a name="create-vpn-gateway"></a>创建 VPN 网关


2. 在门户顶部的搜索框中，输入“虚拟网络网关”。

3. 在搜索结果中，选择“虚拟网络网关”。

4. 选择“+ 新建”。 

5. 在“创建虚拟网络网关”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | **实例详细信息** |   |
    | 名称 | 输入“myVPNGateway”。 |
    | 区域 | 选择“美国西部 2”  。 |
    | 网关类型 | 保留默认值“VPN”。 |
    | VPN 类型 | 保留默认值“基于路由”。 |
    | SKU | 选择“VpnGw1AZ”。 |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 条目会自动选择此前创建的“GatewaySubnet” |
    | **公共 IP 地址** |   |
    | 公共 IP 地址 | 选择“使用现有项”。 |
    | 选择公共 IP 地址 | 选择“myStandardPublicIP”或你的公共 IP 地址 |
    | 启用“主动-主动”模式 | 保留默认值“禁用”。 |
    | 配置 BGP | 保留默认值“禁用”。 |

6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。

7. 选择“创建”。

> [!NOTE]
> 这是一个简单的 VPN 网关部署。 有关高级配置和设置，请参阅[教程：使用 Azure 门户创建和管理 VPN 网关](../vpn-gateway/tutorial-create-gateway-portal.md)。
>
> 有关 Azure VPN 网关的详细信息，请参阅[什么是 VPN 网关？](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

## <a name="change-or-remove-public-ip-address"></a>更改或删除公共 IP 地址

VPN 网关不支持在创建公共 IP 地址后对其进行更改。

## <a name="caveats"></a>注意事项

* VPN 网关目前不支持公共 IPv6 地址。
 
## <a name="next-steps"></a>后续步骤

本文介绍了如何创建 VPN 网关和使用现有公共 IP。 

- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](public-ip-addresses.md)。
- 若要详细了解 VPN 网关，请参阅[什么是 VPN 网关？](../vpn-gateway/vpn-gateway-about-vpngateways.md)。