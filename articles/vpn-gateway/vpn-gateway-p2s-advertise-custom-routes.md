---
title: 为点到站点 VPN 网关客户端播发自定义路由
titleSuffix: Azure VPN Gateway
description: 了解如何将自定义路由播发到 VPN 网关点到站点客户端。 本文包括 VPN 客户端强制隧道的步骤。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/21/2021
ms.author: cherylmc
ms.openlocfilehash: cefbd6d014dda28a5e88a41a0131eeb92fc5f311
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440715"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>为 P2S VPN 客户端播发自定义路由

你可能想要向所有点到站点 VPN 客户端播发自定义路由。 例如，当你在 VNet 中启用了存储终结点并希望远程用户能够通过 VPN 连接访问这些存储帐户时。 可以向所有远程用户播发存储终结点的 IP 地址，以使到存储帐户的流量通过 VPN 隧道，而不是公共 Internet。 还可以使用自定义路由来配置 VPN 客户端的强制隧道。

:::image type="content" source="./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png" alt-text="播发自定义路由的关系图。":::

## <a name="advertise-custom-routes"></a><a name="advertise"></a>播发自定义路由

若要播发自定义路由，请使用 `Set-AzVirtualNetworkGateway cmdlet`。 以下示例演示如何播发 [Contoso 存储帐户表](https://contoso.table.core.windows.net)的 IP。

1. Ping contoso.table.core.windows.net 并记录 IP 地址。 例如：

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 运行以下 PowerShell 命令：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 若要添加多个自定义路由，请使用逗号和空格分隔地址。 例如：

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```

## <a name="advertise-custom-routes---forced-tunneling"></a><a name="forced-tunneling"></a>播发自定义路由 - 强制隧道

可以通过将 0.0.0.0/1 和 128.0.0.0/1 作为自定义路由播发到客户端，将所有流量定向到 VPN 隧道。 将 0.0.0.0/0 拆分为两个较小的子网的原因是，这些较小的前缀比可能已在本地网络适配器上配置的默认路由更具体，因此路由流量时，这是首选。

> [!NOTE]
> 不通过 VPN 网关提供 Internet 连接。 因此，会删除所有绑定到 Internet 的流量。
>

1. 若要启用强制隧道，请使用以下命令：

    ```azurepowershell-interactive    
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 0.0.0.0/1 , 128.0.0.0/1
    ```

## <a name="view-custom-routes"></a><a name="view"></a>查看自定义路由

使用以下示例查看自定义路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="delete-custom-routes"></a><a name="delete"></a>删除自定义路由

使用以下示例删除自定义路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>后续步骤

有关 P2S 路由的详细信息，请参阅[关于点到站点路由](vpn-gateway-about-point-to-site-routing.md)。
