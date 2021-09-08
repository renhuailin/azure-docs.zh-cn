---
title: 快速入门：使用 Azure CLI 创建并配置路由服务器
description: 这篇快速入门文章介绍如何使用 Azure CLI 来创建并配置路由服务器。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 1682a1b80f3b2d85e0d263b9ea2648da2b57a561
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430893"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>快速入门：使用 Azure CLI 创建并配置路由服务器 

本文可帮助你使用 Azure PowerShell 将 Azure 路由服务器配置为与虚拟网络中的网络虚拟设备 (NVA) 对等互连。 路由服务器将会从 NVA 获知路由，并将这些路由配置到虚拟网络中的虚拟机上。 Azure 路由服务器还会将虚拟网络路由播发到 NVA。 有关详细信息，请参阅 [Azure 路由服务器](overview.md)。

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="使用 Azure CLI 的路由服务器部署环境示意图。" border="false":::

##  <a name="prerequisites"></a>先决条件 

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 请确保具有最新的 Azure CLI，或者可以在门户中使用 Azure Cloud Shell。 
* 查看 [Azure 路由服务器的服务限制](route-server-faq.md#limitations)。 

##  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，并选择订阅。 

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
az account set \
    --subscription "<subscription ID>"
```

## <a name="create-a-resource-group-and-a-virtual-network"></a>创建资源组和虚拟网络 

### <a name="create-a-resource-group"></a>创建资源组

在创建 Azure 路由服务器之前，必须创建一个资源组来托管路由服务器。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例将在 westus 位置中创建一个名为 myRouteServerRG 的资源组 ：

```azurecli-interactive
az group create \
    --name myRouteServerRG \
    --location westus
```

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例将创建一个名为 myVirtualNetwork 的默认虚拟网络。 如果已经有虚拟网络，可以跳转到下一部分。

```azurecli-interactive
az network vnet create \
    --name myVirtualNetwork \
    --resource-group myRouteServerRG \
    --address-prefix 10.0.0.0/16 
``` 

### <a name="add-a-dedicated-subnet"></a>添加专用子网 

Azure 路由服务器需要名为 RouteServerSubnet 的专用子网。 子网大小必须至少为 /27 或为短前缀（例如 /26 或 /25），否则在部署路由服务器时会收到错误消息。 使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 创建名为 RouteServerSubnet 的子网配置：

1. 运行以下命令将 RouteServerSubnet 添加到虚拟网络。

    ```azurecli-interactive 
    az network vnet subnet create \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --address-prefix 10.0.0.0/24)
    ``` 

1. 记下 RouteServerSubnet ID。 若要获取 RouteServerSubnet 的资源 ID 并将其存储到 `subnet_id` 变量中，请使用 [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show)：

    ```azurecli-interactive 
    subnet_id=$(az network vnet subnet show \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --query id -o tsv) 

    echo $subnet_id
    ```

## <a name="create-the-route-server"></a>创建路由服务器 

1. 若要确保连接到管理路由服务器配置的后端服务，需要分配一个公共 IP 地址。 使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 创建一个名为 RouteServerIP 的标准公共 IP：

    ```azurecli-interactive
    az network public-ip create \
        --name RouteServerIP \
        --resource-group myRouteServerRG \
        --version IPv4 \
        --sku Standard
    ```

2. 使用 [az network routeserver create](/cli/azure/network/routeserver#az_network_routeserver_create) 创建 Azure 路由服务器。 以下示例将创建一个名为 myRouteServer 的 Azure 路由服务器。 hosted-subnet 是在前一部分中创建的 RouteServerSubnet 的资源 ID。

    ```azurecli-interactive
    az network routeserver create \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --hosted-subnet $subnet_id \
        --public-ip-address RouteServerIP
    ``` 

## <a name="create-bgp-peering-with-an-nva"></a>与 NVA 建立 BGP 对等互连 

使用 [az network routeserver peering create](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_create) 在路由服务器和 NVA 之间建立 BGP 对等互连： 

`peer-ip` 是分配到 NVA 的虚拟网络 IP。 `peer-asn` 是在 NVA 中配置的自治系统编号 (ASN)。 该 ASN 可以是 65515-65520 范围之外的任意 16 位数字。 此 ASN 范围是 Microsoft 保留的。

```azurecli-interactive 
az network routeserver peering create \
    --name myNVA \
    --peer-ip 192.168.0.1 \
    --peer-asn 65501 \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
``` 

若要设置与不同的 NVA 或同一 NVA 的另一个实例的对等互连以实现冗余，请使用与上述相同的命令，但需要使用不同的 PeerName、PeerIp 和 PeerAsn  。

## <a name="complete-the-configuration-on-the-nva"></a>在 NVA 上完成配置 

若要在 NVA 上完成配置并启用 BGP 会话，需要 Azure 路由服务器的 IP 和 ASN。 可以使用 [az network routeserver show](/cli/azure/network/routeserver#az_network_routeserver_show) 获取该信息：

```azurecli-interactive 
az network routeserver show \
    --name myRouteServer \
    --resource-group myRouteServerRG 
``` 

输出将如下所示：

``` 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>配置路由交换 

如果在同一虚拟网络中有一个 ExpressRoute 和 Azure VPN 网关，并且需要让它们交换路由，则可以在 Azure 路由服务器上启用路由交换。

> [!IMPORTANT]
> 对于全新的部署，请确保先创建 Azure VPN 网关，然后再创建 Azure 路由服务器；否则 Azure VPN 网关的部署将会失败。
> 

1. 若要启用 Azure 路由服务器和网关之间的路由交换，请使用 [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update)，并将 `--allow-b2b-traffic`` 标志设置为 true：

    ```azurecli-interactive 
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic true 
    ``` 

2. 若要禁用 Azure 路由服务器和网关之间的路由交换，请使用 [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update)，并将 `--allow-b2b-traffic`` 标志设置为 false：

    ```azurecli-interactive
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic false 
    ``` 

## <a name="troubleshooting"></a>疑难解答 

使用 [az network routeserver peering list-advertised-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_advertised_routes) 查看 Azure 路由服务器播发的路由：

```azurecli-interactive 
az network routeserver peering list-advertised-routes \
    --name myNVA \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
```

使用 [az network routeserver peering list-learned-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_learned_routes) 查看 Azure 路由服务器获知的路由：

```azurecli-interactive
az network routeserver peering list-learned-routes \
    --name myNVA \
    --routeserver myRouteServer
    --resource-group myRouteServerRG \
``` 

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure 路由服务器，请使用第一个命令删除 BGP 对等互连，然后使用第二个命令删除路由服务器。 

1. 使用 [az network routeserver peering delete](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_delete) 删除 Azure 路由服务器和 NVA 之间的 BGP 对等互连：

    ```azurecli-interactive
    az network routeserver peering delete \
        --name myNVA \
        --routeserver myRouteServer \
        --resource-group myRouteServerRG
    ``` 

2. 使用 [az network routeserver delete](/cli/azure/network/routeserver#az_network_routeserver_delete) 删除 Azure 路由服务器： 

    ```azurecli-interactive 
    az network routeserver delete \
        --name myRouteServer \
        --resource-group myRouteServerRG
    ``` 

## <a name="next-steps"></a>后续步骤

在创建 Azure 路由服务器后，请继续了解有关 Azure 路由服务器如何与 ExpressRoute 和 VPN 网关进行交互的详细信息： 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 和 Azure VPN 支持](expressroute-vpn-support.md)
 
