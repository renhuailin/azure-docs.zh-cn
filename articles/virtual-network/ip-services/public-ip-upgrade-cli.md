---
title: 升级公共 IP 地址 - Azure CLI
description: 本文介绍如何使用 Azure CLI 升级基本 SKU 公共 IP 地址。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 6e6f348cd8836363e8f3b0023669897fc2123650
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367554"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-cli"></a>使用 Azure CLI 升级公共 IP 地址

Azure 公共 IP 地址是使用基本或标准 SKU 创建的。 SKU 决定了 IP 地址的功能，包括分配方法、功能支持以及它们可以关联到的资源。 

本文介绍如何使用 Azure CLI 将静态基本 SKU 公共 IP 地址升级到标准 SKU。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 订阅中的静态基本 SKU 公共 IP 地址。 有关详细信息，请参阅[创建公共 IP 地址 - Azure 门户](../../virtual-network/create-public-ip-portal.md#create-a-basic-sku-public-ip-address)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本教程需要 Azure CLI 版本 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="upgrade-public-ip-address"></a>升级公共 IP 地址

在本部分，你将使用 Azure CLI 将静态基本 SKU 公共 IP 升级到标准 SKU。

```azurecli-interactive
az network public-ip update \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard

```
> [!NOTE]
> 要升级的基本公共 IP 必须是静态分配类型。 如果尝试升级动态分配的 IP 地址，会收到一条警告，指示无法升级 IP。

> [!WARNING]
> 将基本公共 IP 升级为标准 SKU 的过程不可逆。 从基本 SKU 升级到标准 SKU 的公共 IP 仍然没有保证的[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)。

## <a name="verify-upgrade"></a>验证升级

在本部分中，将验证公共 IP 地址现在是否为标准 SKU。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myBasicPublicIP \
  --query sku \
  --output tsv

```
该命令应显示“标准”。

## <a name="next-steps"></a>后续步骤

本文中，已经将基本 SKU 公共 IP 地址 升级为标准 SKU。

有关 Azure 中公共 IP 地址的详细信息，请参阅：

- [Azure 中的公共 IP 地址](public-ip-addresses.md)
- [创建公共 IP - Azure 门户](../../virtual-network/create-public-ip-portal.md)

