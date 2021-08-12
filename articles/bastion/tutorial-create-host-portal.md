---
title: 教程：创建 Azure Bastion 主机：Windows VM：门户
description: 了解如何创建 Azure Bastion 主机并连接到 Windows VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: bdaad591effc2ef9e5d682dd9a80d994a9e1c34d
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733387"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm"></a>教程：配置 Bastion 并连接到 Windows VM

本教程介绍如何使用 Azure Bastion 和 Azure 门户通过浏览器连接到虚拟机。 本教程介绍使用 Azure 门户将 Bastion 部署到虚拟网络。 预配服务后，RDP/SSH 体验即可用于同一虚拟网络中的所有虚拟机。 使用 Bastion 进行连接时，VM 不需要公共 IP 地址或特殊软件。 部署 Bastion 后，如果不需要用公共 IP 地址做其他事，可以从 VM 中删除该地址。 下一步，使用 Azure 门户通过 VM 的专用 IP 地址连接到该 VM。 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion](bastion-overview.md)。

本教程介绍以下操作：

> [!div class="checklist"]
> * 为你的 VNet 创建堡垒主机。
> * 删除虚拟机中的公共 IP 地址。
> * 连接到 Windows 虚拟机。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 一个虚拟网络。
* 虚拟网络中的 Windows 虚拟机。 如果没有 VM，请按照[快速入门：创建 VM](../virtual-machines/windows/quick-create-portal.md) 的说明创建一个。
* 资源所需的以下角色：
   * 所需 VM 角色：
     * 虚拟机上的读者角色。
     * NIC 上的读者角色（使用虚拟机的专用 IP）。

* 端口：若要连接到 Windows VM，必须在 Windows VM 上打开以下端口：
  * 入站端口：RDP (3389)

 >[!NOTE]
 >目前不支持一起使用 Azure Bastion 和 Azure 专用 DNS 区域。 开始之前，请确保你计划在其中部署 Bastion 资源的虚拟网络未链接到专用 DNS 区域。
 >

### <a name="example-values"></a><a name="values"></a>示例值

创建此配置时，可以使用以下示例值，也可以将其替换为自己的值。

**基本 VNet 和 VM 值：**

|**名称** | **值** |
| --- | --- |
| 虚拟机| TestVM |
| 资源组 | TestRG1 |
| 区域 | 美国东部 |
| 虚拟网络 | VNet1 |
| 地址空间 | 10.1.0.0/16 |
| 子网 | 前端：10.1.0.0/24 |

**Azure Bastion 值：**

|**名称** | **值** |
| --- | --- |
| 名称 | VNet1-bastion |
| + 子网名称 | AzureBastionSubnet |
| AzureBastionSubnet 地址 | VNet 地址空间中的一个子网，其子网掩码为 /27 或更大。<br> 例如 10.1.1.0/26。  |
| 层/SKU | 标准 |
| 实例计数（主机缩放）| 3 或更大 |
| 公共 IP 地址 |  新建 |
| 公共 IP 地址名称 | VNet1-ip  |
| 公用 IP 地址 SKU |  Standard  |
| 分配  | 静态 |

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

[!INCLUDE [Azure Bastion preview portal](../../includes/bastion-preview-portal-note.md)]

登录到 Azure 门户。

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分可帮助你在 VNet 中创建 Bastion 对象。 为了与 VNet 中的 VM 建立安全连接，这是必需的。

1. 在搜索框中键入“Bastion”。
1. 在“服务”下方，单击“Bastions”。
1. 在“Bastion”页面上单击“创建”，打开“创建 Bastion”页面 。
1. 在“创建 Bastion”页上，配置新的 Bastion 资源。

   :::image type="content" source="./media/tutorial-create-host-portal/review-create.png" alt-text="“创建 Bastion 门户”页面的屏幕截图。" lightbox="./media/tutorial-create-host-portal/create-expand.png":::

### <a name="project-details"></a>项目详细信息

* **订阅**：要使用的 Azure 订阅。

* **资源组**：将在其中创建新的 Bastion 资源的 Azure 资源组。 如果目前没有资源组，可新建一个。

### <a name="instance-details"></a>实例详细信息

