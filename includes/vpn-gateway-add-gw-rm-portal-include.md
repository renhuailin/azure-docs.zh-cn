---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1894f38a92fe839dcf44cc3265aa1ca25c05d6f6
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111564919"
---
1. 在 [Azure 门户](https://portal.azure.com)的“搜索资源、服务和文档(G+/)”中，键入“虚拟网络网关” 。 在搜索结果中找到“虚拟网络网关”，并选中它。

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="搜索字段" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. 在“虚拟网络网关”页上选择“+ 添加” 。 这会打开“创建虚拟网关”页  。

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="“虚拟网络网关”页":::
1. 在“基本信息”选项卡上，填写虚拟网关的值。 

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="网关字段":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="其他网关字段":::

   * 订阅：从下拉列表中选择要使用的订阅。
   * **资源组**：在此页上选择虚拟网络后，此设置将自动进行填充。

   **实例详细信息**

   * **名称**：为网关命名。 为网关命名与为网关子网命名不同。 它是要创建的网关对象的名称。
   * **区域**：选择要在其中创建此资源的区域。 网关的区域必须与虚拟网络相同。
   * **网关类型**：选择“VPN”。  VPN 网关使用虚拟网络网关类型“VPN”  。
   * **VPN 类型**：选择为你的配置指定的 VPN 类型。 大多数配置需要''基于路由'' VPN 类型。
   * **SKU**：从下拉列表中选择网关 SKU。 下拉列表中列出的 SKU 取决于选择的 VPN 类型。 请确保选择那个支持你要使用的功能的 SKU。 有关网关 SKU 的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)。
   * **生成**：有关 VPN 网关生成的信息，请参阅 [网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)。
   * **虚拟网络**：从下拉列表中，选择要将此网关添加到其中的虚拟网络。
   * **网关子网地址范围**：仅当 VNet 没有网关子网时，此字段才会显示。 如果可能，请将范围设置为 /27 或更大（/26、/25 等）。 建议不要创建任何小于 /28 的范围。 如果你已有网关子网，可通过导航到虚拟网络来查看 GatewaySubnet 详细信息。 单击“子网”，以查看范围。 如果要更改范围，可以删除并重新创建 GatewaySubnet。

   **公共 IP 地址**

   此设置指定与 VPN 网关关联的公共 IP 地址对象。 创建 VPN 网关后，会将公共 IP 地址动态分配给此对象。 公共 IP 地址只在删除或重新创建网关时更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

     * **公共 IP 地址**：让“新建”  保持选中状态。
     * **公共 IP 地址名称**：在文本框中，键入公共 IP 地址实例的名称。
     * **分配**：VPN 网关仅支持“动态”。
     * **启用主动-主动模式**：仅当要创建主动-主动网关配置时，才选择“启用主动-主动模式”。 否则，请让此设置保留“禁用”状态。
     * 让“配置 BGP”保留“禁用”状态，除非你的配置特别需要此设置 。 如果确实需要此设置，则默认 ASN 为 65515，但可以更改此值。
1. 选择“查看 + 创建”  ，运行验证。
1. 验证通过后，选择“创建”  以部署 VPN 网关。

网关可能需要长达 45 分钟才能完全创建和部署。 可以在网关的“概述”页上查看部署状态。 创建网关后，可以通过在门户中查看虚拟网络，来查看已分配给网关的 IP 地址。 网关显示为连接的设备。
