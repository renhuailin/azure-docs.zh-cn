---
title: 管理专用终结点的网络策略
titleSuffix: Azure Private Link
description: 了解如何管理专用终结点的网络策略。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 08/26/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c06ad374e46e9900ba99a95708e19a63498719ec
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101542"
---
# <a name="manage-network-policies-for-private-endpoints"></a>管理专用终结点的网络策略

专用终结点以前不支持 NSG（网络安全组）之类的网络策略。 若要在给定的子网上部署专用终结点，需要在该子网上启用显式的禁用设置。 此设置仅适用于专用终结点。 对于子网中的其他资源，将根据网络安全组的安全规则控制访问。

> [!IMPORTANT]
> NSG 和 UDR 对专用终结点的支持处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用门户创建专用终结点时，会在创建过程中自动禁用 `PrivateEndpointNetworkPolicies` 设置。 使用其他客户端进行部署需要额外的步骤来更改此设置。 

可以使用以下方法禁用和启用设置：

* Azure 门户中的 Cloud Shell。
* Azure PowerShell
* Azure CLI
* Azure Resource Manager 模板
 
以下示例介绍如何对名为“myVNet”的虚拟网络禁用和启用 `PrivateEndpointNetworkPolicies`，该虚拟网络的默认子网托管在名为“myResourceGroup”的资源组中。  

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="disable-network-policy"></a>禁用网络策略

本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。 使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 和 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) 禁用策略。

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```

### <a name="enable-network-policy"></a>启用网络策略

本部分介绍如何使用 Azure PowerShell 启用子网专用终结点策略。 使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 和 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) 启用策略。

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Enabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI

### <a name="disable-network-policy"></a>禁用网络策略

本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 禁用策略。

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```

### <a name="enable-network-policy"></a>启用网络策略

本部分介绍如何使用 Azure CLI 启用子网专用终结点策略。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 启用策略。

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies false \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>资源管理器模板

### <a name="disable-network-policy"></a>禁用网络策略

本节介绍如何使用 Azure 资源管理器模板禁用子网专用终结点策略。

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```

### <a name="enable-network-policy"></a>启用网络策略

本部分介绍如何使用 Azure 资源管理器模板启用子网专用终结点策略。

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Enabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用终结点](private-endpoint-overview.md)
 
