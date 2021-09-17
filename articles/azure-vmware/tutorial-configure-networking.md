---
title: 教程 - 在 Azure 中为 VMware 私有云配置网络
description: 了解如何在 Azure 中创建和配置在部署私有云时所需的网络
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/30/2021
ms.openlocfilehash: 61a1c1c45455c9edc402aca1e5471f3ed95a8d66
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069547"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>教程：在 Azure 中为 VMware 私有云配置网络

Azure VMware 解决方案私有云需要一个 Azure 虚拟网络。 由于 Azure VMware 解决方案不支持本地 vCenter，因此需要执行额外的步骤与本地环境集成。 此外，还需要设置 ExpressRoute 线路和虚拟网络网关。

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络 
> * 创建虚拟网络网关
> * 将 ExpressRoute 线路连接到网关

>[!NOTE]
>创建新 vNet 之前，请评估 Azure 中是否已有现有的 vNet 并计划使用它连接到 Azure VMware 解决方案；或者是否完全创建新 vNet。  
>* 若要使用现有 vNet，请使用“连接”下的[“Azure vNet 连接”](#select-an-existing-vnet)选项卡。 
>* 若要创建新 vNet，请[“Azure vNet 连接”](#create-a-new-vnet)选项卡或[手动](#create-a-vnet-manually)创建一个。

## <a name="connect-with-the-azure-vnet-connect-feature"></a>使用 Azure vNet 连接功能进行连接

可以使用“Azure vNet 连接”功能来使用现有 vNet，或创建新 vNet 以连接到 Azure VMware 解决方案。   

>[!NOTE]
>vNet 中的地址空间不能与 Azure VMware 解决方案私有云 CIDR 重叠。


### <a name="select-an-existing-vnet"></a>选择现有 vNet

选择现有 vNet 时，创建 vNet 和其他资源的 Azure 资源管理器 (ARM) 模板会进行部署。 在这种情况下，资源是公共 IP、网关、网关连接和 ExpressRoute 授权密钥。 如果所有内容都已设置，则部署不会更改任何内容。 但是，如果缺少任何内容，则会自动创建。 例如，如果缺少 GatewaySubnet，则部署期间会添加它。

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“连接” 。

2. 选择“Azure vNet 连接”选项卡，然后选择现有 vNet。

   :::image type="content" source="media/networking/azure-vnet-connect-tab.png" alt-text="显示“连接”下的“Azure vNet 连接”选项卡的屏幕截图，其中选择了现有 vNet。":::

3. 选择“保存”。

   此时，vNet 会验证是否检测到 Azure VMware 解决方案与 vNet 之间的重叠 IP 地址空间。 如果检测到，请更改私有云或 vNet 的网络地址，以便它们不会重叠。 


### <a name="create-a-new-vnet"></a>创建新 vNet

创建新 vNet 时，会自动创建连接到 Azure VMware 解决方案所需的组件。

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“连接” 。

2. 选择“Azure vNet 连接”选项卡，然后选择“新建”。

   :::image type="content" source="media/networking/azure-vnet-connect-tab-create-new.png" alt-text="显示“连接”下的“Azure vNet 连接”选项卡的屏幕截图。":::

3. 提供或更新新 vNet 的信息，然后选择“确定”。

   此时，vNet 会验证是否检测到 Azure VMware 解决方案与 vNet 之间的重叠 IP 地址空间。 如果检测到，请更改私有云或 vNet 的网络地址，以便它们不会重叠。 

   :::image type="content" source="media/networking/create-new-virtual-network.png" alt-text="显示“创建虚拟网络”窗口的屏幕截图。":::

随即会在订阅和资源组中创建具有提供的地址范围和 GatewaySubnet 的 vNet。  


## <a name="connect-to-the-private-cloud-manually"></a>手动连接到私有云

### <a name="create-a-vnet-manually"></a>手动创建 vNet

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



### <a name="create-a-virtual-network-gateway"></a>创建虚拟网络网关

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

### <a name="connect-expressroute-to-the-virtual-network-gateway"></a>将 ExpressRoute 连接到虚拟网络网关

现在，你已部署了虚拟网络网关，你将在网关与 Azure VMware 解决方案私有云之间添加连接。

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 使用 vNet 连接功能创建虚拟网络
> * 手动创建虚拟网络
> * 创建虚拟网络网关
> * 将 ExpressRoute 线路连接到网关


继续学习下一教程，了解如何创建 vCenter 中用于 VM 的 NSX-T 网段。

> [!div class="nextstepaction"]
> [创建 NSX-T 网段](./tutorial-nsx-t-network-segment.md)