* **名称**：新 Bastion 资源的名称。

* **区域**：将在其中创建资源的 Azure 公共区域。

* **层**：层也称为 SKU。 对于本教程，我们从下拉菜单中选择“标准”SKU。 选择标准 SKU 可以配置主机缩放的实例计数。 基本 SKU 不支持主机缩放。 有关详细信息，请参阅[配置设置 - SKU](configuration-settings.md#skus)。 标准 SKU 目前为预览版。

* **实例计数：** 这是主机缩放的设置，并按缩放单元增量进行配置。 使用滑块配置实例计数。 如果已指定基本层 SKU，则不能配置此设置。 有关详细信息，请参阅[配置设置 - 主机缩放](configuration-settings.md#instance)。 在本教程中，可以选择首选的实例计数，请记住任何缩放单元[定价](https://azure.microsoft.com/pricing/details/azure-bastion)注意事项。

### <a name="configure-virtual-networks"></a>配置虚拟网络

* **虚拟网络**：将在其中创建 Bastion 资源的虚拟网络。 你可在此过程中通过门户创建新的虚拟网络，也可使用现有虚拟网络。 如果是后者，请确保现有虚拟网络有足够多的空闲地址空间来满足 Bastion 子网的要求。 如果从下拉列表中看不到虚拟网络，请确保已选择正确的“资源组”。

* **子网**：创建或选择虚拟网络后，子网字段将于页面上显示。 这是部署 Bastion 实例的子网。 

#### <a name="add-the-azurebastionsubnet"></a>添加 AzureBastionSubnet

在大多数情况下，你尚未配置 AzureBastionSubnet。 配置 bastion 子网： 

1. 选择“管理子网配置”。 这会转到“子网”页面。

   :::image type="content" source="./media/tutorial-create-host-portal/subnet.png" alt-text="管理子网配置的屏幕截图。":::
1. 在“子网”页上，选择“+子网”以打开“添加子网”页面  。 

1. 使用以下指引以创建子网：

   * 子网命名必须为 AzureBastionSubnet。
   * 子网必须为 /27 或更大。 对于标准 SKU，建议大于或等于 /26 以适应将来的其他主机缩放实例。

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-subnet.png" alt-text="AzureBastionSubnet 子网的屏幕截图。":::

1. 无需在此页面上填写其他字段。 选择页面底部的“保存”以保存设置，并关闭“添加子网” 。

1. 在“子网”页面顶部选择“创建 Bastion”以返回到 Bastion 配置页面 。

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="创建 Bastion 的屏幕截图。":::

### <a name="public-ip-address"></a>公共 IP 地址

将在 Bastion 资源的公共 IP 地址的端口 443 访问 RDP/SSH。 新建一个公共 IP 地址。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。 此 IP 地址与你要连接的任何 VM 无关。 它是 Bastion 主机资源的公共 IP 地址。

   * **公共 IP 地址名称**：公共 IP 地址资源的名称。 在本教程中，你可以保留默认值。
   * **公共 IP 地址 SKU**：默认情况下，该设置预填充为“标准”。 Azure Bastion 仅使用/支持标准公共 IP SKU。
   * **分配**：默认情况下，该设置预填充为“静态”。

### <a name="review-and-create"></a>查看并创建

1. 完成指定设置后，选择“查看 + 创建”。 这会验证值。 验证通过后，即可创建 Bastion 资源。
1. 复查你的设置。 
1. 在页面底部，选择“创建”。
1. 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 创建和部署 Bastion 资源大约需要 5 分钟的时间。

## <a name="remove-vm-public-ip-address"></a>删除 VM 公共 IP 地址

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>连接到 VM

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除相关的资源：

1. 在门户顶部的“搜索”框中输入资源组的名称。 当在搜索结果中看到你的资源组时，将其选中。
1. 选择“删除资源组”  。
1. 输入你的资源组的名称“键入资源组名称”：然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 Bastion 主机并将其关联到了虚拟网络。 然后从 VM 中删除了公共 IP 地址并连接到该地址。 你可以选择将网络安全组与 Azure Bastion 子网一起使用。 为此，请参阅：

> [!div class="nextstepaction"]
> [使用 NSG](bastion-nsg.md)
