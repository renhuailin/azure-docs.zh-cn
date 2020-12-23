---
title: 教程 - 部署和配置 VMware HCX
description: 了解如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 解决方案。
ms.topic: tutorial
ms.date: 11/25/2020
ms.openlocfilehash: a792f8dbc646f61377cf0a88e1a6e386340f23e8
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357885"
---
# <a name="deploy-and-configure-vmware-hcx"></a>部署和配置 VMware HCX

本文将演示如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 本地连接器。 使用 VMware HCX，可以通过各种迁移类型将 VMware 工作负载迁移到 Azure VMware 解决方案和其他已连接站点。 由于 Azure VMware 解决方案部署并配置 HCX 云管理器，因此必须在本地 VMware 数据中心下载、激活和配置 HCX 连接器。

Azure VMware 解决方案中已预先部署 VMware HCX 高级连接器。 它最多支持三个站点连接（本地到云或云到云）。 如果需要三个以上的站点连接，请提交[支持请求](https://portal.azure.com/#create/Microsoft.Support)以启用 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 外接程序。 此加载项目前以预览版提供。 

>[!TIP]
>尽管 VMware 配置最大值工具将本地连接器和云管理器之间的站点对最大值限制为 25，但对于 Advanced Edition 和 Enterprise Edition，许可将该值分别限制为 3 和 10。

>[!NOTE]
>VMware HCX Enterprise 随 Azure VMware 解决方案以预览版服务的形式提供。 它是免费的，并受预览版服务的条款和条件的约束。 在 VMware HCX Enterprise 服务正式发布后，你将提前 30 天收到一则通知，指出计费将进行切换。 你也可选择关闭或退出服务。 无法简单地从 VMware HCX Enterprise 降级地 VMware HCX Advanced。 如果决定降级，必须重新部署，这会导致停机。

首先，请仔细查看[开始之前](#before-you-begin)、[软件版本要求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)和[先决条件](#prerequisites)。 

然后，我们会演练所有必需过程，以便：

> [!div class="checklist"]
> * 下载 VMware HCX 连接器 OVA。
> * 部署本地 VMware HCX OVA（VMware HCX 连接器）。
> * 激活 VMware HCX 连接器。
> * 将本地 VMware HCX 连接器与 Azure VMware 解决方案 HCX 云管理器配对。
> * 配置互连（网络配置文件、计算配置文件和服务网格）。
> * 通过检查设备状态并验证是否可以迁移来完成设置。

完成后，按照本文末尾建议的后续步骤进行操作。  

## <a name="before-you-begin"></a>准备阶段

准备部署时，建议查看以下 VMware 文档：

* [VMware HCX 用户指南](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [VMware HCX 部署注意事项](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware 博客系列 - 云迁移](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [VMware HCX 所需的网络端口](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>先决条件

如果计划使用 VMware HCX Enterprise，请确保已通过 Azure VMware 解决方案支持渠道请求激活。


### <a name="on-premises-vsphere-environment"></a>本地 vSphere 环境

确保本地 vSphere 环境（源环境）满足[最低要求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。 

### <a name="network-and-ports"></a>网络和端口

* 在本地与 Azure VMware 解决方案 SDDC ExpressRoute 线路之间配置 [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)。

* 若要在本地组件与 Azure VMware 解决方案 SDDC 之间进行通信，请打开[所有必需的端口](https://ports.vmware.com/home/VMware-HCX)。

### <a name="ip-addresses"></a>IP 地址

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>下载 VMware HCX 连接器 OVA

将虚拟设备部署到本地 vCenter 之前，必须下载 VMware HCX 连接器 OVA。  

1. 在 Azure 门户中，选择 Azure VMware 解决方案私有云。 

1. 选择“管理” > “连接性”，然后选择“HCX”选项卡，以确定 Azure VMware 解决方案 HCX 管理器的 IP 地址  。 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="VMware HCX IP 地址的屏幕截图。" lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. 选择“管理” > “标识”，然后选择“vCenter 管理员密码”来确定密码  。

   > [!TIP]
   > vCenter 密码是在设置私有云时定义的。 它与用于登录 Azure VMware 解决方案 HCX 管理器的密码相同。

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-admin-password.png" alt-text="查找 HCX 密码。" lightbox="media/tutorial-vmware-hcx/hcx-admin-password.png":::

1. 打开浏览器窗口，使用 cloudadmin\@vsphere.local 用户凭据通过 `https://x.x.x.9` 端口 443 登录到 Azure VMware 解决方案 HCX 管理器

1. 选择“管理” > “系统更新”，然后选择“请求下载链接”  。

1. 选择所选的选项，以下载 VMware HCX 连接器 OVA 文件。

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>在本地部署 VMware HCX 连接器 OVA

1. 在本地 vCenter，选择 [OVF 模板](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)将 VMware HCX 连接器配置到本地 vCenter。 

   > [!TIP]
   > 你将选择在上一部分中下载的 OVA 文件。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="浏览到 OVF 模板的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 选择名称和位置，然后选择需要在其中部署 VMware HCX 连接器的资源或群集。 接下来，查看详细信息和所需的资源，并选择“下一步”。  

1. 查看许可条款。 如果同意，则选择所需的存储和网络，然后选择“下一步”。

1. 选择“存储”，然后选择“下一步”。

1. 选择之前在 [IP 地址先决条件](#ip-addresses)部分定义的 VMware HCX 管理网络段。  然后，选择“下一步”  。

1. 在“自定义模板”中，输入所有必需信息，然后选择“下一步” 。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="用于自定义模板的框的屏幕截图。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 验证配置，然后选择“完成”以部署 VMware HCX 连接器 OVA。
   
   > [!IMPORTANT]
   > 你将需要手动打开虚拟设备。  开机后，请等待 10-15 分钟，然后继续下一步。

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：HCX 设备部署](https://www.youtube.com/embed/UKmSTYrL6AY)视频。 


## <a name="activate-vmware-hcx"></a>激活 VMware HCX

在本地部署 VMware HCX 连接器 OVA 并启动设备后，即可激活。 首先，需要从 Azure VMware 解决方案门户获取许可密钥。

1. 在 Azure VMware 解决方案门户中，转到“管理” > “连接性”，选择“HCX”选项卡，然后选择“添加”   。

1. 使用 admin 凭据登录到 `https://HCXManagerIP:9443` 处的本地 VMware HCX 管理器。 

   > [!TIP]
   > 在 VMware HCX 管理器 OVA 文件部署过程中，你已定义管理员用户密码。

   > [!IMPORTANT]
   > 确保在 VMware HCX 管理器 IP 地址中包括 `9443` 端口号。

1. 在“许可”中，在“HCX 高级密钥”中输入自己的密钥，然后选择“激活”  。  
   
    > [!NOTE]
    > VMware HCX 管理器必须开放 Internet 访问权限或配置一个代理。

1. 在“数据中心位置”中，提供在本地安装 VMware HCX 管理器的最近位置。 然后选择“继续”。 

1. 在“系统名称”中，修改名称或接受默认名称，然后选择“继续” 。
   
1. 选择“是，继续”。

1. 在“连接 vCenter”中，提供你的 vCenter 服务器的 FQDN 或 IP 地址和相应的凭据，然后选择“继续”。
   
   > [!TIP]
   > vCenter 服务器是在数据中心部署 VMware HCX 连接器的位置。

1. 在“配置 SSO/PSC”中，提供你的 Platform Services Controller 的 FQDN 或 IP 地址，然后选择“继续” 。
   
   > [!NOTE]
   > 通常，它与 vCenter FQDN 或 IP 地址相同。

1. 验证输入的信息是否正确，然后选择“重启”。
    
   > [!NOTE]
   > 重启后，会有一段延迟，然后系统才会提示你执行下一步。

服务重启后，你会在出现的屏幕上看到 vCenter 显示为绿色。 VCenter 和 SSO 必须都具有相应的配置参数，它们应该与上一个屏幕相同。

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="具有绿色 vCenter 状态的仪表板的屏幕截图。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：激活 HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) 视频。

   > [!IMPORTANT]
   > 无论你使用的是 VMware HCX Advanced 还是 VMware HCX Enterprise，你都需要安装 VMware [知识库文章 81558](https://kb.vmware.com/s/article/81558) 中的补丁。 

## <a name="configure-the-vmware-hcx-connector"></a>配置 VMware HCX 连接器

现在，你已准备好添加站点配对、创建网络和计算配置文件，以及启用服务，例如迁移、网络扩展或灾难恢复。 

### <a name="add-a-site-pairing"></a>添加站点配对

可以将 Azure VMware 解决方案中的 VMware HCX 云管理器与数据中心内的 VMware HCX 连接器进行连接或配对。 

1. 登录到你的本地 vCenter，然后在“主页”下选择“HCX”。

1. 在“基础结构”下，选择“站点配对”，然后选择“连接到远程站点”选项（在屏幕中间）  。 

1. 输入之前记下的 Azure VMware 解决方案 HCX 云管理器 URL 或 IP 地址 `https://x.x.x.9`、Azure VMware 解决方案 cloudadmin@vsphere.local 用户名以及密码。 然后选择“连接”  。

   > [!NOTE]
   > 若要成功建立站点对，请执行以下操作：
   > * VMware HCX 连接器必须能够通过端口 443 路由到 HCX 云管理器 IP。
   >
   > * 使用与登录到 vCenter 所使用的同一密码。 你已在初始部署屏幕上定义了此密码。

   将看到一个屏幕，其中显示了 Azure VMware 解决方案中的 VMware HCX 云管理器和本地 VMware HCX 连接器已连接（配对）。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="显示 Azure VMware 解决方案和 VMware HCX 连接器中 HCX 管理器配对的屏幕截图。":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：HCX 站点配对](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720)视频。

### <a name="create-network-profiles"></a>创建网络配置文件

VMware HCX 连接器会（自动）部署虚拟设备的子集，这需要多个 IP 段。 创建网络配置文件时，需要使用在 [VMware HCX 网络段预先部署准备和规划阶段](production-ready-deployment-steps.md#vmware-hcx-network-segments)确定的 IP 段。

将创建四个网络配置文件：

   - 管理
   - vMotion
   - 复制
   - 上行

1. 在“基础结构”下，选择“互连” > “多站点服务网格” > “网络配置文件” > “创建网络配置文件”。    

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="用于开始创建网络配置文件的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 对于每个网络配置文件，请选择网络和端口组，提供名称，并创建该段的 IP 池。 然后选择“创建”。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="新网络配置文件的详细信息的屏幕截图。":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：HCX 网络配置文件](https://www.youtube.com/embed/O0rU4jtXUxc)视频。


### <a name="create-a-compute-profile"></a>创建计算配置文件

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

1. 从“选择用于网络扩展的分布式交换机”中，选择要迁移到第 2 层扩展网络上的 Azure VMware 解决方案的虚拟机所在的交换机。 然后选择“继续”。 

   > [!NOTE]
   > 如果不迁移第 2 层扩展网络上的虚拟机，可跳过此步骤。
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="显示选择的分布式虚拟网络和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 查看连接规则，然后选择“继续”。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="显示连接规则和“继续”按钮的屏幕截图。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. 选择“完成”以创建完整配置文件。


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="显示计算配置文件信息的屏幕截图。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：计算配置文件](https://www.youtube.com/embed/e02hsChI3b8)视频。

### <a name="create-a-service-mesh"></a>创建服务网格

现在，可以在本地与 Azure VMware 解决方案 SDDC 之间配置服务网格了。



> [!NOTE]
> 若要使用 Azure VMware 解决方案成功建立服务网格，请执行以下操作：
>
> * 在本地 VMware HCX 连接器的“上行”网络配置文件地址与 Azure VMware 解决方案 HCX 云“上行”网络配置文件地址之间打开端口 UDP 500/4500。
>
> * 请务必查看 [VMware HCX 所需的端口](https://ports.vmware.com/home/VMware-HCX)。

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

   每台设备最多可以有八个 VLAN，但可通过部署另一台设备来添加额外的八个 VLAN。 还必须具有用于额外设备的 IP 空间，每台设备一个 IP。  有关详细信息，请参阅 [VMware HCX 配置限制](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)。
   
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

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：服务网格](https://www.youtube.com/embed/COY3oIws108)视频。

### <a name="optional-create-a-network-extension"></a>（可选）创建网络扩展

如果希望将任何网络从本地环境扩展到 Azure VMware 解决方案，请执行以下步骤：

1. 在“服务”下，选择“网络扩展” > “创建网络扩展”  。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="显示用于开始创建网络扩展的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. 选择要将其扩展到 Azure VMware 解决方案的每个网络，然后选择“下一步”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="显示选择网络的屏幕截图。":::

1. 输入要扩展的每个网络的本地网关 IP，然后选择“提交”。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="显示网关 IP 地址条目的屏幕截图。":::

   只需几分钟时间即可完成网络扩展。 然后，你会看到状态更改为“扩展完成”。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="显示“扩展完成”状态的屏幕截图。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

有关此过程的端到端概述，请观看 [Azure VMware 解决方案：网络扩展](https://www.youtube.com/embed/gYR0nftKui0)视频。


## <a name="next-steps"></a>后续步骤

如果设备互连隧道状态为“正常”且其颜色为绿色，可使用 VMware HCX 来迁移和保护 Azure VMware 解决方案 VM。 Azure VMware 解决方案支持工作负载迁移（带有或不带网络扩展）。 你仍然可以在 vSphere 环境中迁移工作负载，以及在本地创建网络并将 VM 部署到这些网络上。  

有关如何使用 HCX 的详细信息，请参阅 VMware 技术文档：

* [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)
* [使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX 所需的端口](https://ports.vmware.com/home/VMware-HCX)
