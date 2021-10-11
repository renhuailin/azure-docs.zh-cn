---
title: 快速入门：创建公共 IP 地址 - Azure 门户
titleSuffix: Azure Virtual Network
description: 本快速入门介绍如何创建标准或基本的 SKU 公共 IP 地址。 还将介绍路由首选项和层级。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: ea18353a787918cbf0abe491b1a2437b9a7d3d4a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369160"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建公共 IP 地址

本快速入门介绍如何创建 Azure 公共 IP 地址。 Azure 中的公共 IP 地址用于以公共方式连接到 Azure 资源。 公共 IP 地址有两种可用 SKU：基本和标准。 公共 IP 地址可分为两层：区域和全局。  公共 IP 地址的路由首选项是在创建时设置的。 可用的选项包括 Internet 路由和 Microsoft 网络路由。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

---

# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-1-create-public-ip-standard)

>[!NOTE]
>对于生产工作负载，建议使用标准 SKU 公共 IP。  有关 SKU 的详细信息，请参阅[公共 IP 地址](public-ip-addresses.md)。

## <a name="create-a-standard-sku-public-ip-address"></a>创建标准公共 IP 地址

按照以下步骤，创建一个名为 myStandardPublicIP 的标准公共 IPv4 地址。 

> [!NOTE]
>若要创建 IPv6 地址，请选择“IPv6”作为“IP 版本”参数。 。 如果部署需要双堆栈配置（IPv4 和 IPv6 地址），请选择“两者”。

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中选择“公共 IP 地址”。

3. 选择“+ 新建”。 

4. 在“创建公共 IP 地址”中，输入或选择以下信息：

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”              |    
    | SKU                     | 选择“标准”         |
    | 层                   | 选择“区域”     |
    | 名称                    | 输入 myStandardPublicIP          |
    | IP 地址分配   | 锁定为“静态”                |
    | 路由首选项     | 选择“Microsoft 网络”。 |
    | 空闲超时(分钟)  | 保留默认值“4”。        |
    | DNS 名称标签          | 将该值保留为空白。    |
    | 订阅            | 选择订阅   |
    | 资源组          | 选择“新建”，然后输入“QuickStartCreateIP-rg” 。 </br> 选择“确定”。 |
    | 位置                | 选择“(US)美国东部 2”     |
    | 可用性区域       | 选择“无区域” |

5. 选择“创建”。

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="在 Azure 门户中创建标准 IP 地址的屏幕截图" border="false":::

> [!NOTE]
> 在提供[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../../availability-zones/az-overview.md)。

