---
title: 教程：创建 Azure Bastion 主机：Windows VM：门户
description: 在本文中，你将了解如何创建 Azure Bastion 主机并连接到 Windows VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 60b49e5b6e103a85d79cf8495f2743b22e434c96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586786"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>教程：配置 Bastion 并通过浏览器连接到 Windows VM

本教程介绍如何使用 Azure Bastion 和 Azure 门户通过浏览器连接到虚拟机。 在 Azure 门户中，将 Bastion 部署到虚拟网络。 部署 Bastion 之后，使用 Azure 门户通过 VM 的专用 IP 地址连接到该 VM。 你的 VM 不需要公共 IP 地址或特殊软件。 预配服务后，RDP/SSH 体验即可用于同一虚拟网络中的所有虚拟机。 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion](bastion-overview.md)。

本教程介绍以下操作：

> [!div class="checklist"]
> * 为你的 VNet 创建 Bastion 主机
> * 连接到 Windows 虚拟机

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 一个虚拟网络。
* 虚拟网络中的 Windows 虚拟机。
* 需要以下角色：
  * 虚拟机上的读者角色。
  * NIC 上的读者角色（使用虚拟机的专用 IP）。
  * Azure Bastion 资源上的读者角色。

* 端口：若要连接到 Windows VM，必须在 Windows VM 上打开以下端口：
  * 入站端口：RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分可帮助你在 VNet 中创建 Bastion 对象。 为了与 VNet 中的 VM 建立安全连接，这是必需的。

1. 从“主页”选择“+ 创建资源” 。
1. 在“新建”页上的“搜索”框中，键入“Bastion”，然后选择 Enter 以获得搜索结果  。 在“Bastion”的搜索结果中，确认发布者是 Microsoft。
1. 选择“创建”  。
1. 在“创建 Bastion”页上，配置新的 Bastion 资源。

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="创建 Bastion 主机" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **订阅**：你需要用于新建 Bastion 资源的 Azure 订阅。
    * **资源组**：将在其中创建新的 Bastion 资源的 Azure 资源组。 如果目前没有资源组，可新建一个。
    * **名称**：新 Bastion 资源的名称。
    * **区域**：将在其中创建资源的 Azure 公共区域。
    * **虚拟网络**：将在其中创建 Bastion 资源的虚拟网络。 你可在此过程中通过门户创建新的虚拟网络，也可使用现有虚拟网络。 如果是后者，请确保现有虚拟网络有足够多的空闲地址空间来满足 Bastion 子网的要求。 如果从下拉列表中看不到虚拟网络，请确保已选择正确的“资源组”。
    * **子网**：创建或选择虚拟网络后，将显示“子网”字段。 Bastion 主机将部署到的虚拟网络中的子网。 此子网专用于该 Bastion 主机。 选择“管理子网配置”并创建 Azure Bastion 子网。 选择“+ 子网”并按照以下指南创建子网：

         * 子网命名必须为 AzureBastionSubnet。
         * 子网必须为 /27 或更大。

      无需填写其他字段。 选择“确定”，然后在页面顶部选择“创建 Bastion”以返回到 Bastion 配置页面 。
    * **公共 IP 地址**：Bastion 资源的公共 IP，将在该 IP 上通过端口 443 访问 RDP/SSH。 创建新的公共 IP。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。 此 IP 地址与你要连接的任何 VM 无关。 它是 Bastion 主机资源的公共 IP。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。 在本教程中，你可以保留默认值。
    * **公共 IP 地址 SKU**：默认情况下，该设置预填充为“标准”。 Azure Bastion 仅使用/支持标准公共 IP SKU。
    * **分配**：默认情况下，该设置预填充为“静态”。

1. 指定完设置后，选择“查看 + 创建”。 这会验证值。 验证通过后，即可创建 Bastion 资源。
1. 选择“创建”  。
1. 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 创建和部署 Bastion 资源大约需要 5 分钟的时间。

## <a name="connect-to-a-vm"></a>连接到 VM

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除相关的资源：

1. 在门户顶部的“搜索”框中输入资源组的名称。 当在搜索结果中看到你的资源组时，将其选中。
1. 选择“删除资源组”  。
1. 输入你的资源组的名称“键入资源组名称”：然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 Bastion 主机并将其关联到了虚拟网络，然后连接到了 Windows 虚拟机。 你可以选择将网络安全组与 Azure Bastion 子网一起使用。 为此，请参阅：

> [!div class="nextstepaction"]
> [使用 NSG](bastion-nsg.md)
