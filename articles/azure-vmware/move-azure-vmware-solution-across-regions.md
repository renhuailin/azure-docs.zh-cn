---
title: 跨区域移动 Azure VMware 解决方案资源
description: 本文介绍如何将 Azure VMware 解决方案资源从一个 Azure 区域移动到另一个区域。
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 116350ccf32c18e232f22788d5dd3d693d7e1e9d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323655"
---
# <a name="move-azure-vmware-solution-resources-to-another-region"></a>将 Azure VMware 解决方案资源移动到另一个区域

>[!IMPORTANT]
>本文中的步骤严格适用于将一个区域中的 Azure VMware 解决方案（源）移动到另一个区域中的 Azure VMware 解决方案（目标）。 

你可能会由于多种原因而将 Azure VMware 解决方案移动到不同的区域。 例如，部署仅在特定区域中可用的功能或服务、满足策略和治理要求或是满足容量规划要求。 

本文可帮助你进行规划并将 Azure VMware 解决方案从一个 Azure 区域移动到另一个，例如 Azure 区域 A 到 Azure 区域 B。


下图显示两个 Azure VMware 解决方案环境之间的建议 ExpressRoute 连接。  在两个环境之间创建了一个 HCX 站点配对和服务网格。  HCX 迁移流量和第 2 层扩展在两个环境之间移动（通过红线进行描绘）。 有关 VMware 建议 HCX 规划，请参阅[规划 HCX 迁移](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section1)。

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-2.png" alt-text="显示源与目标 Azure VMware 解决方案环境之间的 ExpressRoute Global Reach 通信的图。" border="false":::

>[!NOTE]
>无需将任何工作流迁移回本地，因为流量会在私有云（源和目标）之间流动：
>
>Azure VMware 解决方案私有云（源）> ExpressRoute 网关（源）> ExpressRoute 网关（目标）> Azure VMware 解决方案私有云（目标）

下图显示两个 Azure VMware 解决方案环境之间的连接。 

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-connectivity-diagram.png" alt-text="显示源与目标 Azure VMware 解决方案环境之间的通信的图。" border="false":::


本文将指导完成执行以下任务的步骤： 

> [!div class="checklist"]
> * 准备并规划移动到另一个 Azure 区域
> * 在两个 Azure VMware 解决方案私有云之间建立网络连接
> * 从 Azure VMware 解决方案源环境导出配置
> * 将支持的配置元素重新应用于 Azure VMware 解决方案目标环境
> * 使用 VMware HCX 迁移工作负载

## <a name="prerequisites"></a>必备条件

- [VMware HCX 设备已升级到最新补丁](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-02DB88E1-EC81-434B-9AE9-D100E427B31C.html)以避免迁移问题（如果有）。