可以将上面创建的公共 IP 地址与 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机相关联。 使用“教程”页上的“CLI”部分：[将公共 IP 地址与虚拟机关联](../../virtual-network/associate-public-ip-address-vm.md#azure-cli)以将公共 IP 关联至 VM。 也可将上面创建的公共 IP 地址与 [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)相关联，只需将其分配给负载均衡器前端配置即可。 此公共 IP 地址充当负载均衡型虚拟 IP 地址 (VIP)。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-public-ip-basic)

>[!NOTE]
>对于生产工作负载，建议使用标准 SKU 公共 IP。  有关 SKU 的详细信息，请参阅[公共 IP 地址](public-ip-addresses.md)。

## <a name="create-a-basic-sku-public-ip-address"></a>创建基本 SKU 公共 IP 地址

在本部分中，创建一个名为“myBasicPublicIP”的基本公共 IPv4 地址。 

> [!NOTE]
> 基本公共 IP 不支持可用性区域。

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中选择“公共 IP 地址”。

3. 选择“+ 新建”。 

4. 在“创建公共 IP 地址”页面上，输入或选择以下信息： 

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”                |    
    | SKU                     | 选择“基本”         |
    | 名称                    | 输入“myBasicPublicIP”          |
    | IP 地址分配   | 选择“静态”            |
    | 空闲超时(分钟)  | 保留默认值“4”。       |
    | DNS 名称标签          | 将该值保留为空白    |
    | 订阅            | 选择订阅。   |
    | 资源组          | 选择“新建”，然后输入“QuickStartCreateIP-rg” 。 </br> 选择“确定”。 |
    | 位置                | 选择“(US)美国东部 2”      |

5. 选择“创建”。

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="在 Azure 门户中创建基本 IP 地址的屏幕截图" border="true":::

如果可以接受 IP 地址随时间而变这种情况，可将 AllocationMethod 更改为“Dynamic”来选择动态 IP 分配 。 

# <a name="routing-preference"></a>[**路由首选项**](#tab/option-1-create-public-ip-routing-preference)

本部分介绍如何通过 ISP 网络（“Internet”选项）配置公共 IP 地址的[路由首选项](routing-preference-overview.md)。 创建公共 IP 地址后，可将其与以下 Azure 资源相关联：

* 虚拟机
* 虚拟机规模集
* Azure Kubernetes 服务 (AKS)
* 面向 Internet 的负载均衡器
* 应用程序网关
* Azure 防火墙

默认情况下，对于所有 Azure 服务，公共 IP 地址的路由首选项设置为“Microsoft 全球网络”，并且可以与任何 Azure 服务相关联。

> [!NOTE]
>若要创建 IPv6 地址，请选择“IPv6”作为“IP 版本”参数。 。 如果部署需要双堆栈配置（IPv4 和 IPv6 地址），请选择“两者”。

## <a name="create-a-public-ip-with-internet-routing"></a>创建具有 Internet 路由的公共 IP

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中选择“公共 IP 地址”。

3. 选择“+ 新建”。 

4. 在“创建公共 IP 地址”中，输入或选择以下信息：

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”              |    
    | SKU                     | 选择“标准”         |
    | 层                   | 选择“区域”     |
    | 名称                    | 输入“myStandardPublicIP-RP”          |
    | IP 地址分配   | 锁定为“静态”                |
    | 路由首选项     | 选择“Internet”。 |
    | 空闲超时(分钟)  | 保留默认值“4”。        |
    | DNS 名称标签          | 将该值保留为空白。    |
    | 订阅            | 选择订阅   |
    | 资源组          | 选择“新建”，然后输入“QuickStartCreateIP-rg” 。 </br> 选择“确定”。 |
    | 位置                | 选择“(US)美国东部 2”     |
    | 可用性区域       | 选择“区域冗余” |

5. 选择“创建”。

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="在 Azure 门户中配置路由首选项的屏幕截图" border="true":::

> [!NOTE]
> 公共 IP 地址是使用 IPv4 或 IPv6 地址创建的。 但是，路由首选项目前仅支持 IPV4。

> [!NOTE]
> 在提供[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../../availability-zones/az-overview.md)。

可以将上面创建的公共 IP 地址与 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机相关联。 使用“教程”页上的“CLI”部分：[将公共 IP 地址与虚拟机关联](../../virtual-network/associate-public-ip-address-vm.md#azure-cli)以将公共 IP 关联至 VM。 也可将上面创建的公共 IP 地址与 [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)相关联，只需将其分配给负载均衡器前端配置即可。 此公共 IP 地址充当负载均衡型虚拟 IP 地址 (VIP)。

# <a name="tier"></a>[**层**](#tab/option-1-create-public-ip-tier)

公共 IP 地址与单个区域相关联。 全局层可跨多个区域扩展 IP 地址。 跨区域负载均衡器前端需要全局层。  

有关详细信息，请参阅[跨区域负载均衡器](../../load-balancer/cross-region-overview.md)。

## <a name="create-a-global-tier-public-ip"></a>创建全局层公共 IP

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中选择“公共 IP 地址”。

3. 选择“+ 新建”。 

4. 在“创建公共 IP 地址”中，输入或选择以下信息：

    | 设置                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 选择“IPv4”              |    
    | SKU                     | 选择“标准”         |
    | 层                   | 选择“全局”     |
    | 名称                    | 输入“myStandardPublicIP-Global”          |
    | IP 地址分配   | 锁定为“静态”                |
    | 路由首选项     | 选择“Microsoft”。 |
    | 空闲超时(分钟)  | 保留默认值“4”。        |
    | DNS 名称标签          | 将该值保留为空白。    |
    | 订阅            | 选择订阅   |
    | 资源组          | 选择“新建”，然后输入“QuickStartCreateIP-rg” 。 </br> 选择“确定”。 |
    | 位置                | 选择“(US)美国东部 2”     |
    | 可用性区域       | 选择“区域冗余” |

5. 选择“创建”。

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="在 Azure 门户中配置层级的屏幕截图" border="true":::

可将上面创建的 IP 地址与跨区域负载均衡器相关联。 有关详细信息，请参阅[教程：使用 Azure 门户创建跨区域负载均衡器](../../load-balancer/tutorial-cross-region-portal.md)。

---

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除公共 IP 地址：

1. 在门户顶部的搜索框中输入“资源组”。

2. 在搜索结果中选择“资源组”。

3. 选择“QuickStartCreateIP-rg”

4. 选择“删除资源组”。

5. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建公共 IP 前缀：
> [!div class="nextstepaction"]
> [使用 Azure 门户创建公共 IP 前缀](create-public-ip-prefix-portal.md)
