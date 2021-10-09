---
title: 规划 Azure VMware 解决方案部署
description: 了解如何规划 Azure VMware 解决方案部署。
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 09/27/2021
ms.openlocfilehash: 6528727fda867f5f07d7d83b09df1bbf70df6b92
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079459"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>规划 Azure VMware 解决方案部署

规划 Azure VMware 解决方案部署对于实现用于创建虚拟机 (VM) 和进行迁移的成功生产就绪环境至关重要。 在规划过程中，你将确定并收集部署所需的内容。 进行规划时，请务必记录收集的信息，方便在部署过程中参考。 成功的部署会生成一个用于创建虚拟机 (VM) 并进行迁移的生产就绪环境。

在此操作说明中，你将：

> [!div class="checklist"]
> * 确定 Azure 订阅、资源组、区域和资源名称
> * 确定主机大小并确定群集和主机的数量
> * 为符合条件的 Azure 计划请求主机配额
> * 确定用于私有云管理的 /22 CIDR IP 段
> * 确定单个网段
> * 定义虚拟网络网关
> * 定义 VMware HCX 网段

完成后，请按照末尾建议的后续步骤继续执行本入门指南。


## <a name="identify-the-subscription"></a>确定订阅

标识你计划用来部署 Azure VMware 解决方案的订阅。  你可以创建新订阅，也可以使用现有订阅。

>[!NOTE]
>订阅必须与 Microsoft 企业协议 (EA)、云解决方案提供商 (CSP) Azure 计划或 Microsoft 客户协议 (MCA) 相关联。 有关详细信息，请参阅[资格条件](request-host-quota-azure-vmware-solution.md#eligibility-criteria)。

## <a name="identify-the-resource-group"></a>确定别资源组

标识要用于 Azure VMware 解决方案的资源组。  通常会专门为 Azure VMware 解决方案创建一个资源组，但你可以使用现有的资源组。

## <a name="identify-the-region-or-location"></a>确定区域或位置

标识要部署 Azure VMware 解决方案的[区域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)。 

## <a name="define-the-resource-name"></a>定义资源名称

资源名称是一个易记的描述性名称，用来为 Azure VMware 解决方案私有云命名，例如 MyPrivateCloud。

>[!IMPORTANT]
>名称不得超过 40 个字符。 如果名称超出此限制，你将无法创建用于私有云的公共 IP 地址。 

## <a name="identify-the-size-hosts"></a>确定主机大小

确定在部署 Azure VMware 解决方案时要使用的主机大小。  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>确定群集和主机的数目

你执行的第一个 Azure VMware 解决方案部署包含具有一个群集的私有云。 对于部署，你需要定义要部署到第一个群集的主机数。

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]


>[!NOTE]
>若要了解每个群集的主机数、每个私有云的群集数以及每个私有云的主机数方面的限制，请查看 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)。

## <a name="request-a-host-quota"></a>请求主机配额 

请务必提前请求主机配额，这样，在完成规划过程后，你便可立即部署 Azure VMware 解决方案私有云。
在请求主机配额之前，请确保已确定 Azure 订阅、资源组和区域。 此外，请确保已确定主机大小，并确定所需的群集和主机数。

支持团队收到主机配额请求后，最多需要五个工作日来确认请求并分配主机。

