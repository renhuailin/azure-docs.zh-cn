---
title: 创建公共 IP - Azure CLI
description: 了解如何使用 Azure CLI 创建公共 IP
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 70fb502e45a6cdcc196f5fb97ee4c8c59ba6c27d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223527"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>快速入门：使用 Azure CLI 创建公共 IP 地址

本文介绍了如何使用 Azure CLI 来创建公共 IP 地址。 若要详细了解这可能关联到哪些资源，以及基本 SKU 和标准 SKU 之间的差异和其他相关信息，请参阅[公共 IP 地址](./public-ip-addresses.md)。  对于此示例，我们只重点介绍 IPv4 地址；有关 IPv6 地址的详细信息，请参阅[适用于 Azure VNet 的 IPv6](./ipv6-overview.md)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

在 **eastus2** 位置，使用 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)创建资源组。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>创建公共 IP

---
# <a name="standard-sku---using-zones"></a>[**标准 SKU-使用区域**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>以下命令适用于 API 版本 2020-08-01 或更高版本。  有关当前正在使用的 API 版本的详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

使用 [az network 公共 ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)在 **myResourceGroup** 中创建名为 **myStandardZRPublicIP** 的标准区域冗余公共 ip 地址。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> 对于早于2020-08-01 的 API 版本，请在上面运行命令，而无需指定区域参数来创建区域冗余的 IP 地址。 
>

若要在 **myResourceGroup** 中区域2名为 **myStandardZonalPublicIP** 的中创建标准区域性公共 IP 地址，请使用以下命令：

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

请注意，上述区域选项仅适用于具有 [可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选择。

# <a name="standard-sku---no-zones"></a>[标准 SKU - 无区域](#tab/option-create-public-ip-standard)

>[!NOTE]
>以下命令适用于 API 版本 2020-08-01 或更高版本。  有关当前正在使用的 API 版本的详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

使用 [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 在 myResourceGroup 中创建一个标准公共 IP 地址作为名为 myStandardPublicIP 的非区域性资源 。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
此选择在所有区域均有效，并且在没有[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中，这是标准公共 IP 地址的默认选择。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用 [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 在 myResourceGroup 中创建名为“myBasicPublicIP”的基本静态公共 IP 地址 。  基本公共 IP 没有可用性区域的概念。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
如果可以接受 IP 地址随时间的推移发生更改，可以通过将 allocation-method 更改为“Dynamic”来选择动态 IP 分配。

---

## <a name="additional-information"></a>其他信息 

若要更详细地了解以上所列各个变量，请参阅[管理公共 IP 地址](./virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](./associate-public-ip-address-vm.md#azure-portal)。
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。