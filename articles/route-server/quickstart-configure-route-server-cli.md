---
title: 快速入门：使用 Azure CLI 创建并配置路由服务器
description: 这篇快速入门文章介绍如何使用 Azure CLI 来创建并配置路由服务器。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419602"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>快速入门：使用 Azure CLI 创建并配置路由服务器 

本文可帮助你使用 Azure CLI 将 Azure 路由服务器配置为与虚拟网络中的网络虚拟设备 (NVA) 对等互连。 Azure 路由服务器将会从 NVA 获知路由，并将这些路由配置到虚拟网络中的虚拟机上。 它还会将虚拟网络路由播发到 NVA。 有关详细信息，请阅读 [Azure 路由服务器](overview.md)。

> [!IMPORTANT]
> Azure 路由服务器（预览版）目前为公共预览版状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

##  <a name="prerequisites"></a>先决条件 

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 请确保具有最新的 Azure CLI，或者可以在门户中使用 Azure Cloud Shell。 
* 查看 [Azure 路由服务器的服务限制](route-server-faq.md#limitations)。 

##  <a name="create-a-route-server"></a>创建路由服务器 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，并选择订阅。 

要开始配置，请登录到 Azure 帐户。 如果使用 Cloud Shell“试用”，则会自动登录。 使用下面的示例来帮助你连接：

```azurecli-interactive
az login
```

检查该帐户的订阅。

```azurecli-interactive
az account list
```

选择要为其创建 ExpressRoute 线路的订阅。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>创建资源组和虚拟网络 

你需要先有一个虚拟网络来托管部署，然后才能创建 Azure 路由服务器。 请使用以下命令来创建资源组和虚拟网络。 如果已经有虚拟网络，可以跳转到下一部分。

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>添加子网 

1. 添加名为“RouteServerSubnet”的子网，以便在其中部署 Azure 路由服务器。 此子网为专用子网，仅用于 Azure 路由服务器。 RouteServerSubnet 必须是 /27 或更短的前缀（如 /26、/25），否则你会在添加 Azure 路由服务器时收到错误消息。

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. 获取 RouteServerSubnet ID。 若要查看虚拟网络中所有子网的资源 ID，请使用此命令： 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

RouteServerSubnet ID 如下所示： 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>创建路由服务器 

使用此命令来创建路由服务器： 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

该位置需要与虚拟网络的位置匹配。 HostedSubnet 是在前一部分中获取的 RouteServerSubnet ID。 

## <a name="create-peering-with-an-nva"></a>创建与 NVA 之间的对等互连 

请使用以下命令来建立从路由服务器到 NVA 的对等互连： 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

“nva_ip”是分配到 NVA 的虚拟网络 IP。 “nva_asn”是在 NVA 中配置的自治系统编号 (ASN)。 该 ASN 可以是 65515-65520 范围之外的任意 16 位数字。 此 ASN 范围是 Microsoft 保留的。 

若要设置与不同 NVA 的对等互连或与同一 NVA 的另一实例的对等互连，以便实现冗余，请使用此命令：

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>在 NVA 上完成配置 

若要在 NVA 上完成配置并启用 BGP 会话，需要 Azure 路由服务器的 IP 和 ASN。 可以使用以下命令来获取此信息： 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

输出中会包含以下信息。 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>配置路由交换 

如果在同一 VNet 中有一个 ExpressRoute 网关和一个 Azure VPN 网关，并且需要让它们交换路由，则可以在 Azure 路由服务器上启用路由交换。

> [!IMPORTANT]
> 对于全新的部署，请确保先创建 Azure VPN 网关，然后再创建 Azure 路由服务器；否则 Azure VPN 网关的部署将会失败。
> 

1. 若要在 Azure 路由服务器和网关之间启用路由交换，请使用此命令：

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. 若要在 Azure 路由服务器和网关之间禁用路由交换，请使用此命令：

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>疑难解答 

可以使用此命令来查看 Azure 路由服务器播发和接收的路由：

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure 路由服务器，请使用以下命令来删除 BGP 对等互连，然后删除该路由服务器。 

1. 使用此命令删除 Azure 路由服务器与 NVA 之间的 BGP 对等互连：

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. 使用此命令删除路由服务器： 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>后续步骤

在创建 Azure 路由服务器后，请继续了解 Azure 路由服务器如何与 ExpressRoute 和 VPN 网关进行交互： 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 和 Azure VPN 支持](expressroute-vpn-support.md)
 
