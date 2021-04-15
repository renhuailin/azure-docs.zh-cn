---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487027"
---
1. 在 [Azure 门户](https://portal.azure.com)的“搜索资源、服务和文档(G+/)”中，键入“本地网关” 。 在搜索结果中找到“市场”下的“本地网关”，并选择它 。 此操作将打开“创建本地网关”页。
1. 在“创建本地网络网关”页上，指定本地网络网关的值  。

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="创建一个具有 IP 地址的本地网关":::

   * **名称：** 指定本地网络网关对象的名称。
   * **终结点：** 为本地 VPN 设备选择终结点类型 - IP 地址或 FQDN（完全限定的域名） 。
      * **IP 地址**：如果你的 Internet 服务提供商向你分配了静态公共 IP 地址可用于 VPN 设备，那么请选择“IP 地址”选项，再填写示例所示的 IP 地址。 这是 Azure VPN 网关要连接的 VPN 设备的公共 IP 地址。 如果目前没有 IP 地址，可以使用示例中显示的值，但是需要返回并将占位符 IP 地址替换为 VPN 设备的公共 IP 地址。 否则，Azure 不能连接。
   * “地址空间”指的是此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保此处所指定的范围没有与要连接到的其他网络的范围相重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果需要连接到本地站点，请在此处使用自己的值，而不是示例中显示的值。
   * **配置 BGP 设置：** 仅在配置 BGP 时使用。 否则，不选择此项。
   * **订阅：** 确保显示正确的订阅。
   * **资源组：** 选择要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
   * **位置：** 该位置与其他设置中的“区域”相同。 选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。

1. 将值指定完以后，选择页面底部的“创建”按钮即可创建本地网关。