- [EA 客户](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [CSP 客户](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)


## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>定义用于私有云管理的 IP 地址段

Azure VMware 解决方案需要 /22 CIDR 网络，例如 `10.0.0.0/22`。 此地址空间将分割成多个更小的网段（子网）并用于 Azure VMware 解决方案管理段，包括 vCenter、VMware HCX、NSX-T 和 vMotion 功能。 该图突出显示 Azure VMware 解决方案管理 IP 地址段。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="显示 Azure VMware 解决方案管理 IP 地址段的图。" border="false":::  

>[!IMPORTANT]
>/22 CIDR 网络地址块不应与本地或 Azure 中的任何现有网段重叠。 有关如何按私有云划分 /22 CIDR 网络的详细信息，请参阅[路由和子网注意事项](tutorial-network-checklist.md#routing-and-subnet-considerations)。



## <a name="define-the-ip-address-segment-for-vm-workloads"></a>定义用于 VM 工作负载的 IP 地址段

与任何 VMware 环境一样，VM 也必须连接到网段。  随着 Azure VMware 解决方案的生产部署的扩展，通常会组合使用本地 L2 扩展段和本地 NSX-T 网段。 

对于初始部署，请确定单个网段（IP 网络），例如 `10.0.4.0/24`。 此网段主要用于在初始部署期间进行测试。  地址块不应与本地或 Azure 中的任何网段重叠，并且不应位于已定义的 /22 网段内。 
  
:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="标识 - 虚拟机工作负荷的 IP 地址段" border="false":::     


## <a name="define-the-virtual-network-gateway"></a>定义虚拟网络网关

Azure VMware 解决方案需要一个 Azure 虚拟网络和一条 ExpressRoute 线路。 定义是否要使用现有的 ExpressRoute 虚拟网络网关还是新建一个 。 如果决定使用新的虚拟网络网关，则将在创建私有云后创建它。 使用现有 ExpressRoute 虚拟网络网关是可接受的；出于规划目的，请记下要使用的 ExpressRoute 虚拟网络网关。 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="显示附加到 Azure VMware 解决方案的 Azure 虚拟网络的图表" border="false":::

>[!IMPORTANT]
>可连接到 Azure 虚拟 WAN 中的虚拟网络网关，但本快速入门不对此进行讨论。

## <a name="define-vmware-hcx-network-segments"></a>定义 VMware HCX 网段

VMware HCX 是一种应用程序移动平台，可简化跨数据中心和云的应用程序迁移、工作负载重新平衡和业务连续性。 可以通过各种迁移类型将 VMware 工作负载迁移到 Azure VMware 解决方案和其他已连接站点。 

VMware HCX 连接器会（自动）部署虚拟设备的子集，这需要多个 IP 段。 创建网络配置文件时，请使用 IP 段。 为 VMware HCX 部署（支持试点或小规模产品用例）确定以下内容。  根据迁移的需求，根据需要进行修改。 

- **管理网络：** 在本地部署 VMware HCX 时，需要为 VMware HCX 确定管理网络。  通常，该网络是本地 VMware 群集使用的同一管理网络。  最起码需要在此网段中确定 **两个** 用于 VMware HCX 的 IP。 所需的 IP 数目可能更多，具体取决于不包括试点或小规模用例在内的部署规模。

  >[!NOTE]
  >为大型环境做准备，不使用用于本地 VMware 群集的管理网络，而是创建一个新的 /26 网络并将该网络作为端口组提供给本地 VMware 群集。  然后，最多可创建 10 个服务网格和 60 个网络扩展器（每个服务网格 1 个）。 可以使用 Azure VMware 解决方案私有云通过每个网络扩展器扩增 **八个** 网络。

- 上行网络：在本地部署 VMware HCX 时，需要为 VMware HCX 确定上行网络。 请使用与管理网络相同的网络。 

- **vMotion 网络：** 在本地部署 VMware HCX 时，需要为 VMware HCX 确定 vMotion 网络。  通常，该网络是本地 VMware 群集用于 vMotion 的同一网络。  最起码需要在此网段中确定 **两个** 用于 VMware HCX 的 IP。 所需的 IP 数目可能更多，具体取决于不包括试点或小规模用例在内的部署规模。

  必须在分布式虚拟交换机或 vSwitch0 上公开 vMotion 网络。 否则请修改环境以适应配置。

  >[!NOTE]
  >许多 VMware 环境对 vMotion 使用非路由网段，这不会造成问题。
  
- **复制网络：** 在本地部署 VMware HCX 时，需要定义一个复制网络。 请使用与管理网络和上行链路网络相同的网络。  如果本地群集主机使用专用的复制 VMkernel 网络，请在此网段中保留 **两个** IP 地址，并将复制 VMkernel 网络用作复制网络。


## <a name="determine-whether-to-extend-your-networks"></a>确定是否要扩展网络

可选择性地将网段从本地扩展到 Azure VMware 解决方案。 如果确实要扩展网络段，请现在按照以下准则定义这些网络：

- 网络必须连接到本地 VMware 环境中的 [vSphere 分布式交换机 (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)。  
- [vSphere 标准交换机](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上的网络无法扩展。

>[!IMPORTANT]
>这些网络在配置的最后一个步骤进行扩展，而不是在部署期间。


## <a name="next-steps"></a>后续步骤
收集并记录所需的信息后，请继续学习下一个教程以创建 Azure VMware 解决方案私有云。

> [!div class="nextstepaction"]
> [部署 Azure VMware 解决方案](deploy-azure-vmware-solution.md)
