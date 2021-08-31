---
title: 配置强制隧道 - 站点到站点连接：经典
titleSuffix: Azure VPN Gateway
description: 了解如何为使用经典部署模型创建的虚拟网络配置强制隧道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 24c2643da85527a3ff4b2392d607540e2c2801fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729598"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>使用经典部署模型配置强制隧道

借助强制隧道，可以通过站点到站点 VPN 隧道，将全部 Internet 绑定流量重定向或“强制”返回到本地位置，以进行检查和审核。 这是很多企业 IT 策略的关键安全要求。 如果没有强制隧道，来自 Azure 中 VM 的 Internet 绑定流量会始终通过 Azure 网络基础设施直接连接到 Internet，而无法选择对流量进行检查或审核。 未经授权的 Internet 访问可能会导致信息泄漏或其他类型的安全漏洞。

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

本文逐步演示如何配置虚拟网络（使用经典部署模型创建）的强制隧道。 强制隧道可以使用 PowerShell（不通过门户）来配置。 如果想要配置用于[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)的强制隧道，请从下面的下拉列表中选择与资源管理器相关的文章：

> [!div class="op_single_selector"]
> * [经典](vpn-gateway-about-forced-tunneling.md)
> * [资源管理器](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>要求和注意事项

在 Azure 中，可通过虚拟网络用户定义路由 (UDR) 配置强制隧道。 将流量重定向到本地站点，这是 Azure VPN 网关的默认路由。 以下部分列出了 Azure 虚拟网络路由和路由表的当前限制：

* 每个虚拟网络子网具有内置的系统路由表。 系统路由表具有以下三组路由：

  * **本地 VNet 路由：** 直接路由到同一个虚拟网络中的目标 VM。
  * **本地路由：** 路由到 Azure VPN 网关。
  * **默认路由：** 直接路由到 Internet。 如果要将数据包发送到不包含在前面两个路由中的专用 IP 地址，数据包会被删除。
* 随着用户定义路由的发布，可以创建路由表来添加默认路由，然后将路由表关联到 VNet 子网，在这些子网启用强制隧道。
* 需要在连接到虚拟网络的跨界本地站点中，设置一个“默认站点”。
* 强制隧道必须关联到具有动态路由 VPN 网关（而非静态网关）的 VNet。
* ExpressRoute 强制隧道不是通过此机制配置的，而是通过 ExpressRoute BGP 对等会话播发默认路由来启用的。 有关详细信息，请参阅[什么是 ExpressRoute？](../expressroute/expressroute-introduction.md)。

## <a name="configuration-overview"></a>配置概述

在以下示例中，前端子网没有使用强制隧道。 前端子网中的工作负载可以继续直接接受并响应来自 Internet 的客户请求。 中间层和后端子网会使用强制隧道。 任何从这两个子网到 Internet 的出站连接都会通过一个 S2S VPN 隧道重定向或强制返回到本地站点。

这样，在继续支持所需的多层服务体系结构的同时，可以限制并检查来自虚拟机或 Azure 云服务的 Internet 访问。 如果在虚拟网络中没有面向 Internet 的工作负荷，也能选择对整个虚拟网络应用强制隧道连接。

![强制隧道](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>先决条件

在开始配置之前，请确认具有以下各项：

* Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 已配置虚拟网络。 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>配置强制隧道

以下过程将帮助你为虚拟网络指定强制隧道。 配置步骤与 VNet 网络配置文件相对应。  在本示例中，虚拟网络“MultiTier-VNet”具有三个子网：“前端”、“中间层”和“后端子网”，并且具有四个跨界连接：一个“DefaultSiteHQ” 和三个 Branch。


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

以下步骤会将“DefaultSiteHQ”设置为强制隧道的默认站点连接，并将中间层和后端子网配置为使用强制隧道。

1. 使用提升的权限打开 PowerShell 控制台。 使用以下示例连接到你的帐户：

   ```powershell
   Add-AzureAccount
   ```

1. 创建一个路由表。 使用以下 cmdlet 创建路由表。

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. 将默认路由添加到路由表中。 

   下面的示例将默认路由添加到在步骤 1 中创建的路由表。 请注意，唯一支持的路由是“0.0.0.0/0”到“VPN 网关”下一跃点的目标前缀。

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. 将路由表关联到子网。 

   创建路由表并添加路由后，可以使用以下示例将路由表添加到 VNet 子网，或将路由表与 VNet 子网关联。 下面的示例将“MyRouteTable”路由表添加到 VNet MultiTier-VNet 的中间层和后端子网。

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. 为强制隧道指定默认站点。 

   在前面的步骤中，示例 cmdlet 脚本创建了路由表，并将路由表关联到两个 VNet 子网。 剩下的步骤是在虚拟网络的多站点连接中，选择一个本地站点作为默认站点或隧道。

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>其他 PowerShell cmdlet

### <a name="to-delete-a-route-table"></a>删除路由表

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>列出路由表

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>从路由表中删除路由

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>从子网中删除路由

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>列出与子网关联的路由表

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>从 VNet VPN 网关中删除默认站点

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
