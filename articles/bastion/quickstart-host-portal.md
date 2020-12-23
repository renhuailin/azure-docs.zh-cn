---
title: 快速入门：配置 Azure Bastion 并通过专用 IP 地址和浏览器连接到 VM
titleSuffix: Azure Bastion
description: 本快速入门文章介绍如何从虚拟机创建 Azure Bastion 主机并通过专用 IP 地址和浏览器安全地连接到 VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021480"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>快速入门：通过专用 IP 地址和浏览器安全地连接到 VM

可以使用 Azure 门户和 Azure Bastion 通过浏览器连接到虚拟机 (VM)。 本快速入门文章介绍了如何根据 VM 设置配置 Azure Bastion，然后通过门户连接到 VM。 VM 不需要公共 IP 地址、客户端软件、代理或特殊配置。 预配服务后，RDP/SSH 体验即可用于同一虚拟网络中的所有虚拟机。 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion](bastion-overview.md)。

## <a name="prerequisites"></a><a name="prereq"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有，请[免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 为了能够使用 Bastion 通过浏览器连接到 VM，必须能够登录 Azure 门户。

* 虚拟网络中的 Windows 虚拟机。 如果没有 VM，请按照[快速入门：创建 VM](../virtual-machines/windows/quick-create-portal.md) 的说明创建一个。

  * 如果需要示例值，请参阅提供的[示例值](#values)。
  * 如果已有虚拟网络，请确保在创建 VM 时在“网络”选项卡上选择它。
  * 如果还没有虚拟网络，可以在创建 VM 的同时创建一个虚拟网络。
  * 此 VM 无需公共 IP 地址即可通过 Azure Bastion 进行连接。

* 所需 VM 角色：
  * 虚拟机上的读者角色。
  * NIC 上的读者角色（使用虚拟机的专用 IP）。
  
* 所需 VM 端口：
  * 入站端口：RDP (3389)

### <a name="example-values"></a><a name="values"></a>示例值

创建此配置时，可以使用以下示例值，也可以将其替换为自己的值。

**基本 VNet 和 VM 值：**

|**名称** | **值** |
| --- | --- |
| 虚拟机| TestVM |
| 资源组 | TestRG |
| 区域 | 美国东部 |
| 虚拟网络 | TestVNet1 |
| 地址空间 | 10.0.0.0/16 |
| 子网 | FrontEnd：10.0.0.0/24 |

**Azure Bastion 值：**

|**名称** | **值** |
| --- | --- |
| 名称 | TestVNet1-bastion |
| + 子网名称 | AzureBastionSubnet |
| AzureBastionSubnet 地址 | VNet 地址空间中子网掩码为 /27 的子网。 例如 10.0.1.0/27。  |
| 公共 IP 地址 |  新建 |
| 公共 IP 地址名称 | VNet1BastionPIP  |
| 公用 IP 地址 SKU |  Standard  |
| 分配  | 静态 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>创建 Bastion 主机

可以通过几种不同的方式来配置堡垒主机。 可按照以下步骤在 Azure 门户中直接从 VM 创建堡垒主机。 从 VM 创建主机时，多个设置可根据虚拟机和/或虚拟网络自动填充。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到要连接的 VM，然后选择“连接”。

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="虚拟机设置" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. 在下拉列表中，选择“Bastion”。
1. 在“TestVM | 连接”页上，选择“使用 Bastion” 。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="选择“Bastion”" border="false":::

1. 在“Bastion”页上，填写以下设置字段：

   * 名称：为 Bastion 主机命名。
   * **子网**：这是将向其中部署 Bastion 资源的虚拟网络地址空间。 必须使用名称 AzureBastionSubnet 创建子网。 使用至少为 /27 或更大（/27、/26、/25 等）的子网。
   * 选择“管理子网配置”。
1. 在“子网”页中，选择“+ 子网” 。

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="+ 子网":::
    
1. 在“添加子网”页上，为“名称”键入 AzureBastionSubnet  。
   * 对于子网地址范围，请选择你的虚拟网络地址空间中的子网地址。
   * 请勿调整任何其他设置。 选择“确定”以接受并保存子网更改。

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="添加子网":::
1. 单击浏览器上的后退按钮，导航回“Bastion”页，然后继续指定值。
   * **公共 IP 地址**：保留“新建”。
   * **公共 IP 地址名称**：公共 IP 地址资源的名称。
   * **分配**：默认为“静态”。 不能对 Azure Bastion 使用“动态”分配。
   * **资源组**：与 VM 相同的资源组。

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="创建 Bastion 主机":::
1. 选择“创建”以创建 Bastion 主机。 Azure 会验证设置，然后创建主机。 主机和其资源的创建及部署大约需要 5 分钟。

## <a name="connect"></a><a name="connect"></a>连接

在将 Bastion 部署到虚拟网络后，屏幕切换到连接页面。

1. 键入虚拟机的用户名和密码。 然后，选择“连接”。

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="屏幕截图显示“使用 Azure Bastion 进行连接”对话框。":::
1. 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP 连接":::

## <a name="clean-up-resources"></a>清理资源

使用完虚拟网络和虚拟机之后，请删除资源组和其包含的所有资源：

1. 在门户顶部的“搜索”框中输入资源组的名称，并从搜索结果中选择资源组。

1. 选择“删除资源组”  。

1. 在“键入资源组名称”中输入资源组名称，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你为虚拟网络创建了一个堡垒主机，然后通过 Bastion 安全连接到了虚拟机。 接下来，如果要连接到虚拟机规模集，可以继续执行以下步骤。

> [!div class="nextstepaction"]
> [使用 Azure Bastion 连接到虚拟机规模集](bastion-connect-vm-scale-set.md)
