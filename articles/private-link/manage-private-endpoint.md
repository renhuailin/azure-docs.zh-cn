---
title: 管理 Azure 中的专用终结点连接
titleSuffix: Azure Private Link
description: 了解如何管理 Azure 中的专用终结点连接
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 10/04/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7923e35165f756878ed52e807a1e0106e1660e59
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535964"
---
# <a name="manage-a-private-endpoint-connection"></a>管理专用终结点连接

Azure 专用链接基于一个审批模型工作，其中，专用链接服务使用者可以请求连接到服务提供商以使用该服务。 

然后，服务提供商可以决定是否允许使用者进行连接。 Azure 专用链接可允许服务提供商管理其资源上的专用终结点连接。 

本文提供了有关如何管理专用终结点连接的说明。

![管理专用终结点](media/manage-private-endpoint/manage-private-endpoint.png)

专用链接服务使用者可以从两种连接审批方法中进行选择：

- 自动：如果服务使用者对服务提供商资源具有 Azure 基于角色的访问控制权限，则使用者可以选择自动审批方法。 当请求到达服务提供商资源时，服务提供程序不需要执行任何操作，并且将自动批准连接。 

- 手动：如果服务使用者对服务提供商资源没有 Azure 基于角色的访问控制权限，则使用者可以选择手动审批方法。 连接请求会以“挂起”状态出现在服务资源上。 服务提供商必须手动批准请求，然后才能建立连接。 在手动情况下，服务使用者还可以指定包含请求的消息来向服务提供商提供更多上下文。 服务提供商可以为所有专用终结点连接选择以下选项：**已批准**、**拒绝**、**删除**。

下表显示了专用终结点的各种服务提供商操作和产生的连接状态。 服务提供商可在稍后更改连接状态，且无需使用者干预。 此操作将更新使用者端的终结点状态。 


| 服务提供商操作  | 服务使用者专用终结点状态 | 说明 |
|---------|---------|---------|
| 无    |    挂起     |    连接是手动创建的，正等待专用链接资源所有者审批。       |
| 审批    |  已批准       |  连接已自动或手动批准，随时可供使用。     |
| 拒绝     | 已拒绝        | 连接已被专用链接资源所有者拒绝。        |
| 删除    |  已断开连接       | 专用链接资源所有者已将连接删除，专用终结点仅供参考，应将其删除以清理资源。        |

## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>管理 Azure PaaS 资源上的专用终结点连接

要管理 Azure PaaS 资源上的专用终结点连接，Azure 门户首选方法。 

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“专用链接”。 在搜索结果中，选择“专用链接”。

3. 在“专用链接中心”中，选择“专用终结点”或“专用链接服务”。

4. 对于每个终结点，可以查看与其关联的专用终结点连接数。 你可以根据需要筛选资源。

5. 选择专用终结点。  在列出的连接下，选择要管理的连接。 

6. 可以通过从顶部的选项中进行选择来更改连接的状态。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>管理客户/合作伙伴拥有的专用链接服务上的专用终结点连接

Azure PowerShell 和 Azure CLI 是用于管理 Microsoft 合作伙伴服务或客户拥有的服务上的专用终结点连接的首选方法。 
 
### <a name="powershell"></a>PowerShell 
  
可以使用以下 PowerShell 命令来管理专用终结点连接。  

#### <a name="get-private-link-connection-states"></a>获取专用链接连接状态 

输入 [Get-AzPrivateEndpointConnection](/powershell/module/az.network/get-azprivateendpointconnection)，即可获取专用终结点连接及其状态。  

```azurepowershell
Get-AzPrivateEndpointConnection -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
```
 
#### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接 
 
输入 [Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) cmdlet，即可批准专用终结点连接。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>拒绝专用终结点连接 
 
输入 [Deny-AzPrivateEndpointConnection](/powershell/module/az.network/deny-azprivateendpointconnection) cmdlet，即可拒绝专用终结点连接。 

```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```

#### <a name="remove-private-endpoint-connection"></a>删除专用终结点连接 
 
输入 [Remove-AzPrivateEndpointConnection](/powershell/module/az.network/remove-azprivateendpointconnection) cmdlet，即可审批专用终结点连接。 

```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
### <a name="azure-cli"></a>Azure CLI 
 
#### <a name="get-private-link-connection-states"></a>获取专用链接连接状态 

输入 [az network private-endpoint-connection show](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_show)，即可获取专用终结点连接及其状态。  

```azurecli
  az network private-endpoint-connection show \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```
 
#### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接 
 
输入 [az network private-endpoint-connection approve](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_approve) cmdlet，即可批准专用终结点连接。 
 
```azurecli
  az network private-endpoint-connection approve \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```
 
#### <a name="deny-private-endpoint-connection"></a>拒绝专用终结点连接 
 
输入 [az network private-endpoint-connection reject](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_reject) cmdlet，即可拒绝专用终结点连接。 

```azurecli
  az network private-endpoint-connection reject \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```

#### <a name="remove-private-endpoint-connection"></a>删除专用终结点连接 
 
输入 [az network private-endpoint-connection delete](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_delete) cmdlet，即可删除专用终结点连接。 

```azurecli
  az network private-endpoint-connection delete \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>后续步骤
- [了解专用终结点](private-endpoint-overview.md)
 
