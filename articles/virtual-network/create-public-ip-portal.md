---
title: 创建公共 IP - Azure 门户
description: 了解如何在 Azure 门户中创建公共 IP
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 5c5650d896442f10846e16903a1231010d032a44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675211"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建公共 IP 地址

本文介绍如何使用 Azure 门户创建公共 IP 地址资源。 

有关此公共 IP 可以关联到的资源以及基本和标准 Sku 之间的差异的详细信息，请参阅 [公共 ip 地址](./public-ip-addresses.md)。 

本文重点介绍 IPv4 地址。 有关 IPv6 地址的详细信息，请参阅 [适用于 Azure VNet 的 IPv6](./ipv6-overview.md)。

# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-create-public-ip-standard-zones)

使用以下步骤创建名为 **myStandardZRPublicIP** 的标准区域冗余公共 IP 地址。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。  
3. 在搜索框中，输入 " **公共 IP 地址**"。 在搜索结果中选择 " **公共 IP 地址** "。
4. 在 " **公共 IP 地址** " 页上，选择 " **创建**"。
5. 在 " **创建公共 IP 地址** " 页上，输入或选择以下信息： 

    | 设置                 | Value                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“标准”         |
    | 单层                   | 选择 **区域**         |
    | 名称                    | 输入 **myStandardZRPublicIP**          |
    | IP 地址分配   | 注意此选择被锁定为 "静态"                                        |
    | 路由首选项      | 保留 " **Microsoft 网络**" 的默认值。 </br> 有关路由首选项的详细信息，请参阅 [什么是路由首选项 (预览) ？](./routing-preference-overview.md)。 |
    | 空闲超时(分钟)  | 保留默认值 **4**。        |
    | DNS 名称标签          | 将值保留为空。    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择 " **新建**"，输入 **myResourceGroup**。 </br> 选择“确定”。 |
    | 位置                | 选择“美国东部 2”      |
    | 可用性区域       | 选择 " **区域冗余**"、"无区域" 或 "选择特定区域" (参阅下面的注释)  |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="在 Azure 门户中创建标准 IP 地址" border="false":::

> [!NOTE]
> 这些选择在具有 [可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中是有效的。 </br>
你可以在这些区域中选择特定区域，但不能恢复范围故障。 </br> 有关可用性区域的详细信息，请参阅 [可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

\* = 层关联于 [跨区域负载均衡器](../load-balancer/cross-region-overview.md) 功能，目前为预览版。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

在本部分中，创建一个名为 **myBasicPublicIP** 的基本公共 IP 地址。 

> [!NOTE]
> 基本公共 Ip 不支持可用性区域。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。  
3. 在搜索框中，输入 " **公共 IP 地址**"。 在搜索结果中选择 " **公共 IP 地址** "。
4. 在 " **公共 IP 地址** " 页上，选择 " **创建**"。
5. 在 " **创建公共 IP 地址** " 页上，输入或选择以下信息： 

    | 设置                 | Value                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“基本”         |
    | 名称                    | 输入“myBasicPublicIP”          |
    | 路由首选项      | 保留 " **Microsoft 网络**" 的默认值。 </br> 有关路由首选项的详细信息，请参阅 [什么是路由首选项 (预览) ？](./routing-preference-overview.md)。 |
    | IP 地址分配   | 选择 **静态** (请参阅下面的注释)                                      |
    | 空闲超时(分钟)  | 保留默认值 **4**。       |
    | DNS 名称标签          | 保留值为空    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择 " **新建**"，输入 **myResourceGroup**。 </br> 选择“确定”。 |
    | 位置                | 选择“美国东部 2”      |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="在 Azure 门户中创建标准 IP 地址" border="false":::

如果 IP 地址可在一段时间内更改，则可以选择 **动态** ip 分配。

---

## <a name="additional-information"></a>其他信息 

有关上面所列各个字段的详细信息，请参阅 [管理公共 IP 地址](./virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](./associate-public-ip-address-vm.md#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。