---
title: 教程 - 在 Azure 中为 VMware 私有云配置网络
description: 了解如何在 Azure 中创建和配置在部署私有云时所需的网络
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 04/23/2021
ms.openlocfilehash: 10326a07e5838dd5fe2264029c857f5ad49f5811
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442013"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>教程：在 Azure 中为 VMware 私有云配置网络

Azure VMware 解决方案私有云需要一个 Azure 虚拟网络。 由于 Azure VMware 解决方案不支持本地 vCenter，因此需要执行额外的步骤才能与本地环境集成。 此外，还需要设置 ExpressRoute 线路和虚拟网络网关。

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建虚拟网络网关
> * 将 ExpressRoute 线路连接到网关


## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到[创建私有云教程](tutorial-create-private-cloud.md)中创建的资源组，然后选择“+ 添加”以定义新资源。 

1. 在“搜索市场”文本框中，键入“虚拟网络”。 找到“虚拟网络”资源并将其选中。

1. 在“虚拟网络”页上，选择“创建”来为私有云设置虚拟网络。

1. 在“创建虚拟网络”页上，输入虚拟网络的详细信息。

1. 在“基本信息”选项卡上输入虚拟网络的名称，选择相应的区域，然后选择“下一步: IP 地址”。

1. 在“IP 地址”选项卡上的“IPv4 地址空间”下，输入在上一教程中创建的地址空间 。

   > [!IMPORTANT]
   > 使用的地址空间不得与在上一教程中创建私有云时所用的地址空间重叠。

1. 选择“+ 添加子网”，然后在“添加子网”页上，指定子网名称和相应的地址范围 。 完成后，选择“添加”。

1. 选择“查看 + 创建”。

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="显示新虚拟网络设置的屏幕截图。" border="true":::

1. 验证信息并选择“创建”。 部署完成后，会在资源组中看到你的虚拟网络。

## <a name="create-a-virtual-network-gateway"></a>创建虚拟网络网关

你已经创建了一个虚拟网络，现在将创建一个虚拟网络网关。

1. 在资源组中，选择“+ 添加”以添加新资源。

1. 在“搜索市场”文本框中，键入“虚拟网络网关” 。 找到“虚拟网络”资源并将其选中。

1. 在“虚拟网络网关”页上选择“创建” 。

1. 在“创建虚拟网络网关”页的“基本信息”选项卡上，为字段提供值，然后选择“查看 + 创建”。  

   | 字段 | 值 |
   | --- | --- |
   | **订阅** | 已使用资源组所属的订阅预填充的值。 |
   | **资源组** | 为当前资源组预填充的值。 值应为在前面的测试中创建的资源组。 |
   | **名称** | 输入虚拟网络网关的唯一名称。 |
   | **区域** | 选择虚拟网络网关的地理位置。 |
   | **网关类型** | 选择“ExpressRoute”。 |
   | **SKU** | 保留默认值：“标准”。 |
   | **虚拟网络** | 选择前面创建的虚拟网络。 如果未看到虚拟网络，请确保网关的区域与虚拟网络的区域匹配。 |
   | **网关子网地址范围** | 选择虚拟网络时会填充此值。 不要更改默认值。 |
   | **公共 IP 地址** | 选择“新建”。 |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="显示虚拟网络网关详细信息的屏幕截图。" border="true":::

1. 验证详细信息是否正确，然后选择“创建”以开始部署虚拟网络网关。 
1. 部署完成后，转到下一部分，将 ExpressRoute 连接连接到包含 Azure VMware 解决方案私有云的虚拟网络网关。

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>将 ExpressRoute 连接到虚拟网络网关

现在，你已部署了虚拟网络网关，你将在网关与 Azure VMware 解决方案私有云之间添加连接。

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建虚拟网络网关
> * 将 ExpressRoute 线路连接到网关


继续学习下一教程，了解如何创建 vCenter 中用于 VM 的 NSX-T 网段。

> [!div class="nextstepaction"]
> [创建 NSX-T 网段](./tutorial-nsx-t-network-segment.md)