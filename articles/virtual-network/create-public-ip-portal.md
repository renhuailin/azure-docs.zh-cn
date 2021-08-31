---
title: 创建公共 IP - Azure 门户
titleSuffix: Azure Virtual Network
description: 了解如何使用 Azure 门户创建公共 IP
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/04/2021
ms.author: allensu
ms.openlocfilehash: 6cf9e86222d98835ea5355440343df96794dbfd4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435034"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建公共 IP 地址

本文介绍如何使用 Azure 门户创建公共 IP 地址资源。 

若要详细了解此公共 IP 可关联到的资源以及基本和标准 SKU 之间的区别，请查看[公共 IP 地址](./public-ip-addresses.md)。 

## <a name="create-a-standard-sku-public-ip-address"></a>创建标准 SKU 公共 IP 地址

使用以下步骤创建名为“myStandardPublicIP”的标准公共 IPv4 地址。  

> [!NOTE]
>若要创建 IPv6 地址，请在“IP 版本”参数中选择“IPv6”。  如果你的部署要求双堆栈配置（IPv4 和 IPv6 地址），请选择“两者”。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“公共 IP”。

3. 在搜索结果中选择“公共 IP 地址”。

4. 选择“+ 新建”。 

5. 在“创建公共 IP 地址”中，输入或选择以下信息：

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”              |    
    | SKU                     | 选择“标准”         |
    | 层                   | 选择“区域”    </br> 有关详细信息，请参阅[路由首选项和层](#routing-preference-and-tier)     |
    | 名称                    | 输入 myStandardPublicIP          |
    | IP 地址分配   | 锁定为“静态”                |
    | 路由首选项     | 选择“Microsoft 网络”。 </br> 有关详细信息，请参阅[路由首选项和层](#routing-preference-and-tier) |
    | 空闲超时(分钟)  | 保留默认值“4”。        |
    | DNS 名称标签          | 将该值保留为空白。    |
    | 订阅            | 选择订阅   |
    | 资源组          | 选择“新建”，输入 myResourceGroup 。 </br> 选择“确定”。 |
    | 位置                | 选择“(US)美国东部 2”     |
    | 可用性区域       | 选择“无区域” |

6. 选择“创建”。

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="在 Azure 门户中创建标准 IP 地址" border="false":::

> [!NOTE]
> 在提供[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

> [!NOTE]
> IPv6 目前不支持路由首选项或跨区域负载平衡（全局层）。

## <a name="create-a-basic-sku-public-ip-address"></a>创建基本 SKU 公共 IP 地址

在本部分中，创建一个名为“myBasicPublicIP”的基本公共 IPv4 地址。 

> [!NOTE]
> 基本公共 IP 不支持可用性区域。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。  
3. 在搜索框中输入“公共 IP 地址”。 在搜索结果中，选择“公共 IP 地址”。
4. 在“公共 IP 地址”页面中，选择“创建” 。
5. 在“创建公共 IP 地址”页面上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“基本”         |
    | 名称                    | 输入“myBasicPublicIP”          |
    | IP 地址分配   | 选择“静态”            |
    | 空闲超时(分钟)  | 保留默认值“4”。       |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，输入 myResourceGroup 。 </br> 选择“确定”。 |
    | 位置                | 选择“(US)美国东部 2”      |

6. 选择“创建”。

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="在 Azure 门户中创建基本 IP 地址" border="false":::

如果可接受 IP 地址随时间的推移发生更改，可通过将 AllocationMethod 更改为 Dynamic 来选择动态 IP 分配 。 

---

## <a name="routing-preference-and-tier"></a>路由首选项和层

标准 SKU 静态公共 IPv4 地址支持路由首选项或全局层功能。 创建新的公共 IP 地址时，请选择路由首选项和层。

### <a name="routing-preference"></a>路由首选项

默认情况下，公共 IP 地址的路由首选项设置为“Microsoft 网络”，该网络通过 Microsoft 的全球广域网向用户传送流量。  

相比传输 ISP 网络，选择“Internet”可最大限度减少 Microsoft 网络漫游，从而按成本优化费率传送流量。  

有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](./routing-preference-overview.md)。

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="在 Azure 门户中配置路由首选项" border="false":::

### <a name="tier"></a>层

公共 IP 地址与单个区域相关联。 全局层使一个 IP 地址跨越多个区域。 跨区域负载均衡器的前端需要全局层。  

有关详细信息，请参阅[跨区域负载均衡器](../load-balancer/cross-region-overview.md)。

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="在 Azure 门户中配置层" border="false":::

## <a name="additional-information"></a>其他信息 

若要详细了解上面列出的各个字段，请参阅[管理公共 IP 地址](./virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](./associate-public-ip-address-vm.md#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。