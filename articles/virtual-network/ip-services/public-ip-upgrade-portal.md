---
title: 升级公共 IP 地址 - Azure 门户
description: 本文介绍如何使用 Azure 门户升级基本 SKU 公共 IP 地址。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: f436cd2d113909a65b62891f9fbfb5965d06a981
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367549"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户升级公共 IP 地址

Azure 公共 IP 地址是使用基本或标准 SKU 创建的。 SKU 决定了地址功能，包括分配方法、功能支持和可以关联的资源。 

本文介绍如何在 Azure 门户中将静态基本 SKU 公共 IP 地址升级为标准 SKU。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 订阅中的静态基本 SKU 公共 IP 地址。 有关详细信息，请参阅[创建公共 IP 地址 - Azure 门户](../../virtual-network/create-public-ip-portal.md#create-a-basic-sku-public-ip-address)。

## <a name="upgrade-public-ip-address"></a>升级公共 IP 地址

在本部分，将登录到 Azure 门户，然后将静态基本 SKU 公共 IP 升级为标准 SKU。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“公共 IP”。

3. 在搜索结果中选择“公共 IP 地址”。

4. 在“公共 IP 地址”中，选择“myBasicPublicIP”或要升级的 IP 地址 。

5. 选择“myBasicPublicIP”中概述部分顶部的升级横幅。

    :::image type="content" source="./media/public-ip-upgrade-portal/upgrade-ip-portal.png" alt-text="在 Azure 门户中升级基本 IP 地址" border="true":::

    > [!NOTE]
    > 要升级的基本公共 IP 必须具有静态分配类型。 如果尝试升级动态分配的 IP 地址，会收到一条警告，指示无法升级 IP。

6.  选中“我确认”复选框。 选择“升级”。

    > [!WARNING]
    > 将基本公共 IP 升级为标准 SKU 的过程不可逆。 从基本 SKU 升级到标准 SKU 的公共 IP 仍然没有保证的[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)。

## <a name="verify-upgrade"></a>验证升级

在本部分中，将验证公共 IP 地址现在是否为标准 SKU。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“公共 IP”。

3. 在搜索结果中选择“公共 IP 地址”。

4. 在“公共 IP 地址”中，选择“myBasicPublicIP”或已升级的 IP 地址 。

5. 验证“概述”部分中的 SKU 是否显示为“标准” 。

    :::image type="content" source="./media/public-ip-upgrade-portal/verify-upgrade-ip.png" alt-text="验证公共 IP 地址为标准 SKU。" border="true":::

## <a name="next-steps"></a>后续步骤

在本文中，将基本 SKU 公共 IP 地址升级为了标准 SKU。

有关 Azure 中公共 IP 地址的详细信息，请参阅：

- [Azure 中的公共 IP 地址](public-ip-addresses.md)
- [创建公共 IP - Azure 门户](../../virtual-network/create-public-ip-portal.md)

