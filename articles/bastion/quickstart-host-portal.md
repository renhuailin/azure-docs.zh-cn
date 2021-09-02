---
title: 快速入门：从 VM 设置配置 Bastion
titleSuffix: Azure Bastion
description: 了解如何从虚拟机设置创建 Azure Bastion 主机，并在浏览器中通过专用 IP 地址安全地连接到 VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: f6319369a03de754b1ce016fb20aa99e5ab466e1
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223425"
---
# <a name="quickstart-configure-azure-bastion-from-vm-settings"></a>快速入门：从 VM 设置配置 Azure Bastion

本快速入门文章介绍如何在 Azure 门户中根据 VM 设置配置 Azure Bastion，然后通过专用 IP 地址连接到 VM。 预配服务后，RDP/SSH 体验即可用于同一虚拟网络中的所有虚拟机。 VM 不需要公共 IP 地址、客户端软件、代理或特殊配置。 如果不需要对 VM 上的公共 IP 地址执行任何其它操作，可以将其删除。 然后，使用专用 IP 地址，通过门户连接到 VM。 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion？](bastion-overview.md)

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
| 公共 IP 地址 |  新建 |
| 公共 IP 地址名称 | VNet1-ip  |
| 公用 IP 地址 SKU |  Standard  |
| 分配  | 静态 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>创建 Bastion 主机

可以通过几种不同的方式来配置堡垒主机。 可按照以下步骤在 Azure 门户中直接从 VM 创建堡垒主机。 从 VM 创建主机时，多个设置可根据虚拟机和/或虚拟网络自动填充。

1. 登录到 [Azure 门户](https://ms.portal.azure.com)。
1. 导航到要连接的 VM，然后选择“连接”。

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="虚拟机设置的屏幕截图。" lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. 在下拉列表中，选择“Bastion”。

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="“Bastion”下拉列表的屏幕截图。" lightbox="./media/quickstart-host-portal/bastion.png":::
1. 在“TestVM | 连接”页上，选择“使用 Bastion” 。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="“使用 Bastion”的屏幕截图。":::

1. 在“使用 Azure Bastion 进行连接”页上，“步骤 1”中的值已预先填充，因为你是直接从 VM 创建堡垒主机 。

   :::image type="content" source="./media/quickstart-host-portal/create-step-1.png" alt-text="“步骤 1”中预先填充的设置的屏幕截图。" lightbox="./media/quickstart-host-portal/create-step-1.png":::

1. 在“使用 Azure Bastion 进行连接”页上的“步骤 2”中配置子网值 。 AzureBastionSubnet 地址空间已使用建议的地址空间预先填充。 AzureBastionSubnet 必须有 /27 或更大（/26、/25 等）的地址空间。 建议使用 /26，使主机缩放不受限制。 配置完此设置后，单击“创建子网”以创建 AzureBastionSubnet。

     :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="“创建 Bastion 子网”的屏幕截图。":::

1. 在创建子网后，页面会自动前进到步骤 3。 对于步骤 3，请使用以下值：

   * 名称：为堡垒主机命名。
   * 层：指 SKU。 对于本练习，请从下拉列表中选择“标准”。 选择标准 SKU 可以配置主机缩放的实例计数。 基本 SKU 不支持主机缩放。 有关详细信息，请参阅[配置设置 - SKU](configuration-settings.md#skus)。 标准 SKU 目前为预览版。
   * 实例计数：主机缩放的设置。 使用滑块进行配置。 如果指定了基本层 SKU，则最多只能扩展为 2 个实例，且无法配置此设置。 有关详细信息，请参阅[配置设置 - 主机缩放](configuration-settings.md#instance)。 实例计数目前为预览版，并依赖于标准 SKU。 在本快速入门中，可以选择首选的实例计数，请记住任何缩放单元[定价](https://azure.microsoft.com/pricing/details/azure-bastion)注意事项。
   * **公共 IP 地址**：选择“新建”。
   * 公共 IP 地址名称：公共 IP 地址资源的名称。
   * 公共 IP 地址 SKU：预配置为“标准” 。
   * 分配：预配置为“静态” 。 不能对 Azure Bastion 使用“动态”分配。
   * 资源组：与 VM 相同的资源组。

   :::image type="content" source="./media/quickstart-host-portal/create-step-3.png" alt-text="步骤 3 的屏幕截图。":::
1. 在设置完这些值后，请选择“使用默认值创建 Azure Bastion”。 Azure 会验证设置，然后创建主机。 主机和其资源的创建及部署大约需要 5 分钟。

## <a name="remove-vm-public-ip-address"></a><a name="remove"></a>删除 VM 公共 IP 地址

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a><a name="connect"></a>连接到 VM

在将 Bastion 部署到虚拟网络后，屏幕切换到连接页面。

1. 键入虚拟机的用户名和密码。 然后，选择“连接”。

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="屏幕截图显示“使用 Azure Bastion 进行连接”对话框。":::
1. 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。 当系统请求剪贴板访问权限时，请单击“允许”。 这样，你便可以使用屏幕左侧的远程剪贴板箭头。

   * 进行连接时，VM 的桌面看起来可能与示例屏幕截图有所不同。 
   * 连接到 VM 时，使用键盘快捷键可能不会产生与本地计算机上的快捷键相同的行为。 例如，从 Windows 客户端连接到 Windows VM 时，CTRL+ALT+END 是本地计算机上 CTRL+ALT+Delete 的键盘快捷方式。 若要在连接到 Windows VM 时从 Mac 上执行此操作，键盘快捷方式为 Fn+CTRL+ALT+Backspace。

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
