---
title: 教程：为 VM 配置路由首选项 - Azure 门户
description: 本教程介绍如何使用 Azure 门户来创建具有已设置所选路由首选项的公共 IP 地址的 VM。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a06ec95b6970e4c3f8d4bde4cc180bf832f3a6a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369092"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-portal"></a>教程：使用 Azure 门户为 VM 配置路由首选项 

本教程介绍如何为虚拟机配置路由首选项。 如果你选择“Internet”作为路由首选项，来自 VM 的 Internet 限制流量会通过 ISP 网络进行路由。 默认是通过 Microsoft 全球网络路由。

在本教程中，你将了解：

> [!div class="checklist"]
> * 使用为 Internet 路由首选项配置的公共 IP 地址创建虚拟机。
> * 验证公共 IP 地址是否设置为“Internet”路由首选项。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-machine"></a>创建虚拟机

本部分中将创建一个虚拟机和公共 IP 地址。 在公共 IP 地址配置过程中，你将选择“Internet”作为路由首选项。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，依次选择“+ 创建”、“+ 虚拟机”  。

4. 在“创建虚拟机”的“基本信息”选项卡中，输入或选择以下信息 。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“TutorVMRoutePref-rg”。 选择“确定”。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVM”。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 可用性选项 | 选择“无需基础结构冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen2”。 |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择一个大小。 |
    | **管理员帐户** |   |
    | 用户名 | 输入用户名。 |
    | Password | 输入密码。 |
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 保留默认值“RDP (3389)”。 </br> 对于生产型工作负载，建议不要打开从 Internet 进行访问的端口 3389。 |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-virtual-machine.png" alt-text="“创建虚拟机”的屏幕截图。":::

5. 选择“下一步: 磁盘”，然后选择“下一步: 网络”或“网络”选项卡  。

6. 在“网络”选项卡中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 保留默认值“(new) TutorVMRoutePref-rg-vnet”。 |
    | 子网 | 保留默认值“(new) default (10.1.0.0/24)”。 |
    | 公共 IP | 选择“新建”。 </br> 在“名称”中，选择“myPublicIP” 。 </br> 在“SKU”中，选择“标准” 。 </br> 在“路由首选项”中，选择“Internet” 。 </br> 选择“确定”。 |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-public-ip.png" alt-text="创建公共 IP 地址的屏幕截图。":::

7. 选择“查看 + 创建”。

8. 选择“创建”。

## <a name="verify-internet-routing-preference"></a>验证 Internet 路由首选项

本部分中将搜索先前创建的公共 IP 地址，并验证 Internet 路由首选项。

1. 在门户搜索框中，输入“公共 IP 地址”。 在搜索结果中选择“公共 IP 地址”。

2. 在“公共 IP 地址”中，选择“myPublicIP” 。

3. 在“设置”中，选择“属性” 。

4. 验证“Internet”是否显示在“路由首选项”中 。 

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/verify-routing-preference.png" alt-text="验证 Internet 路由首选项的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请按以下步骤删除公共 IP 地址：

1. 在门户顶部的搜索框中输入“资源组”。

2. 在搜索结果中选择“资源组”。

3. 选择“TutorVMRoutePref-rg”

4. 选择“删除资源组”。

5. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建具有混合路由首选项的虚拟机：
> [!div class="nextstepaction"]
> [为虚拟机配置两个路由首选项选项](routing-preference-mixed-network-adapter-portal.md)

