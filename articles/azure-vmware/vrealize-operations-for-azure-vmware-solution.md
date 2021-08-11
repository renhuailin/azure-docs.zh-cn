---
title: 为 Azure VMware 解决方案配置 vRealize Operations
description: 了解如何为 Azure VMware 解决方案私有云设置 vRealize Operations。
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 127280d22e26a88b81016aeb53432dade45dc010
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108769302"
---
# <a name="configure-vrealize-operations-for-azure-vmware-solution"></a>为 Azure VMware 解决方案配置 vRealize Operations


vRealize Operations Manager 是一个操作管理平台，可让 VMware 基础结构管理员监视系统资源。 这些系统资源可以是应用程序级或基础结构级（物理和虚拟）对象。 大多数 VMware 管理员已使用 vRealize Operations 来监视和管理 VMware 私有云组件 - vCenter、ESXi、NSX-T、vSAN 和 VMware HCX。  每个预配的 Azure VMware 解决方案私有云都包括专用 vCenter、NSX-T、vSAN 和 HCX 部署。 

请先仔细查看[开始之前](#before-you-begin)和[先决条件](#prerequisites)。 接下来，我们将介绍两种典型的部署拓扑：

> [!div class="checklist"]
> * [管理 Azure VMware 解决方案的本地 vRealize Operations 部署](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [在 Azure VMware 解决方案上运行的 vRealize Operations 部署](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>准备阶段
* 查看 [vRealize Operations Manager 产品文档](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)，了解有关部署 vRealize Operations 的详细信息。 
* 查看基本的 Azure VMware 解决方案软件定义数据中心 (SDDC) [教程系列](tutorial-network-checklist.md)。
* （可选）查看 [vRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 产品文档，了解用于管理 Azure VMware 解决方案部署选项的本地 vRealize Operations。 


## <a name="prerequisites"></a>先决条件
* 已安装 [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)。
* 在本地与 Azure VMware 解决方案 SDDC 之间配置了 VPN 或 Azure ExpressRoute。
* 在 Azure 中部署了 Azure VMware 解决方案私有云。



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>管理 Azure VMware 解决方案的本地 vRealize Operations 部署
大多数客户使用 vRealize Operations 的现有本地部署来管理一个或多个本地 vCenter 域。 当他们预配 Azure VMware 解决方案私有云时，他们使用 Azure ExpressRoute 或第 3 层 VPN 解决方案将其本地环境与其私有云连接起来。  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="管理 Azure VMware 解决方案的本地 vRealize Operations 部署" border="false":::

若要将 vRealize Operations 功能扩展到 Azure VMware 解决方案私有云，需要[为私有云资源创建适配器实例](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)。 该实例可从 Azure VMware 解决方案私有云收集数据，并将其引入本地 vRealize Operations。 本地 vRealize Operations Manager 实例可以直接连接到 Azure VMware 解决方案上的 vCenter 和 NSX-T 管理器。 或者，可以在 Azure VMware 解决方案私有云上部署 vRealize Operations Remote Collector。 收集器会压缩并加密从私有云中收集的数据，再通过 ExpressRoute 或 VPN 网络将其发送到在本地运行的 vRealize Operations Manager。 

> [!TIP]
> 有关安装 vRealize Operations Manager 的分步指南，请参阅 [VMware 文档](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)。 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>在 Azure VMware 解决方案上运行的 vRealize Operations 部署

另一种方法是在私有云中的 vSphere 群集上部署 vRealize Operations Manager 的实例。 

>[!IMPORTANT]
>VMware 目前不支持此方法。

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="在 Azure VMware 解决方案上运行的 vRealize Operations" border="false":::

部署实例后，可以配置 vRealize Operations 以从 vCenter、ESXi、NSX-T、vSAN 和 HCX 收集数据。 



## <a name="known-limitations"></a>已知的限制

- Azure VMware 解决方案中的 cloudadmin\@vsphere.local 用户拥有[有限的权限](concepts-identity.md)。  Azure VMware 解决方案中的虚拟机 (VM) 不支持使用 VMware 工具进行来宾内内存收集。  在这种情况下，处于活动状态和已使用的内存利用率将继续工作。
- 基于主机的业务意图进行的工作负载优化不起作用，因为 Azure VMware 解决方案管理群集配置，包括 DRS 设置。
- vRealize Operations Manager 8.0 和更高版本完全支持使用基于群集的业务意图在 SDDC 中进行跨群集放置的工作负载优化。 但是，工作负载优化并不知道资源池并将 VM 置于群集级别。 用户可以在 Azure VMware 解决方案 vCenter Server 界面中手动更正此问题。
- 无法使用 Azure VMware 解决方案 vCenter Server 凭据登录到 vRealize Operations Manager。 
- Azure VMware 解决方案不支持 vRealize Operations Manager 插件。

使用 vCenter Server 云帐户将 Azure VMware 解决方案 vCenter 连接到 vRealize Operations Manager 时，将显示一条警告：

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="警告：适配器实例创建成功":::

出现此警告是因为 Azure VMware 解决方案中的 cloudadmin\@vsphere.local 用户没有足够的权限执行注册所需的所有 vCenter Server 操作。 但是，这些权限足以让适配器实例执行数据收集，如下所示：

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="执行数据收集的适配器实例":::

有关详细信息，请参阅[配置 vCenter 适配器实例所需的权限](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)。

<!-- LINKS - external -->


<!-- LINKS - internal -->




