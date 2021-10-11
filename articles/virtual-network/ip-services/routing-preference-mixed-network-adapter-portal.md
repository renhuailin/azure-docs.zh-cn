---
title: 教程：为虚拟机配置两个路由首选项选项 - Azure 门户
titlesuffix: Azure Virtual Network
description: 使用本教程可了解如何使用 Azure 门户为虚拟机配置两个路由首选项选项。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: d11e77725dfb99060c96f62233833330867f1c0d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368796"
---
# <a name="tutorial-configure-both-routing-preference-options-for-a-virtual-machine-using-the-azure-portal"></a>教程：使用 Azure 门户为虚拟机配置两个路由首选项选项

本文介绍如何为虚拟机 (VM) 配置两个[路由首选项](routing-preference-overview.md)选项（Internet 和 Microsoft 全局网络）。 此配置是使用两个虚拟网络接口实现的。 一个网络接口配置有通过 Microsoft 全局网络进行路由的公共 IP。 另一个网络接口配置有通过 ISP 网络进行路由的公共 IP。

在本教程中，你将了解：

> [!div class="checklist"]
> * 使用“Microsoft 网络”路由首选项创建具有公共 IP 地址的虚拟机。
> * 使用“Internet”路由首选项创建公共 IP 地址。
> * 为虚拟机创建辅助网络接口。
> * 将 Internet 路由首选项公共 IP 关联到虚拟机辅助网络接口。
> * 将辅助网络接口附加到虚拟机。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-virtual-machine"></a>创建虚拟机

在本部分中，将创建一个虚拟机和公共 IP 地址。 在公共 IP 地址配置过程中，你将选择“Microsoft 网络”作为路由首选项。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，依次选择“+ 创建”、“+ 虚拟机”  。

4. 在“创建虚拟机”的“基本信息”选项卡中，输入或选择以下信息 。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“TutorVMMixRoutePref-rg”。 选择“确定”。 |
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
    | 选择入站端口 | 保留默认值“RDP (3389)”。 </br> _**对于生产型工作负载，建议不要打开从 Internet 进行访问的端口 3389。**_ |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-virtual-machine.png" alt-text="“创建虚拟机”的屏幕截图。":::

5. 选择“下一步: 磁盘”，然后选择“下一步: 网络”或“网络”选项卡  。

6. 在“网络”选项卡中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 保留默认值“(new) TutorVMMixRoutePref-rg-vnet”。 |
    | 子网 | 保留默认值“(new) default (10.1.0.0/24)”。 |
    | 公共 IP | 选择“新建”。 </br> 在“名称”中，选择“myPublicIP” 。 </br> 在“SKU”中，选择“标准” 。 </br> 在“路由首选项”中，选择“Microsoft 网络” 。 </br> 选择“确定”。 |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-ms-rp.png" alt-text="使用 Microsoft 路由首选项创建公共 IP 地址的屏幕截图。":::

7. 选择“查看 + 创建”。

8. 选择“创建”。

## <a name="create-the-public-ip-address"></a>创建公共 IP 地址

在本部分中，将使用“Internet”路由首选项创建公共 IP 地址。

1. 在门户搜索框中，输入“公共 IP 地址”。 在搜索结果中选择“公共 IP 地址”。

2. 选择“+ 新建”。 

3. 在“创建公共 IP 地址”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | IP 版本 | 保留默认值“IPv4”。 |
    | SKU | 保留默认值“标准”。 |
    | 层 | 保留默认值“区域”。 |
    | IPv4 IP 地址配置 |   |
    | 名称 | 输入“myPublicIP-IR”。 |
    | 路由首选项 | 选择“Internet”。 |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“TutorVMMixRoutePref-rg”。 |
    | 位置 | 选择“美国西部 2”。 |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-internet-rp.png" alt-text="使用 Internet 路由首选项创建公共 IP 地址的屏幕截图。":::

4. 选择“创建”。

## <a name="create-the-secondary-nic"></a>创建辅助 NIC

在本部分中，将为之前创建的虚拟机创建辅助网络接口。

1. 在门户搜索框中，输入“网络接口”。 在搜索结果中，选择“网络接口”。

2. 选择“+ 新建”。 

3. 在“创建网络接口”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“TutorVMMixRoutePref-rg”。 |
    | 名称 | 输入“myVMNic2”。 |
    | 区域 | 选择“美国西部 2”  。 |
    | 虚拟网络 | 选择“TutorVMMixRoutePref-rg-vnet”。 |
    | 子网 | 选择“TutorVMMixRoutePref-rg-vnet/默认值(10.1.0.0/24)”。 |
    | 网络安全组 | 选择“myVM-nsg”。 |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-network-interface.png" alt-text="创建辅助网络接口的屏幕截图。":::

4. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

5. 选择“创建”。

## <a name="associate-the-public-ip-address-with-secondary-nic"></a>将公共 IP 地址与辅助 NIC 关联

在本部分中，会将之前创建的“Internet”路由首选项公共 IP 地址与在上一部分中创建的网络接口关联。

1. 在门户搜索框中，输入“公共 IP 地址”。 在搜索结果中选择“公共 IP 地址”。

2. 选择“myPublicIP-IR”。

3. 在 myPublic-IR 的“概述”页上，选择“关联”  。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/associate-public-ip.png" alt-text="带有关联按钮的 myPublicIP-IR 概述页的屏幕截图。":::

4. 在“关联公共 IP 地址”中，在“资源类型”下拉框中选择“网络接口”  。

5. 在“网络接口”下拉框中选择“myVMNic2” 。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/select-ip-association-resource.png" alt-text="选择要关联到公共 IP 地址的资源的屏幕截图。":::

6. 选择“确定”。

## <a name="attach-secondary-network-interface-to-virtual-machine"></a>将辅助网络接口附加到虚拟机

在本部分中，会将之前创建的辅助网络接口附加到虚拟机。

1. 在门户的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 选择“myVM”。

3. 如果正在运行，请停止 myVM，否则继续执行下一步。

4. 在“myVM”中，在“设置”中选择“网络”。  

5. 在 myVM 的“网络”页中，选择“附加网络接口”  。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-01.png" alt-text="myVM 网络概述页的屏幕截图。":::

6. 在“附加网络接口”中，在下拉框中选择“myVMNic2” 。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-02.png" alt-text="附加网络接口的屏幕截图。":::

7. 选择“确定”。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除公共 IP 地址和虚拟机：

1. 在门户顶部的搜索框中输入“资源组”。

2. 在搜索结果中选择“资源组”。

3. 选择“TutorVMMixRoutePref-rg”

4. 选择“删除资源组”。

5. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建公共 IP 前缀：
> [!div class="nextstepaction"]
> [使用 Azure CLI 为 Kubernetes 群集配置路由首选项](routing-preference-azure-kubernetes-service-cli.md)