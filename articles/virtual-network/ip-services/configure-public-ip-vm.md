---
title: 管理 Azure 虚拟机的公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解如何结合使用公共 IP 地址和 Azure 虚拟机，以及如何更改配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: a21b734ee1825a86c9276a2e603d04d723bd8d46
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367515"
---
# <a name="manage-a-public-ip-address-with-an-azure-virtual-machine"></a>管理 Azure 虚拟机的公共 IP 地址

公共 IP 地址可在两个 SKU 中使用：标准和基本。 所选的 SKU 确定了 IP 地址的功能。 SKU 确定了 IP 地址可以关联到的资源。  

Azure 虚拟机是 Azure 中的主要计算服务。 客户可以创建 Linux 或 Windows 虚拟机。 可以为虚拟机分配公共 IP 地址，以便与虚拟机建立入站连接。 

虚拟机不需要使用公共 IP 地址进行配置。

本文介绍如何使用订阅中的现有公共 IP 创建 Azure 虚拟机。 了解如何将公共 IP 地址添加到虚拟机。 更改 IP 地址。 最后，将了解如何删除公共 IP。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的两个标准 SKU 公共 IP 地址。 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](../../virtual-network/create-public-ip-portal.md)。
    - 本文中的示例将新的公共 IP 地址命名为“myStandardPublicIP-1”和“myStandardPublicIP-2”。 
- 您的订阅中路由首选项为“Internet”的标准 SKU 公共 IP 地址。 有关创建具有“Internet”路由首选项的公共 IP 的详细信息，请参阅[使用 Azure 门户配置公共 IP 地址的路由首选项](../../virtual-network/routing-preference-portal.md)。
    - 本文中的示例将新的公共 IP 地址命名为“myStandardPublicIP-3”。
## <a name="create-virtual-machine-existing-public-ip"></a>创建虚拟机现有的公共 IP

在本节中，将创建虚拟机。 选择在先决条件中创建的 IP 地址作为虚拟机的公共 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“虚拟机”。

3. 在搜索结果中，选择“虚拟机”。

4. 选择“+ 添加”，然后选择“+ 虚拟机”。 

5. 在“创建虚拟机”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“myResourceGroupVM”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVM”。 |
    | 区域 | 选择“(US) 美国西部 2”。 | 
    | 可用性选项 | 选择“无需基础结构冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1”。 |
    | Azure Spot 实例  | 保留默认值“未选中”。 |
    | 大小 | 选择虚拟机的大小 |
    | **管理员帐户** |   |
    | 用户名 | 输入用户名。 |
    | Password | 输入密码。 |
    | 确认密码 | 确认该密码。 |
    | **入站端口规则** |   |
    | 公共入站端口 | 保留默认值“允许所选端口”。 |
    | 选择入站端口 | 保留默认值“RDP (3389)”。 |

6. 选择“网络”选项卡，或者选择“下一步: 磁盘”，然后选择“下一步: 网络”  。

7. 在“网络”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 保留默认值“(new) myResourceGroupVM-vnet”。 |
    | 子网 | 保留默认值“(new) default (10.1.0.0/24)”。 |
    | 公共 IP | 选择“myStandardPublicIP-1”。 |
    | NIC 网络安全组 | 保留默认值“基本”。 |
    | 公共入站端口 | 保留默认值“允许所选端口”。 |
    | 选择入站端口 | 保留默认值“RDP (3389)”。 |

6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。

7. 选择“创建”。

> [!NOTE]
> 这是一个简单的 Azure 虚拟机部署。 有关高级配置和设置，请参阅[快速入门：在 Azure 门户中创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md)
>
> 有关 Virtual 虚拟机的详细信息，请参阅 [Azure 中的 Windows 虚拟机](../../virtual-machines/windows/overview.md)

## <a name="change-public-ip-address"></a>更改公共 IP 地址

在本节中，将更改与虚拟机的默认公共 IP 配置相关联的公共 IP 地址。

1. 在门户顶部的搜索框中，输入“虚拟机”。

2. 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，选择“myVM” 。

4. 在“myVM”的“设置”中，选择“网络”。  

5. 在“网络”中，选择 VM 的“网络接口”。  NIC 的名称将以 VM 的名称作为前缀，以随机数字结尾。  在此示例中，它是“myvm793”。

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="选择网络接口。" border="true":::

6. 在网络接口的“设置”中，选择“IP 配置”。 

7. 在“IP 配置”中，选择“ipconfig1” 。  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="选择 ipconfig 以更改 IP 地址。" border="true":::

1. 在“ipconfig1”的“公共 IP 地址”中，选择“myStandardPublicIP-2”。  

7. 选择“保存”  。

## <a name="add-public-ip-configuration"></a>添加公共 IP 配置

在本节中，将为虚拟机添加公共 IP 配置。 

有关添加多个 IP 地址的信息，请参阅[使用 Azure 门户将多个 IP 地址分配给虚拟机](../../virtual-network/virtual-network-multiple-ip-addresses-portal.md)。 

有关使用两种类型的路由首选项的详细信息，请参阅[为虚拟机配置两个路由首选项选项](../../virtual-network/routing-preference-mixed-network-adapter-portal.md)。

1. 在门户顶部的搜索框中，输入“虚拟机”。

2. 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，选择“myVM” 。

4. 在“myVM”的“设置”中，选择“网络”。  

5. 在“网络”中，选择 VM 的“网络接口”。  NIC 的名称将以 VM 的名称作为前缀，以随机数字结尾。  在此示例中，它是“myvm793”。

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="选择网络接口。" border="true":::

6. 在网络接口的“设置”中，选择“IP 配置”。 

7. 在“IP 配置”中，选择“+ 添加” 。

8. 在“名称”中，输入“ipconfig2”。 

9. 在“公共 IP 地址”中，选择“关联”。 

10. 在“公共 IP 地址”中，选择“myStandardPublicIP-3” 。

11. 选择“确定”。

## <a name="remove-public-ip-address-association"></a>删除公共 IP 地址关联

在本节中，将从网络接口中删除公共 IP 地址。 此过程后，虚拟机将不能用于外部连接。

1. 在门户顶部的搜索框中，输入“虚拟机”。

2. 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，选择“myVM” 。

4. 在“myVM”的“设置”中，选择“网络”。  

5. 在“网络”中，选择 VM 的“网络接口”。  NIC 的名称将以 VM 的名称作为前缀，以随机数字结尾。  在此示例中，它是“myvm793”。

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="选择网络接口。" border="true":::

6. 在网络接口的“设置”中，选择“IP 配置”。 

7. 在“IP 配置”中，选择“ipconfig1” 。  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="选择 ipconfig 以更改 IP 地址。" border="true":::

8. 在“公共 IP 地址设置”中，选择“接触dis关联”。 

9. 选择“保存”。

## <a name="next-steps"></a>后续步骤

本文已介绍如何创建虚拟机和使用现有公共 IP。 你更改了默认 IP 配置的公共 IP。 最后，将具有 Internet 路由首选项的公共 IP 配置添加到防火墙。

- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](../../virtual-network/public-ip-addresses.md)。
