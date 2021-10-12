---
title: 快速入门：创建公共 IP - Azure CLI
titleSuffix: Azure Virtual Network
description: 了解如何使用 Azure CLI 创建公共 IP
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 8d8b1525c910592cb9cc35ae1e08da7e9e24e30b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369049"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建公共 IP 地址

本快速入门介绍如何创建 Azure 公共 IP 地址。 Azure 中的公共 IP 地址用于以公共方式连接到 Azure 资源。 公共 IP 地址有两种可用 SKU：基本和标准。 公共 IP 地址可分为两层：区域和全局。 公共 IP 地址的路由首选项在创建时设置。 可用的选项包括 Internet 路由和 Microsoft 网络路由。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 版本 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 在“eastus2”位置创建名为“QuickStartCreateIP-rg”的资源组 。

```azurecli-interactive
  az group create \
    --name QuickStartCreateIP-rg \
    --location eastus2
```

# <a name="standard-sku"></a>[**标准 SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>对于生产工作负荷，建议使用标准 SKU 公共 IP。  有关 SKU 的详细信息，请参阅[公共 IP 地址](public-ip-addresses.md)。
>
>以下命令适用于 API 2020-08-01 或更高版本。   有关当前正在使用的 API 版本的详细信息，请参阅[资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 在 QuickStartCreateIP-rg 中创建名为 myStandardPublicIP 的标准区域冗余公共 IPv4 地址。  

若要创建 IPv6 地址，请将 `--version` 参数修改为“IPv6” 。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> 对于 2020-08-01 之前的 API 版本，请执行命令而不指定 `--zone` 参数，以创建区域冗余 IP 地址。 
>

# <a name="basic-sku"></a>[**基本 SKU**](#tab/create-public-ip-basic)

在本部分，你将创建基本公共 IP。 基本公共 IP 不支持可用性区域。

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 在 QuickStartCreateIP-rg 中创建名为 myBasicPublicIP 的基本静态公共 IPv4 地址。

若要创建 IPv6 地址，请将 `--version` 参数修改为“IPv6” 。 

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
如果可接受 IP 地址随时间的推移发生更改，可通过将 `--allocation-method` 更改为 Dynamic 来选择动态 IP 分配。  

>[!NOTE]
> 基本 IPv6 地址必须始终为“Dynamic”。

---

## <a name="create-a-zonal-or-no-zone-ip-address"></a>创建区域或非区域 IP 地址

此部分介绍如何创建区域或非区域公共 IP 地址。

# <a name="zonal"></a>[**局部区域**](#tab/create-public-ip-zonal)

若要在 QuickStartCreateIP-rg 的区域 2 中创建名为 myStandardPublicIP 的标准区域公共 IPv4 地址，请使用以下命令。

若要创建 IPv6 地址，请将 `--version` 参数修改为“IPv6” 。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rgLB \
    --name myStandardPublicIP-zonal \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

# <a name="non-zonal"></a>[非区域](#tab/create-public-ip-non-zonal)

在本部分，你将创建一个非区域性的 IP 地址。  

>[!NOTE]
>以下命令适用于 API 版本 2020-08-01 或更高版本。  有关当前正在使用的 API 版本的详细信息，请参阅[资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 在 QuickStartCreateIP-rg 中创建一个标准公共 IPv4 地址作为名为 myStandardPublicIP-nozone 的非区域资源。 

若要创建 IPv6 地址，请将 `--version` 参数修改为“IPv6” 。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-nozone \
    --version IPv4 \
    --sku Standard
```
在命令中删除 `--zone` 参数的操作对所有区域有效。  

对于没有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除 `--zone` 参数。

---

## <a name="routing-preference-and-tier"></a>路由首选项和层

标准 SKU 静态公共 IPv4 地址支持路由首选项或全局层功能。

# <a name="routing-preference"></a>[路由首选项](#tab/routing-preference)

默认情况下，公共 IP 地址的路由首选项设置为“Microsoft 网络”，该网络通过 Microsoft 的全球广区网络向用户传送流量。  

相比传输 ISP 网络，选择“Internet”可最大限度减少 Microsoft 网络漫游，从而按成本优化费率传送流量。  

有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](routing-preference-overview.md)。

此命令使用类型为 Internet 的路由首选项创建新的标准区域冗余公用 IPv4 地址：

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

# <a name="tier"></a>[**层**](#tab/tier)

公共 IP 地址与单个区域相关联。 全局层可跨多个区域扩展 IP 地址。 跨区域负载均衡器前端需要全局层。  

有关详细信息，请参阅[跨区域负载均衡器](../../load-balancer/cross-region-overview.md)。

以下命令将创建一个全局 IPv4 地址。 此地址可与跨区域负载均衡器的前端相关联。

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>全局层地址不支持可用性区域。

---

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请按以下步骤删除公共 IP 地址：

1. 在门户顶部的搜索框中输入“资源组”。

2. 在搜索结果中选择“资源组”。

3. 选择“QuickStartCreateIP-rg”。

4. 选择“删除资源组”。

5. 在“键入资源组名称”中输入“QuickStartCreateIP-rg”，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建公共 IP 前缀：
> [!div class="nextstepaction"]
> [使用 Azure CLI 创建公共 IP 前缀](create-public-ip-prefix-cli.md)
