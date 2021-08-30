---
title: 创建、更改或删除 Azure 公共 IP 地址前缀
titlesuffix: Azure Virtual Network
description: 了解公共 IP 地址前缀以及如何创建、更改或删除它们。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 085bc186b8a2290919820e35ff510346f9ab3bfc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438268"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>创建、更改或删除公共 IP 地址前缀

了解公共 IP 地址前缀。 公共 IP 地址前缀是一系列连续的标准 SKU 公共 IP 地址。  创建公共 IP 地址资源时，可以从前缀分配一个静态公共 IP 地址，并将该地址与 Azure 资源关联。 有关详细信息，请参阅[公共 IP 地址前缀概述](public-ip-address-prefix.md)。

## <a name="create-a-public-ip-address-prefix"></a>创建公共 IP 地址前缀

下面将详细介绍创建公共 IP 前缀时的参数。

   |设置|必需？|详细信息|
   |---|---|---|
   |订阅|是|必须与要将公共 IP 地址关联到的资源位于同一[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
   |资源组|是|可与要将公共 IP 地址关联到的资源位于相同或不同的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
   |名称|是|名称在所选资源组中必须唯一。|
   |区域|是|必须位于与公共 IP 地址相同的[区域](https://azure.microsoft.com/regions)，你将从该范围分配地址。|
   |IP 版本|是| 前缀的 IP 版本（v4 或 v6）。
   |前缀大小|是| 所需的前缀大小。 包含 16 个 IP 地址的范围（v4 为 /28，v6 为 /124）是默认值。

或者，你可以使用下面的 CLI 和 PowerShell 命令来创建公共 IP 地址前缀。

命令

|工具|命令|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>在具有可用性区域的区域中，可以使用 PowerShell 或 CLI 命令创建公共 IP 地址前缀，如：与特定区域关联的非区域性，或使用区域冗余。  对于 API 版本 2020-08-01 或更高版本，如果未提供区域参数，将创建一个非区域性公共 IP 地址前缀。 对于早于 2020-08-01 的 API 版本，将创建区域冗余公共 IP 地址前缀。 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>从前缀创建静态公共 IP 地址

以下部分将详细介绍从前缀创建静态公共 IP 地址所需的参数。

   |设置|必需？|详细信息|
   |---|---|---|
   |名称|是|公共 IP 地址的名称在所选的资源组中必须唯一。|
   |空闲超时(分钟)|否|在不依赖客户端发送保持连接消息的情况下，TCP 或 HTTP 连接持续打开的分钟数。 |
   |DNS 名称标签|否|必须在创建该名称的 Azure 区域（跨所有订阅和所有客户）中保持唯一。 Azure 会在其 DNS 中自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 将默认子网 location. cloudappp.azure.com 追加到你提供的名称后面，以创建完全限定的 DNS 名称。 有关详细信息，请参阅[将 Azure DNS 与 Azure 公共 IP 地址配合使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|

或者，可以使用下面的 CLI 和 PowerShell 命令与 --public-ip-prefix (CLI) 和 -PublicIpPrefix (PowerShell) 参数，通过前缀创建公共 IP 地址资源 。 

|工具|命令|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

>[!NOTE]
>仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。

## <a name="view-or-delete-a-prefix"></a>查看或删除前缀

要查看或删除前缀，可在 Azure CLI 和 Azure PowerShell 中使用以下命令。

命令

|工具|命令|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) 用于列出公共 IP 地址<br>[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) 用于显示设置<br> [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) 用于更新<br>[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) 用于删除|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) 用于检索公共 IP 地址对象并查看其设置<br>[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) 用于更新设置<br> [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) 用于删除|

## <a name="permissions"></a>权限

为了获取公共 IP 地址前缀的管理权限，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色。 

| 操作                                                            | 名称                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | 读取公共 IP 地址前缀                                |
| Microsoft.Network/publicIPPrefixes/write                          | 创建或更新公共 IP 地址前缀                    |
| Microsoft.Network/publicIPPrefixes/delete                         | 删除公共 IP 地址前缀                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | 从前缀创建公共 IP 地址 |

## <a name="next-steps"></a>后续步骤

- 了解使用[公共 IP 前缀](public-ip-address-prefix.md)的方案和好处
