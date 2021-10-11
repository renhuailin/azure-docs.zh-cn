---
title: 快速入门：创建公共 IP 地址前缀 - Azure CLI
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure CLI 创建公共 IP 地址。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0b72eb4e70fb80602b352c32d6ffbde1cf4336da
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368790"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建公共 IP 地址前缀

了解公共 IP 地址前缀，以及如何创建、更改和删除此类地址。 公共 IP 地址前缀是一系列连续的标准 SKU 公共 IP 地址。 

创建公共 IP 地址资源时，可以从前缀分配一个静态公共 IP 地址，并将该地址关联到虚拟机、负载均衡器或其他资源。 有关详细信息，请参阅[公共 IP 地址前缀概述](public-ip-address-prefix.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

在 eastus2 位置，使用名为“QuickStartCreateIPPrefix-rg”的 [az group create](/cli/azure/group#az_group_create) 创建资源组 。

```azurecli-interactive
  az group create \
    --name QuickStartCreateIPPrefix-rg \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>创建公共 IP 地址前缀

在本部分中，你将使用 Azure PowerShell 创建区域冗余、区域和非区域公共 IP 前缀。 

示例中的前缀为：

* **IPv4** - /28（16 个地址）

* **IPv6** - /124（16 个地址）

有关可用前缀大小的详细信息，请参阅[前缀大小](public-ip-address-prefix.md#prefix-sizes)。

通过名为 myPublicIpPrefix 的 [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) 在 eastus2 位置创建公共 IP 前缀 。

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**区域冗余 IPv4 前缀**](#tab/ipv4-zone-redundant)

若要创建 IPv4 公共 IP 前缀，请在 `--version` 参数中输入“IPv4” 。 若要创建区域冗余 IPv4 前缀，请在 `--zone` 参数中输入“1、2、3” 。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

# <a name="zonal-ipv4-prefix"></a>[**区域 IPv4 前缀**](#tab/ipv4-zonal)

若要创建 IPv4 公共 IP 前缀，请在 `--version` 参数中输入“IPv4” 。 在 `--zone` 参数中输入“2”，以在区域 2 中创建区域 IP 前缀 。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

# <a name="non-zonal-ipv4-prefix"></a>[**非区域性 IPv4 前缀**](#tab/ipv4-non-zonal)

若要创建 IPv4 公共 IP 前缀，请在 `--version` 参数中输入“IPv4” 。 删除 `--zone` 参数以创建非区域 IP 前缀。

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4
```

在命令中删除 `--zone` 参数对所有区域有效。  

对于不带[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除 `--zone` 参数。

---



## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**区域冗余 IPv6 前缀**](#tab/ipv6-zone-redundant)

若要创建 IPv4 公共 IP 前缀，请在 `--version` 参数中输入“IPv6” 。 若要创建区域冗余 IPv6 前缀，请在 `--zone` 参数中输入“1、2、3” 。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

# <a name="zonal-ipv6-prefix"></a>[**区域 IPv6 前缀**](#tab/ipv6-zonal)

若要创建 IPv6 公共 IP 前缀，请在 `--version` 参数中输入“IPv6” 。 在 `--zone` 参数中输入“2”，以在区域 2 中创建区域 IP 前缀 。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

# <a name="non-zonal-ipv6-prefix"></a>[**非区域性 IPv6 前缀**](#tab/ipv6-non-zonal)

若要创建 IPv6 公共 IP 前缀，请在 `--version` 参数中输入“IPv6” 。 删除 `--zone` 参数以创建非区域 IP 前缀。

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6
```

在命令中删除 `--zone` 参数对所有区域有效。  

对于不带[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除 `--zone` 参数。

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>从前缀创建静态公共 IP 地址

在创建前缀后，必须从前缀创建静态 IP 地址。 在本部分，你将从前面创建的前缀创建一个静态 IP 地址。

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 在 myPublicIpPrefix 前缀中创建一个公共 IP 地址。

# <a name="ipv4-address"></a>[**IPv4 地址**](#tab/ipv4-address)

若要创建 IPv4 公共 IP 地址，请在 `--version` 参数中输入“IPv4” 。

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

# <a name="ipv6-address"></a>[**IPv6 地址**](#tab/ipv6-address)

若要创建 IPv6 公共 IP 前缀，请在 `--version` 参数中输入“IPv6” 。

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

---

>[!NOTE]
>仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](public-ip-addresses.md#public-ip-addresses)。

## <a name="delete-a-prefix"></a>删除前缀

本部分介绍如何删除前缀。

若要删除公共 IP 前缀，请使用 [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete)。

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg
```

>[!NOTE]
>如果前缀中的地址关联到公共 IP 地址资源，必须先删除公共 IP 地址资源。 请参阅[删除公共 IP 地址](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)。

## <a name="clean-up-resources"></a>清理资源

在本文中，你创建了一个公共 IP 前缀，并从该前缀创建了一个公共 IP。 


处理完公共 IP 前缀后，请使用 [az group delete](/cli/azure/group#az_group_delete) 删除资源组及其包含的所有资源。

```azurecli-interactive
  az group delete \
    --name QuickStartCreateIPPrefix-rg
```

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何使用 Azure PowerShell 创建公共 IP 前缀：
> [!div class="nextstepaction"]
> [使用 Azure CLI 创建公共 IP](create-public-ip-cli.md)
