---
title: 在 Azure 中对专用终结点禁用网络策略
description: 了解如何对专用终结点禁用网络策略。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6014867ed30901f0b6470d843dd7b3761bc1ba71
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696604"
---
# <a name="disable-network-policies-for-private-endpoints"></a>对专用终结点禁用网络策略

专用终结点不支持网络安全组 (NSG) 等网络策略。 若要在给定的子网上部署专用终结点，需要在该子网上显式配置禁用设置。 此设置仅适用于专用终结点。 对于子网中的其他资源，访问权限基于网络安全组 (NSG) 安全规则定义进行控制。 
 
使用门户创建专用终结点时，会在创建过程中自动禁用此设置。 使用其他客户端进行的部署需要额外的步骤来更改此设置。 可以使用 Azure 门户中的云 shell 或 Azure PowerShell、Azure CLI 的本地安装来禁用此设置，也可以使用 Azure 资源管理器模板。  
 
以下示例介绍了如何对名为 *myVirtualNetwork* 的虚拟网络禁用 `PrivateEndpointNetworkPolicies`，此时 *默认* 子网托管在名为 *myResourceGroup* 的资源组中。

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本部分介绍如何使用 Azure PowerShell 禁用子网专用终结点策略。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本部分介绍如何使用 Azure CLI 禁用子网专用终结点策略。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>使用模板
本部分介绍如何使用 Azure 资源管理器模板禁用子网专用终结点策略。
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
 
