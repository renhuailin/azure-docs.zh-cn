---
title: 对专用终结点禁用网络策略
titleSuffix: Azure Private Link
description: 了解如何对专用终结点禁用网络策略。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 07/14/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 41cdefd340ace93d5a068c9a74543965834d01ca
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221347"
---
# <a name="disable-network-policies-for-private-endpoints"></a>对专用终结点禁用网络策略

专用终结点不支持网络安全组 (NSG) 等网络策略。 若要在给定的子网上部署专用终结点，需要在该子网上显式配置禁用设置。 此设置仅适用于专用终结点。 对于子网中的其他资源，将根据网络安全组的安全规则控制访问。
 
使用门户创建专用终结点时，会在创建过程中自动禁用 `PrivateEndpointNetworkPolicies` 设置。 使用其他客户端进行部署需要额外的步骤来更改此设置。 

可以使用以下方法禁用设置：

* Azure 门户中的 Cloud Shell。
* Azure PowerShell
* Azure CLI
* Azure 资源管理器模板
 
以下示例介绍如何对名为 myVNet 的虚拟网络禁用 `PrivateEndpointNetworkPolicies`，该虚拟网络的默认子网托管在名为 myResourceGroup 的资源组中。  

## <a name="azure-powershell"></a>Azure PowerShell

本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。 使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 和 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) 禁用策略。

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI
本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 禁用策略。

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>资源管理器模板
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
## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用终结点](private-endpoint-overview.md)
 
