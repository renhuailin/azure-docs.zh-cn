---
title: 在 Azure 开发测试实验室虚拟机上启用浏览器连接
description: 开发测试实验室现已与 Azure Bastion 集成，作为实验室所有者，你可以启用通过浏览器访问所有实验室虚拟机。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 7982c878f062e0b8623bde9da6ea585226a455c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624285"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>在 Azure 开发测试实验室虚拟机上启用浏览器连接 
开发测试实验室与 [Azure Bastion](../bastion/index.yml) 集成，因此你可以通过浏览器连接到虚拟机。 首先需要在实验室虚拟机上启用浏览器连接。

作为实验室所有者，你可以启用通过浏览器访问所有实验室虚拟机。 无需额外的客户端、代理或软件。 在 Azure Bastion 中，可以通过 TLS 直接在 Azure 门户中安全无缝地与虚拟机建立 RDP/SSH 连接。 当你通过 Azure Bastion 连接时，虚拟机无需公共 IP 地址。 有关详细信息，请参阅[什么是 Azure Bastion？](../bastion/bastion-overview.md)


本文介绍如何在实验室虚拟机上启用浏览器连接。

## <a name="prerequisites"></a>先决条件 
- 在现有实验室的虚拟网络中部署堡垒主机，或者将实验室与配置了堡垒的虚拟网络相连接。
若要了解如何在虚拟网络中部署堡垒主机，请参阅[创建 Azure 堡垒主机](../bastion/tutorial-create-host-portal.md)。 创建堡垒主机时，请选择实验室的虚拟网络。 
- 实验室用户需要在堡垒主机上以及配置了堡垒的虚拟网络上拥有“读取者”角色。 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>在堡垒虚拟网络中创建另一个子网
首先需要在堡垒虚拟网络中创建另一个子网，因为 AzureBastionSubnet 不允许在其中创建非堡垒资源。 如下图所示在堡垒虚拟网络中创建另一个子网：

![Azure 堡垒虚拟网络中的第二个子网](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>在子网中启用 VM 创建
现在请执行以下步骤，在此子网中启用 VM 的创建： 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航菜单中，选择“所有服务”。 
1. 从列表中选择“开发测试实验室”。 
1. 在实验室列表中选择你的实验室。 

    > [!NOTE]
    > Azure Bastion 现已在以下区域正式发布：美国西部、美国东部、西欧、美国中南部、澳大利亚东部和日本东部。 因此，如果你的实验室不在其中一个区域，请在其中一个区域中创建实验室。 
    
1. 在左侧菜单的“设置”部分选择“配置和策略” 。 
1. 选择“虚拟网络”。
1. 在工具栏中选择“添加”。 
1. 选择部署了堡垒主机的虚拟网络。 
1. 请选择 VM 的子网，而不是前面创建的另一个 AzureBastionSubnet。 如果在底部列表中未看到该子网，请关闭再重新打开页面。 

    ![在子网中启用 VM 创建](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. 选择“在创建虚拟机时使用”选项。 
1. 在工具栏上选择“保存”。 
1. 如果实验室有旧的虚拟网络，请选择依次“...”和“删除”以删除该虚拟网络。* 

## <a name="enable-browser-connection"></a>启用浏览器连接 

在实验室中创建一个配置了堡垒的虚拟网络后，作为实验室所有者，你可以在实验室虚拟机上启用浏览器连接。

若要在实验室虚拟机上启用浏览器连接，请执行以下步骤：

1. 在 Azure 门户中导航到你的实验室。
1. 选择“配置和策略”。
1. 在“设置”中选择“浏览器连接” 。 如果未看到此选项，请关闭再重新打开“配置策略”页。 

    ![启用浏览器连接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>后续步骤
参阅以下文章，了解如何使用浏览器连接到 VM：[通过浏览器连接到虚拟机](connect-virtual-machine-through-browser.md)