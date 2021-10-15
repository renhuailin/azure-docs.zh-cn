---
title: 创建具有静态专用 IP 地址的 VM - Azure 门户
description: 了解如何使用 Azure 门户创建具有静态专用 IP 地址的虚拟机。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eec4e9e07f19130732219c0932f66a1cfa0fa2b7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367414"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建具有静态专用 IP 地址的虚拟机

系统会从指定的范围自动为虚拟机 (VM) 分配专用 IP 地址。 此范围取决于部署 VM 的子网。 VM 会保留该地址，直到此 VM 被删除。 Azure 从你创建 VM 时所在的子网动态分配下一个可用的专用 IP 地址。 如果需要子网中的特定 IP 地址，请向 VM 分配静态 IP 地址。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-machine"></a>创建虚拟机

使用以下步骤创建虚拟机、虚拟网络和子网。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，依次选择“+ 创建”、“+ 虚拟机”  。

4. 在“创建虚拟机”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 在“名称”中输入“myResourceGroup” 。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVM”。 |
    | 区域 | 选择“(US)美国东部 2”。 |
    | 可用性选项 | 选择“无需基础结构冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen2”。 |
    | Azure Spot 实例 | 保持未选中状态。 |
    | 大小 | 选择一个大小。 |
    | **管理员帐户** |   |
    | 用户名 | 输入用户名。 |
    | Password | 输入密码。 |
    | 确认密码 | 重新输入密码。 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“RDP (3389)” |

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/create-vm.png" alt-text="“创建虚拟机”的屏幕截图。":::

    > [!WARNING]
    > 选择了端口 3389，目的是能够通过 Internet 对 Windows Server 虚拟机进行远程访问。 不建议向 Internet 打开端口 3389 来管理生产工作负荷。 </br> 若要安全地访问 Azure 虚拟机，请参阅[什么是 Azure Bastion？](../../bastion/bastion-overview.md)

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 接受默认网络名称。 |
    | 子网 | 接受默认子网配置。 |
    | 公共 IP | 接受默认公共 IP 配置。 |
    | NIC 网络安全组 | 选择“基本” |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“RDP (3389)” |

5. 选择“查看 + 创建”。 
  
6. 检查设置，然后选择“创建”。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="change-private-ip-address-to-static"></a>将专用 IP 地址更改为静态

在本部分中，将把之前创建的虚拟机专用 IP 地址从“动态”更改为“静态” 。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 在“虚拟机”中，选择“myVM” 。

3. 在“myVM”的“设置”中，选择“网络”。  

4. 在“网络”中，选择“网络接口”旁边的网络接口名称 。 在此例中，NIC 的名称是“myvm472”。

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-nic.png" alt-text="选择网络接口的屏幕截图。":::

5. 在网络接口属性的“设置”中，选择“IP 配置” 。

6. 在“IP 配置”页中，选择“ipconfig1” 。

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-ip-configuration.png" alt-text="选择 IP 配置的屏幕截图。":::

7. 在“分配”中，选择“静态” 。 选择“保存”。

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-static-assignment.png" alt-text="选择静态分配的屏幕截图。":::

    > [!NOTE]
    > 选择“保存”后，如果注意到分配仍设置为“动态”，则表示键入的 IP 地址已被使用   。 请尝试输入其他 IP 地址。

要将 IP 地址改回动态，请将专用 IP 地址的分配设置为“动态”，然后选择“保存” 。

> [!WARNING]
> 在 VM 的操作系统中，不应以静态方式分配已分配给 Azure VM 的专用 IP。  仅在必要的情况下（例如，要[向 VM 分配许多 IP 地址](virtual-network-multiple-ip-addresses-portal.md)时），才执行专用 IP 的静态分配。 
>
>如果在操作系统中手动设置专用 IP 地址，请确保该 IP 地址与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址相匹配。 否则，与 VM 的连接可能会断开。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。

2. 选择“删除资源组”。

3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

- 详细了解[静态公共 IP 地址](public-ip-addresses.md#ip-address-assignment)。

- 详细了解 Azure 中的[公共 IP 地址](public-ip-addresses.md#public-ip-addresses)。

- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。

- 详细了解[专用 IP 地址](private-ip-addresses.md)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)。

