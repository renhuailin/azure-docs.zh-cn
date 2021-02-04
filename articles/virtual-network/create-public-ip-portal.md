---
title: 创建公共 IP - Azure 门户
description: 了解如何在 Azure 门户中创建公共 IP
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
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550229"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建公共 IP 地址

本文介绍了如何使用 Azure 门户来创建公共 IP 地址资源。 若要详细了解这可能关联到哪些资源，以及基本 SKU 和标准 SKU 之间的差异和其他相关信息，请参阅[公共 IP 地址](./public-ip-addresses.md)。  对于此示例，我们只重点介绍 IPv4 地址；有关 IPv6 地址的详细信息，请参阅[适用于 Azure VNet 的 IPv6](./ipv6-overview.md)。

# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-create-public-ip-standard-zones)

使用以下步骤创建名为 **myStandardZRPublicIP** 的标准区域冗余公共 IP 地址。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在“创建公共 IP 地址”页上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“标准”         |
    | 分层 (如显示 * )                   | 选择 **区域**         |
    | 名称                    | 输入 *myStandardZRPublicIP*          |
    | IP 地址分配   | 请注意，这将被锁定为 "静态"                                        |
    | 空闲超时(分钟)  | 将该值保留为“4”        |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，输入“myResourceGroup”，然后选择“确定”  |
    | 位置                | 选择“美国东部 2”      |
    | 可用性区域       | 选择 " **区域冗余**"、"无区域" 或 "选择特定区域" (参阅下面的注释)  |

请注意，这些只是包含 [可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选择。   (你还可以在这些区域中选择特定的区域，但不能恢复范围故障。 ) 有关可用性区域的详细信息，请参阅 [可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

\* = 层关联于 [跨区域负载均衡器](../load-balancer/cross-region-overview.md) 功能，目前为预览版。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用以下步骤创建名为“myBasicPublicIP”的基本静态公共 IP 地址。  基本公共 IP 没有可用性区域的概念。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在“创建公共 IP 地址”页上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                 |    
    | SKU                     | 选择“标准”         |
    | 名称                    | 输入“myBasicPublicIP”          |
    | IP 地址分配   | 选择“静态”（请参阅下面的注释）                                     |
    | 空闲超时(分钟)  | 将该值保留为“4”        |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，输入“myResourceGroup”，然后选择“确定”  |
    | 位置                | 选择“美国东部 2”      |

如果可以接受 IP 地址随时间的推移发生更改，可以选择动态 IP 分配。

---

## <a name="additional-information"></a>其他信息 

若要详细了解上面列出的各个字段，请参阅[管理公共 IP 地址](./virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](./associate-public-ip-address-vm.md#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