- 源的本地内容库是[已发布的内容库](https://docs.vmware.com/en/VMware-Validated-Design/services/deployment-of-vrealize-suite-2019-on-vmware-cloud-foundation-310/GUID-59E0CBA1-2CF6-488D-AA58-C97C76FD8159.html?hWord=N4IghgNiBcIA4FcBGECWBnAFgAgMYHsA7AFwFMTs0kAnMagTxAF8g)。

## <a name="prepare"></a>准备

以下步骤演示如何准备 Azure VMware 解决方案私有云以移动到另一个 Azure VMware 解决方案私有云。 

### <a name="export-the-source-configuration"></a>导出源配置

1. 从源中[导出扩展段、防火墙规则、端口详细信息和路由表](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-FCE6567E-1174-49CC-90F1-BA7B695B28F0.html)。

1. [将清单列表视图的内容导出到 CSV 文件](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-C0E8DD52-677E-464F-A3EA-044EE20B7B92.html)。

1. [将工作负载分为迁移组（迁移批）](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)。


### <a name="deploy-the-target-environment"></a>部署目标环境

需要先[部署目标环境](plan-private-cloud-deployment.md)，然后才能移动源配置。


### <a name="back-up-the-source-configuration"></a>备份源配置

备份 Azure VMware 解决方案（源）配置（包括 VC、NSX-T 以及防火墙策略和规则）。 

- 计算：导出现有清单配置。 对于清单备份，可以使用 RVtool（一种开放源代码应用）。

- 网络和防火墙策略和规则：在 Azure VMware 解决方案中，创建与源环境相同的网段。

Azure VMware 解决方案支持所有备份解决方案。 需要 CloudAdmin 权限才能进行安装、备份数据和还原备份。 有关详细信息，请参阅[适用于 Azure VMware 解决方案 VM 的备份解决方案](ecosystem-back-up-vms.md)。

- 使用 Commvault 解决方案进行 VM 工作负荷备份：

   - 从命令中心为 Azure VMware 解决方案 vCenter [创建 VMware 客户端](https://documentation.commvault.com/commvault/v11_sp20/article?p=119380.htm)。

   - 使用备份所需的 VM [创建 VM 组](https://documentation.commvault.com/commvault/v11_sp20/article?p=121182.htm)。

   - [在 VM 组 上运行备份](https://documentation.commvault.com/commvault/v11_sp20/article?p=121657.htm)。

   - [还原 VM](https://documentation.commvault.com/commvault/v11_sp20/article?p=87275.htm)。

- 使用 [Veritas NetBackup 解决方案](https://vrt.as/nb4avs)的 VM 工作负载备份。 

>[!TIP]
>可以使用 [Azure 资源转移器](../resource-mover/move-region-within-resource-group.md?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)验证和迁移支持的资源列表，以跨区域移动（依赖于 Azure VMware 解决方案）。

### <a name="locate-the-source-expressroute-circuit-id"></a>找到源 ExpressRoute 线路 ID

1. 从源中登录 [Azure 门户](https://portal.azure.com/)。

2. 选择“管理” > “连接” > “ExpressRoute”  。

3. 复制源的 ExpressRoute ID。  在私有云之间进行对等互连时需要它。


### <a name="create-the-targets-authorization-key"></a>创建目标的授权密钥

1. 从目标登录 [Azure 门户](https://portal.azure.com/)。

1. 选择“管理” > “连接” > “ExpressRoute”，然后选择“+ 请求授权密钥”   。

   :::image type="content" source="media/expressroute-global-reach/start-request-authorization-key.png" alt-text="屏幕截图显示了如何请求 ExpressRoute 授权密钥。" border="true" lightbox="media/expressroute-global-reach/start-request-authorization-key.png":::

1. 为该授权密钥提供一个名称，然后选择“创建”。

   创建该密钥可能需要大约 30 秒。 创建后，新密钥会出现在私有云的授权密钥列表中。

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="屏幕截图显示了 ExpressRoute Global Reach 授权密钥。":::
  
1. 复制授权密钥和 ExpressRoute ID。 你需要使用它们来完成对等互连。 授权密钥会在一段时间后消失，因此请在它出现时立即复制它。

### <a name="peer-between-private-clouds"></a>在私有云之间对等互连

现在，你拥有两个环境的 ExpressRoute 线路 ID 和授权密钥，可以将源对等互连到目标。 你会使用私有云 ExpressRoute 线路的资源 ID 和授权密钥来完成对等互连。
 
1. 在目标中，使用与源 ExpressRoute 线路相同的订阅登录 [Azure 门户](https://portal.azure.com)。

1. 在“管理”下，选择“连接” > “ExpressRoute Global Reach” > “添加”  。

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="显示 Azure VMware 解决方案私有云中“ExpressRoute Global Reach”选项卡的屏幕截图。":::

1. 粘贴在上一步中创建的 ExpressRoute 线路 ID 和目标的授权密钥。  然后选择“创建”：

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="屏幕截图显示用于输入连接信息的对话框。":::   

### <a name="create-a-site-pairing-between-private-clouds"></a>在私有云之间创建站点配对

建立连接后，会在私有云之间创建 VMware HCX 站点配对，以促进 VM 的迁移。 可以将 Azure VMware 解决方案中的 VMware HCX 云管理器与数据中心内的 VMware HCX 连接器进行连接或配对。 

1. 登录到你的源 vCenter，然后在“主页”下选择“HCX”。

1. 在“基础结构”下，选择“站点配对”，然后选择“连接到远程站点”选项（在屏幕中间）  。 

1. 输入之前记下的 Azure VMware 解决方案 HCX 云管理器 URL 或 IP 地址 `https://x.x.x.9`、Azure VMware 解决方案 cloudadmin\@vsphere.local 用户名以及密码。 然后选择“连接”  。

   > [!NOTE]
   > 若要成功建立站点对，请执行以下操作：
   > * VMware HCX 连接器必须能够通过端口 443 路由到 HCX 云管理器 IP。
   >
   > * 使用与登录到 vCenter 所使用的同一密码。 你已在初始部署屏幕上定义了此密码。

   将看到一个屏幕，其中显示了 Azure VMware 解决方案中的 VMware HCX 云管理器和本地 VMware HCX 连接器已连接（配对）。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="显示 Azure VMware 解决方案和 VMware HCX 连接器中 HCX 管理器配对的屏幕截图。":::

### <a name="create-a-service-mesh-between-private-clouds"></a>在私有云之间创建服务网格

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

1. 查看要启用的服务，然后选择“继续”。  

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

## <a name="move"></a>移动

以下步骤演示如何将 Azure VMware 解决方案私有云资源移动到另一个区域中的其他 Azure VMware 解决方案私有云。 

在本部分中，你将迁移：

- 资源池配置和文件夹创建

- VM 模板和关联标记

- 基于源端口组和关联 VLAN 的逻辑段部署 

- 网络安全服务和组

- 基于源防火墙策略的网关防火墙策略和规则

### <a name="migrate-the-source-vsphere-configuration"></a>迁移源 vSphere 配置

在此步骤中，你会复制源的 vSphere 配置，并将其移动到目标环境。 

1. 从该源的 vCenter，使用相同的资源池配置，并在目标 vCenter 上 [创建相同的资源池配置](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html#example-creating-resource-pools-4)。

2. 从该源的 vCenter，使用相同的 VM 文件夹名称，并在目标 vCenter 上的“文件夹”下，[创建相同的 VM 文件夹](https://docs.vmware.com/en/VMware-Validated-Design/6.1/sddc-deployment-of-cloud-operations-and-automation-in-the-first-region/GUID-9D935BBC-1228-4F9D-A61D-B86C504E469C.html)。

3. 使用 VMware HCX 将所有 VM 模板从源的 vCenter 迁移到目标的 vCenter 时。

   1. 从源中，将现有模板转换为 VM，然后将它们迁移到目标。

   2. 在目标中，将 VM 转换为 VM 模板。

4. 在源环境中，使用相同的 VM 标记名称并[在目标 vCenter 上创建它们](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vcenterhost.doc/GUID-05323758-1EBF-406F-99B6-B1A33E893453.html)。 

5. 在源的 vCenter 内容库中，使用订阅的库选项将 ISO、OVF、OVA 和 VM 模板复制到目标内容库：

   1. 如果尚未发布内容库，请选择“启用发布”选项。

   2. 在源的内容库中，复制已发布的库的 URL。

   3. 从目标[创建订阅的内容库](deploy-vm-content-library.md)，其中包含来自源库的 URL。

   4. 选择“立即同步”。


### <a name="configure-the-target-nsx-t-environment"></a>配置目标 NSX-T 环境

在此步骤中，你将使用源 NSX-T 配置来配置目标 NSX-T 环境。

>[!NOTE]
>你在源 NSX-T 上配置了多个功能，因此必须从源 NXS-T 进行复制或读取，然后在目标私有云中重新创建。 将源迁移到目标 AVS 私有云时，使用 L2 扩展保留 VM 的相同 IP 地址和 Mac 地址，以避免由于 IP 更改和相关配置而导致的故障时间。

1. [配置 NSX 网络组件](tutorial-nsx-t-network-segment.md)（在目标环境中默认第 1 层网关下需要）。

1. [创建安全组配置](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html)。

1. [创建分布式防火墙策略和规则](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html)。

1. [创建网关防火墙策略和规则](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-DE6FE8CB-017E-41C8-85FC-D71CF27F85C2.html)。

1. [创建 DHCP 服务器或 DHCP 中继服务](configure-dhcp-azure-vmware-solution.md)。 

1. [配置端口镜像](configure-port-mirroring-azure-vmware-solution.md)。

1. [配置 DNS 转发器](configure-dns-azure-vmware-solution.md)。

1. [配置新的第 1 层网关（默认网关以外的网关）](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A6042263-374F-4292-892E-BC86876325A4.html)。  此配置基于源上配置的 NSX。 

### <a name="migrate-the-vms-from-the-source"></a>从源迁移 VM 

在此步骤中，你会使用 VMware HCX 将 VM 从源迁移到目标。 可以选择从源中执行第 2 层扩展，并使用 HCX 将 VM 从源 vMotion 到目标（中断最少）。 

除了 vMotion 以外，还建议使用其他方法，例如大容量和冷 vMotion。  了解详细信息：

- [规划 HCX 迁移](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)

- [使用 HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-14D48C15-3D75-485B-850F-C5FCB96B5637.html)

### <a name="cutover-extended-networks"></a>直接转换扩展网络 

在此步骤中，你会执行最后一个网关直接转换，以终止扩展网络。 还会将网关从源 Azure VMware 解决方案环境移动到目标环境（迁移）。

>[!IMPORTANT]
>在将 VLAN 工作负载迁移到目标 Azure VMware 解决方案环境后，必须执行网关直接转换。 此外，源和目标环境中不应有任何 VM 依赖项。

在进行网关直接转换之前，请验证所有迁移的工作负载服务和性能。 一旦应用程序和 Web 服务所有者接受性能（除了任何延迟问题），你便可以继续执行网关直接转换。  完成直接转换后，需要修改公共 DNS A 和 PTR 记录。 

有关 VMware 建议，请参阅[扩展网络的直接转换](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section9)。


### <a name="public-ip-dnat-for-migrated-dmz-vms"></a>用于已迁移的 DMZ VM 的公共 IP DNAT

至此，已将工作负载迁移到目标环境。 必须可以从公共 Internet 访问这些应用程序工作负载。 目标环境提供了两种托管任何应用程序的方式。 应用程序可以：

- 在应用程序网关负载均衡器下托管和发布。

- 通过 vWAN 中的公共 IP 功能发布。

公共 IP 通常是转换为 Azure 防火墙中的目标 NAT。 使用 DNAT 规则时，防火墙策略会将公共 IP 地址请求转换为带有端口的专用地址（Web 服务器）。 有关详细信息，请参阅[如何使用 Azure Virtual WAN 中的公共 IP 功能](./enable-public-internet-access.md)。

>[!NOTE]
>默认情况下，SNAT 在 Azure VMware 解决方案中进行配置，因此你必须从“管理”选项卡下的“Azure VMware 解决方案私有云连接设置”中启用 SNAT。

## <a name="decommission"></a>解除授权

在最后一步中，你会验证是否已成功迁移所有 VM 工作负载，包括网络配置。 如果没有依赖项，则可以断开 HCX 服务网格、站点配对以及来自源环境的网络连接。 

>[!NOTE]
>解除私有云的授权后，无法撤消人名，因为配置和数据会丢失。


## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Microsoft.AVS 的移动操作支持](../azure-resource-manager/management/move-support-resources.md#microsoftavs)
- [网络资源移动指南](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [针对虚拟机的移动指南](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [针对应用服务资源的移动指南](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
