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
ms.openlocfilehash: c1ac3f2fa1ef3f1a24077064ad3ad3f3c30c5f3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048351"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建公共 IP 地址

本文介绍如何使用 Azure 门户创建公共 IP 地址资源。 

若要详细了解此公共 IP 可关联到的资源以及基本和标准 SKU 之间的区别，请查看[公共 IP 地址](./public-ip-addresses.md)。 

本文重点介绍 IPv4 地址。 有关 IPv6 地址的详细信息，请查看[适用于 Azure VNet 的 IPv6](./ipv6-overview.md)。

# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-create-public-ip-standard-zones)

按照以下步骤，创建一个名为“myStandardZRPublicIP”的标准区域冗余公共 IP 地址。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。  
3. 在搜索框中输入“公共 IP 地址”。 在搜索结果中，选择“公共 IP 地址”。
4. 在“公共 IP 地址”页面中，选择“创建” 。
5. 在“创建公共 IP 地址”页面上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“标准”         |
    | 层*                   | 选择“区域”         |
    | 名称                    | 输入 myStandardZRPublicIP          |
    | IP 地址分配   | 注意此选择已锁定为“静态”                                        |
    | 路由首选项      | 保留默认值“Microsoft 网络”。 </br> 有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](./routing-preference-overview.md)。 |
    | 空闲超时(分钟)  | 保留默认值“4”。        |
    | DNS 名称标签          | 将该值保留为空白。    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，输入 myResourceGroup 。 </br> 选择“确定”。 |
    | 位置                | 选择“美国东部 2”      |
    | 可用性区域       | 选择“区域冗余”、“无区域”或选择特定区域（请参阅下面的注释） |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="在 Azure 门户中创建标准 IP 地址" border="false":::

> [!NOTE]
> 可在具有[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中使用这些选项。 </br>
你可在这些区域中选择特定区域，但区域故障不可复原。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

\* 表示层与[跨区域负载均衡器](../load-balancer/cross-region-overview.md)功能关联，目前为预览版。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

在本部分中，创建一个名为 myBasicPublicIP 的基本公共 IP 地址。 

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
    | IP 地址分配   | 选择“静态”（请参阅下面的注释）                                     |
    | 空闲超时(分钟)  | 保留默认值“4”。       |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，输入 myResourceGroup 。 </br> 选择“确定”。 |
    | 位置                | 选择“美国东部 2”      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="在 Azure 门户中创建标准 IP 地址" border="false":::

如果可接受 IP 地址随时间的推移发生更改，可选择动态 IP 分配。

---

## <a name="additional-information"></a>其他信息 

若要详细了解上面列出的各个字段，请参阅[管理公共 IP 地址](./virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](./associate-public-ip-address-vm.md#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。