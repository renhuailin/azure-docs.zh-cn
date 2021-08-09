---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/16/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b47f21ba6597380ca79bfce03feb8edd62aa5ae
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439624"
---
1. 在“搜索资源、服务和文档(G+/)”中，键入“虚拟网络网关” 。 在搜索结果中找到“虚拟网络网关”，并选中它。

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/search.png" alt-text="“搜索”字段的屏幕截图。" lightbox="./media/vpn-gateway-add-gw-portal/search-expand.png":::

1. 在“虚拟网络网关”页上，选择“+ 创建” 。 这会打开“创建虚拟网关”页  。

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/create.png" alt-text="“虚拟网络网关”页的屏幕截图，其中突出显示了“创建”。":::
1. 在“基本信息”选项卡上，填写“项目详细信息”和“实例详细信息”的值  。

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/instance-details.png" alt-text="实例字段的屏幕截图。":::

   * 订阅：从下拉列表中选择要使用的订阅。
   * **资源组**：在此页上选择虚拟网络后，此设置将自动进行填充。
   * **名称**：为网关命名。 为网关命名与为网关子网命名不同。 它是要创建的网关对象的名称。
   * **区域**：选择要在其中创建此资源的区域。 网关的区域必须与虚拟网络相同。
   * **网关类型**：选择“VPN”。  VPN 网关使用虚拟网络网关类型“VPN”  。
   * **VPN 类型**：选择为你的配置指定的 VPN 类型。 大多数配置需要''基于路由'' VPN 类型。
   * SKU：从下拉列表中选择想要使用的网关 SKU。 下拉列表中列出的 SKU 取决于选择的 VPN 类型。 确保选择支持你想要使用的功能的 SKU。 有关网关 SKU 的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)。
   * 代系：选择想要使用的代系。 有关详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)。
   * **虚拟网络**：从下拉列表中，选择要将此网关添加到其中的虚拟网络。
   * **网关子网地址范围**：仅当 VNet 没有网关子网时，此字段才会显示。 最好指定 /27 或更大的范围（/26、/25 等）。 这为将来的更改提供了足够的 IP 地址，例如添加一个 ExpressRoute 网关。 建议不要创建任何小于 /28 的范围。 如果你已有网关子网，可通过导航到虚拟网络来查看 GatewaySubnet 详细信息。 单击“子网”，以查看范围。 如果要更改范围，可以删除并重新创建 GatewaySubnet。