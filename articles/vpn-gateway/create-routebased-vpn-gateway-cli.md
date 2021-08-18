---
title: 创建基于路由的虚拟网络网关：CLI
titleSuffix: Azure VPN Gateway
description: 了解如何创建基于路由的虚拟网关，以便通过 VPN 连接到本地网络或连接虚拟网络。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: 63c8187e8ca5e7d7c17016159e4751299359dddf
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720354"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>使用 CLI 创建基于路由的 VPN 网关

本文可帮助你使用 Azure CLI 快速创建基于路由的 Azure VPN 网关。 创建与本地网络的 VPN 连接时使用 VPN 网关。 还可以使用 VPN 网关连接 VNet。

本文中的步骤将创建 VNet、子网、网关子网和基于路由的 VPN 网关（虚拟网络网关）。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。 完成网关创建后，可以创建连接。 执行这些步骤需要 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group) 命令创建资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet) 命令创建虚拟网络。 以下示例在“EastUS”位置创建一个名为“VNet1”的虚拟网络：

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>添加网关子网

网关子网包含虚拟网络网关服务使用的保留 IP 地址。 使用下面的示例添加网关子网：

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>请求公共 IP 地址

VPN 网关必须具有动态分配的公共 IP 地址。 将向为虚拟网络创建的 VPN 网关分配公共 IP 地址。 使用下面的示例请求一个公共 IP 地址：

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>创建 VPN 网关

使用 [az network vnet-gateway create](/cli/azure/network/vnet-gateway) 命令创建 VPN 网关。

如果使用 `--no-wait` 参数运行该命令，则不会显示任何反馈或输出。 `--no-wait` 参数允许在后台创建网关。 但并不意味着 VPN 网关会立即创建。

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

创建 VPN 网关可能需要 45 分钟或更长时间。

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>查看 VPN 网关

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

响应类似于以下内容：

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>查看公共 IP 地址

若要查看分配给网关的公共 IP 地址，请使用以下示例：

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

与 **ipAddress** 字段关联的值是 VPN 网关的公共 IP 地址。

示例响应:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要所创建的资源，请使用 [az group delete](/cli/azure/group) 删除资源组。 这将删除资源组及其包含的所有资源。

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>后续步骤

完成创建网关后，可以创建虚拟网络与另一个 VNet 之间的连接。 或者，创建虚拟网络与本地位置之间的连接。

> [!div class="nextstepaction"]
> [创建站点到站点连接](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [创建点到站点连接](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [创建与另一个 VNet 的连接](vpn-gateway-vnet-vnet-rm-ps.md)
