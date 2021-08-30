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
ms.openlocfilehash: ff30bf22f0f435db8ae0888440bb0ea26948bef3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481217"
---
4. 指定“公共 IP 地址”的值。 这些设置指定与 VPN 网关关联的公共 IP 地址对象。 创建 VPN 网关后，会将公共 IP 地址动态分配给此对象。 对于非[区域冗余](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)网关，公共 IP 地址只在删除或重新创建网关时会更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

   :::image type="content" source="./media/vpn-gateway-add-gw-pip-active-portal/pip-details.png" alt-text="“公共 IP 地址”字段的屏幕截图。":::

     * **公共 IP 地址**：让“新建”  保持选中状态。
     * **公共 IP 地址名称**：在文本框中，键入公共 IP 地址实例的名称。
     * **分配**：VPN 网关仅支持“动态”。
     * **启用主动-主动模式**：选择“启用”。
     * **第二个公共 IP 地址**：选择“新建”。
     * **公共 IP 地址名称**：第二个公共 IP 地址的名称。
     * 让“配置 BGP”保留“禁用”状态，除非你的配置特别需要此设置 。 如果确实需要此设置，则默认 ASN 为 65515，但可以更改此值。
5. 选择“查看 + 创建”  ，运行验证。
6. 验证通过后，选择“创建”  以部署 VPN 网关。