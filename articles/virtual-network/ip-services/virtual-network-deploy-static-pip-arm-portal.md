---
title: 创建具有静态公共 IP 地址的 VM - Azure 门户
description: 了解如何使用 Azure 门户创建具有静态公共 IP 地址的 VM。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: allensu
ms.openlocfilehash: cebaccb67d2731bf4e669b2ab12102ace3a20fcc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367540"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建具有静态公共 IP 地址的虚拟机

使用公共 IP 地址可以通过 Internet 来与虚拟机通信。 

分配静态公共 IP 地址而非动态地址可以确保地址永远不会改变。   

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”，或者在搜索框中搜索“虚拟机”。   
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“新建”。 </br> 在“名称”中，输入“myResourceGroup”。 </br> 选择“确定”。 |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM” |
    | 区域 | 选择“美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“RDP (3389)” |

    > [!WARNING]
    > 选择了端口 3389，目的是能够通过 Internet 对 Windows Server 虚拟机进行远程访问。 不建议向 Internet 打开端口 3389 来管理生产工作负荷。 </br> 若要安全地访问 Azure 虚拟机，请参阅[什么是 Azure Bastion？](../../bastion/bastion-overview.md)

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 接受默认网络名称。 |
    | 子网 | 接受默认子网配置。 |
    | 公共 IP | 选择“新建”。 </br> 在“创建公共 IP 地址”中，输入“myPublicIP”作为名称。 </br> 对于“SKU”，选择“标准” 。 </br> 对于“分配”，选择“静态”。 </br> 选择“确定”。  |
    | NIC 网络安全组 | 选择“基本” |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“RDP (3389)” |
   
5. 选择“查看 + 创建”。 
  
6. 检查设置，然后选择“创建”。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。

2. 选择“删除资源组”。

3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)：

* 将公共 IP 地址从动态更改为静态。

* 使用专用 IP 地址。

公共 IP 地址会产生[少许费用](https://azure.microsoft.com/pricing/details/ip-addresses)。 每个订阅可以使用的公共 IP 地址数有[限制](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

添加到后端池时，虚拟机的公共 IP 地址的 SKU 必须与 Azure 负载均衡器的公共 IP SKU 相匹配。 有关详细信息，请参阅 [Azure 负载均衡器](../../load-balancer/skus.md)。

对于 Azure [公有](https://www.microsoft.com/download/details.aspx?id=56519)云、[美国政府](https://www.microsoft.com/download/details.aspx?id=57063)云、[中国](https://www.microsoft.com/download/details.aspx?id=57062)云和[德国](https://www.microsoft.com/download/details.aspx?id=57064)云，可以下载范围（前缀）的列表。

- 详细了解[静态公共 IP 地址](public-ip-addresses.md#ip-address-assignment)。

- 详细了解 Azure 中的[公共 IP 地址](public-ip-addresses.md#public-ip-addresses)。

- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。

- 详细了解[专用 IP 地址](private-ip-addresses.md)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)。
