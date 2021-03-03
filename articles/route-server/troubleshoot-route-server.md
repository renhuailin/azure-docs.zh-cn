---
title: 排查 Azure 路由服务器问题
description: 了解如何对 Azure 路由服务器的问题进行故障排除。
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679183"
---
# <a name="troubleshooting-azure-route-server-issues"></a>排查 Azure 路由服务器问题

> [!IMPORTANT]
> Azure 路由服务器 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="bgp-connectivity-issues"></a>BGP 连接问题

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>为什么我的 NVA 与 Azure 路由服务器之间的 BGP 对等互连 ( "稳定" ) ？

由于 BGP 计时器设置的原因，导致稳定的原因。 默认情况下，Azure 路由服务器上的保持活动状态计时器设置为60秒，保留计时器为180秒。

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>我为什么可以从我的 NVA ping 到 Azure 路由服务器上的 BGP 对等节点 IP，但在设置它们之间的 BGP 对等互连后，不能再对同一 IP 进行 ping 操作呢？ BGP 对等互连为何会关闭？

在某些 NVA 中，需要添加 Azure 路由服务器子网的静态路由。 例如，如果 Azure 路由服务器在 10.0.255.0/27 中，而你的 NVA 处于 10.0.1.0/24，则需要将以下路由添加到 NVA 中的路由表：

| 路由 | 下一跃点 |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 是子网中的默认网关 IP，其中，NVA (或更精确地) 承载其中一个 Nic。

## <a name="bgp-route-issues"></a>BGP 路由问题

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>为什么 NVA 不会从 Azure 路由服务器接收路由，即使 BGP 对等互连已启动也是如此？

Azure 路由服务器使用的 ASN 为65515。 请确保为 NVA 配置其他 ASN，以便可以在 NVA 和 Azure 路由服务器之间建立 "eBGP" 会话，以便可以自动进行路由传播。

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>我的 NVA 与 Azure 路由服务器之间的 BGP 对等互连。 我可以看到在它们之间正确交换的路由。 为什么 NVA 路由在我的 VM 的有效路由表中呢？ 

* 如果 VM 与 NVA 和 Azure 路由服务器位于同一 VNet 中：

     Azure 路由服务器公开两个 BGP 对等 Ip，它们托管在两个 Vm 上，这些 Ip 共享将路由发送到虚拟网络中运行的其他所有 Vm 的责任。 每个 NVA 都必须设置两个相同的 BGP 会话 (例如，使用与 path 相同的相同 as number，并将同一组路由) 播发到两个 Vm，以便虚拟网络中的 Vm 可以从 Azure 路由服务器获取一致的路由信息。 请参阅下图。

    ![显示包含路由服务器的网络虚拟设备的图示。](./media/faq/network-virtual-appliances.png)

    如果有两个或多个 NVA 实例，则如果要将一个 NVA 实例指定为活动和其他被动实例， *可以* 为来自不同 NVA 实例的同一路由播发不同的路径。

* 如果 VM 所在的虚拟网络与承载 NVA 和 Azure 路由服务器的虚拟网络不同。 检查是否在两个 Vnet 之间启用了 VNet 对等互连 *，以及* 是否在 VM 的 VNet 中启用了 "使用远程路由服务器"。

## <a name="next-steps"></a>后续步骤

了解如何 [配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)
