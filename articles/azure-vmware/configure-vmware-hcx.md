---
title: 在 Azure VMware 解决方案中配置 VMware HCX
description: 为 Azure VMware 解决方案私有云配置本地 VMware HCX 连接器。
ms.topic: tutorial
ms.date: 07/30/2021
ms.openlocfilehash: 1249a694e01e01d4e7aa31c639c4422be2372ee4
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255465"
---
# <a name="configure-on-premises-vmware-hcx-connector"></a>配置本地 VMware HCX 连接器

[安装 VMware HCX 加载项](install-vmware-hcx.md)后，即可为 Azure VMware 解决方案云私有云配置本地 VMware HCX 连接器。  

在此操作说明中，你将：

* 将本地 VMware HCX 连接器与 Azure VMware 解决方案 HCX 云管理器配对
* 配置网络配置文件、计算配置文件和服务网格
* 检查设备状态并验证迁移是否可行

完成这些步骤后，将获得一个准备就绪的生产环境，可用于创建虚拟机 (VM) 和迁移。 

## <a name="prerequisites"></a>先决条件

- 已安装 [VMware HCX 连接器](install-vmware-hcx.md)。

- 如果计划使用 VMware HCX Enterprise，请确保已通过[支持请求](https://portal.azure.com/#create/Microsoft.Support)启用 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 附加产品。

- [软件版本要求](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)

- 本地 vSphere 环境（源环境）满足[最低要求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。

- 在本地与 Azure VMware 解决方案私有云 ExpressRoute 线路之间配置 [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)。

- 若要在本地组件与 Azure VMware 解决方案私有云之间进行通信，请打开[所有必需的端口](https://ports.vmware.com/home/VMware-HCX)。

- [定义 VMware HCX 网段](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)。  VMware HCX 的主要用例是工作负荷迁移和灾难恢复。

- 请参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html)，了解有关如何使用 HCX 的信息。

## <a name="add-a-site-pairing"></a>添加站点配对

在数据中心内，可以将 Azure VMware 解决方案中的 VMware HCX 云管理器与 VMware HCX 连接器进行连接或配对。

> [!IMPORTANT]
> 尽管 VMware 配置最大值工具将 HCX 本地连接器和 HCX 云管理器之间的站点对最大值限制为 25，但对于 HCX Advanced Edition 和 HCX Enterprise Edition，许可将该值分别限制为 3 和 10。

1. 登录到你的本地 vCenter，然后在“主页”下选择“HCX”。

1. 在“基础结构”下，选择“站点配对”，然后选择“连接到远程站点”选项（在屏幕中间）  。

1. 输入之前记下的 Azure VMware 解决方案 HCX 云管理器 URL 或 IP 地址 `https://x.x.x.9`，以及私有云中具有 CloudAdmin 角色的用户的凭据。 然后选择“连接”  。

   > [!NOTE]
   > 若要成功建立站点对，请执行以下操作：
   > * VMware HCX 连接器必须能够通过端口 443 路由到 HCX 云管理器 IP。
   >
   > * 对于站点配对连接，建议使用来自外部标识源的服务帐户（例如 Active Directory）。 有关为连接服务设置单独帐户的详细信息，请参阅[访问和标识的概念](./concepts-identity.md)。

   将看到一个屏幕，其中显示了 Azure VMware 解决方案中的 VMware HCX 云管理器和本地 VMware HCX 连接器已连接（配对）。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="屏幕截图：Azure VMware 解决方案中的 HCX 管理器和 VMware HCX 连接器的站点配对。":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：HCX 站点配对](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720)视频。

## <a name="create-network-profiles"></a>创建网络配置文件

VMware HCX 连接器会（自动）部署虚拟设备的子集，这需要多个 IP 段。 创建网络配置文件时，请使用在[规划阶段](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)标识的 IP 段。  将创建四个网络配置文件：

   - 管理
   - vMotion
   - 复制
   - 上行

1. 在“基础结构”下，选择“互连” > “多站点服务网格” > “网络配置文件” > “创建网络配置文件”。    

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="屏幕截图：如何在 vSphere 客户端创建网络配置文件。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 对于每个网络配置文件，请选择网络和端口组，提供名称，并创建该段的 IP 池。 然后选择“创建”。

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="屏幕截图：新网络配置文件的详细信息。" lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：HCX 网络配置文件](https://www.youtube.com/embed/O0rU4jtXUxc)视频。

## <a name="create-a-compute-profile"></a>创建计算配置文件

1. 在“基础结构”下，选择“互连” > “计算配置文件” > “创建计算配置文件”   。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="显示用于开始创建计算配置文件的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 输入配置文件的名称，然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="显示计算配置文件名称条目和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 选择要启用的服务，例如迁移、网络扩展或灾难恢复，然后选择“继续”。

   > [!NOTE]
   > 通常，不会有任何变化。

1. 在“选择服务资源”中，选择用于启用选定的 VMware HCX 服务的一个或多个服务资源（群集）。

1. 看到本地数据中心的群集后，选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="显示选定的服务资源和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. 从“选择数据存储”中，选择用于部署 VMware HCX 互连设备的数据存储资源。 然后选择“继续”。

   选择了多个资源时，VMware HCX 会使用所选的第一个资源，直到其容量用尽。

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="显示选定的数据存储资源和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::

1. 从“选择管理网络配置文件”中，选择在前面的步骤中创建的管理网络配置文件。 然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="显示选择的管理网络配置文件和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. 从“选择上行网络配置文件”中，选择在前面的过程中创建的上行网络配置文件。 然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="显示选择的上行网络配置文件和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. 从“选择 vMotion 网络配置文件”中，选择在前面的步骤中创建的 vMotion 网络配置文件。 然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="显示选择的 vMotion 网络配置文件和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. 从“选择 vSphere 复制网络配置文件”中，选择在前面的步骤中创建的复制网络配置文件。 然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="显示选择的复制网络配置文件和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. 从“选择用于网络扩展的分布式交换机”中，选择要迁移到第 2 层扩展网络上 Azure VMware 解决方案的虚拟机所在的交换机。 然后选择“继续”。 

   > [!NOTE]
   > 如果不迁移第 2 层 (L2) 扩展网络上的虚拟机，可跳过此步骤。

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="显示选择的分布式虚拟网络和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 查看连接规则，然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="显示连接规则和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. 选择“完成”以创建完整配置文件。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="显示计算配置文件信息的屏幕截图。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：计算配置文件](https://www.youtube.com/embed/e02hsChI3b8)视频。

## <a name="create-a-service-mesh"></a>创建服务网格

>[!IMPORTANT]
>确保在本地 VMware HCX 连接器的“上行”网络配置文件地址与 Azure VMware 解决方案 HCX 云“上行”网络配置文件地址之间打开端口 UDP 500/4500。


1. 在“基础结构”下，选择“互连” > “服务网格” > “创建服务网格”。  

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="用于开始创建服务网格的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 查看预填充的站点，然后选择“继续”。

   > [!NOTE]
   > 如果这是你的第一个服务网格配置，则无需修改此屏幕。

1. 从下拉列表选择源计算配置文件和远程计算配置文件，然后选择“继续”。

   这些选择定义 VM 可以在其中消耗 VMware HCX 服务的资源。

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="显示选择源计算配置文件的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="显示选择远程计算配置文件的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 查看将启用的服务，然后选择“继续”。

1. 在“高级配置 - 替代上行网络配置文件”中，选择“继续” 。

   上行网络配置文件连接到网络，可以通过该网络访问远程站点的互连设备。

1. 在“高级配置 - 网络扩展设备横向扩展”中进行检查，然后选择“继续”。

   每台设备最多可以有八个 VLAN，但可通过部署另一台设备来添加额外的八个 VLAN。 还必须具有用于更多设备的 IP 空间，每台设备一个 IP。  有关详细信息，请参阅 [VMware HCX 配置限制](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)。

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="显示增加 VLAN 计数的位置的屏幕截图。" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. 在“高级配置 - 流量工程”中进行检查并做出必要的修改，然后选择“继续” 。

1. 查看拓扑预览，然后选择“继续”。

1. 为此服务网格输入一个用户易记名称，然后选择“完成”以完成此操作。

1. 选择“查看任务”以监视部署。

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="显示用于查看任务的按钮的屏幕截图。":::

   当服务网格部署成功完成时，你会看到服务为绿色。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="显示服务上的绿色指示器的屏幕截图。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 通过检查设备状态验证服务网格的运行状况。

1. 选择“互联” > “设备” 。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="显示用于检查设备状态的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

   >[!NOTE]
   >建立服务网格后，可能会注意到私有云中具有新的数据存储和新主机。 这是建立服务网格后完全正常的行为。
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="显示 HCX 服务网格数据存储和主机的屏幕截图。" lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：服务网格](https://www.youtube.com/embed/COY3oIws108)视频。

## <a name="create-a-network-extension"></a>创建网络扩展

这是一个可选步骤，用于将任何网络从本地环境扩展到 Azure VMware 解决方案。

1. 在“服务”下，选择“网络扩展” > “创建网络扩展”  。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="显示用于开始创建网络扩展的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. 选择要将其扩展到 Azure VMware 解决方案的每个网络，然后选择“下一步”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="显示选择网络的屏幕截图。":::

1. 输入要扩展的每个网络的本地网关 IP，然后选择“提交”。

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="显示网关 IP 地址条目的屏幕截图。":::

   只需几分钟时间即可完成网络扩展。 然后，你会看到状态更改为“扩展完成”。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="显示“扩展完成”状态的屏幕截图。":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：网络扩展](https://www.youtube.com/embed/gYR0nftKui0)视频。

## <a name="next-steps"></a>后续步骤

如果 HCX 互连隧道状态为“正常”且其颜色为绿色，可使用 VMware HCX 来迁移和保护 Azure VMware 解决方案 VM。 Azure VMware 解决方案支持工作负载迁移（带有或不带网络扩展）。 你仍然可以在 vSphere 环境中迁移工作负载，以及在本地创建网络并将 VM 部署到这些网络上。 有关详细信息，请参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)。

