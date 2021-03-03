---
title: 为网关配置 VPN NAT 规则
titleSuffix: Azure Virtual WAN
description: 了解如何配置 VWAN VPN 网关的 NAT 规则
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744515"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>为虚拟 WAN VPN 网关配置 NAT 规则-预览

> [!IMPORTANT]
> NAT 规则目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以通过静态一对一 NAT 规则配置虚拟 WAN VPN 网关。 NAT 规则提供了一种机制，用于设置 IP 地址的一对一转换。 NAT 可用于互连两个 IP 网络，这些网络具有不兼容或重叠的 IP 地址。 典型的方案是包含重叠 Ip、需要访问 Azure VNet 资源的分支。

此配置使用 flow 表将流量从外部 (主机) IP 地址路由到与虚拟网络 (虚拟机、计算机和容器等 ) 中的终结点关联的内部 IP 地址。

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="显示体系结构的关系图。":::

## <a name="configure-and-view-rules"></a><a name="view"></a>配置和查看规则

你可以随时配置和查看 VPN 网关设置的 NAT 规则。

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="显示编辑规则的屏幕截图。":::

1. 导航到虚拟中心。
1. 选择 " **VPN (站点到站点)**"。
1. 选择 " **NAT 规则 (编辑)**"。
1. 在 " **编辑 Nat 规则** " 页上，可以使用以下值 **添加/编辑/删除** NAT 规则：

   * **名称：** NAT 规则的唯一名称。
   * **键入：** 静止. 静态一对一 NAT 可在内部地址和外部地址之间建立一对一关系。
   * **模式：** IngressSnat 或 EgressSnat。  
      * IngressSnat 模式 (也称为入口源 NAT) 适用于进入 Azure 集线器站点到站点 VPN 网关的流量。
      * EgressSnat 模式 (也称为出口源 NAT) 适用于离开 Azure 集线器站点到站点 VPN 网关的流量。
   * **InternalMapping：** 内部网络上的源 Ip 地址前缀范围将映射到一组外部 Ip。 换句话说，预 NAT 地址前缀范围。
   * **ExternalMapping：** 源 Ip 将映射到的外部网络上的目标 Ip 地址前缀范围。 换句话说，你的 NAT 地址前缀范围。
   * **链接连接：** 连接资源，它将 VPN 站点连接到 Azure 中心的站点到站点 VPN 网关。

### <a name="configuration-considerations"></a>配置注意事项

* 内部和外部映射的子网大小对于静态一对一 NAT 必须是相同的。
* 请确保在 Azure 门户中编辑 VPN 站点，以在 "专用地址空间" 字段中添加 **ExternalMapping** 前缀。 目前，启用了 BGP 的站点需要确保本地 BGP announcer (设备 BGP 设置) 包含外部映射前缀的条目。

## <a name="next-steps"></a>后续步骤

有关站点到站点配置的详细信息，请参阅 [配置虚拟 WAN 站点到站点连接](virtual-wan-site-to-site-portal.md)。
