---
title: 规划 Azure VMware 解决方案部署
description: 本文概述了 Azure VMware 解决方案部署工作流。  最终结果是一个就绪的可用于创建和迁移虚拟机 (VM) 的环境。
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 05/13/2021
ms.openlocfilehash: 0b8826582957e02ec3f18d09dec7283cc92188a1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097113"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>规划 Azure VMware 解决方案部署

本快速入门提供用于识别和收集部署所需的信息的规划过程。 规划部署时，请务必记录收集的信息，方便在部署过程中参考。

执行所述步骤可获得一个用于创建虚拟机 (VM) 和实现迁移的生产就绪环境。 

>[!TIP]
>若想跟踪要收集的数据，请使用 [VMware HCX 计划清单](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/)。


## <a name="request-a-host-quota"></a>请求主机配额 

在准备创建 Azure VMware 解决方案资源时，务必提前请求主机配额。 你可以现在请求主机配额，这样，在计划过程完成后，你便可立即部署 Azure VMware 解决方案私有云。 支持团队收到主机配额请求后，最多需要五个工作日来确认请求并分配主机。 如果你已有 Azure VMware 解决方案私有云，但想要分配更多主机，请完成相同的过程。 有关详细信息，请参阅以下链接（具体选择哪个链接取决于你拥有的订阅类型）：
- [EA 客户](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [CSP 客户](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)

## <a name="identify-the-subscription"></a>确定订阅

标识你计划用来部署 Azure VMware 解决方案的订阅。  你可以创建新订阅，也可以重用现有的订阅。

>[!NOTE]
>订阅必须与 Microsoft 企业协议或云解决方案提供商 Azure 计划相关联。 有关详细信息，请参阅[如何启用 Azure VMware 解决方案资源](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider)。

## <a name="identify-the-resource-group"></a>确定别资源组

标识要用于 Azure VMware 解决方案的资源组。  通常会专门为 Azure VMware 解决方案创建一个资源组，但你可以使用现有的资源组。

## <a name="identify-the-region-or-location"></a>确定区域或位置

标识要部署 Azure VMware 解决方案的区域。  有关详细信息，请参阅[“可用 Azure 产品（按区域）”指南](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)。

## <a name="identify-the-resource-name"></a>确定资源名称

定义要在部署过程中使用的资源名称。  资源名称是一个易记的描述性名称，用来为 Azure VMware 解决方案私有云命名。

>[!IMPORTANT]
>名称不得超过 40 个字符。 如果名称超出此限制，你将无法创建用于私有云的公共 IP 地址。 

## <a name="identify-the-size-hosts"></a>确定主机大小

确定在部署 Azure VMware 解决方案时要使用的主机大小。  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>确定群集和主机的数目

你执行的第一个 Azure VMware 解决方案部署包含具有一个群集的私有云。 对于部署，你需要定义要部署到第一个群集的主机数。

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>定义用于私有云管理的 IP 地址段

规划部署的第一步是规划 IP 分段。 Azure VMware 解决方案需要 /22 CIDR 网络。 此地址空间将分割成多个更小的网段（子网）并用于 Azure VMware 解决方案管理段，包括 vCenter、VMware HCX、NSX-T 和 vMotion 功能。 下面的可视化效果突出显示了此网段的使用位置。

此 /22 CIDR 网络地址块不应与本地或 Azure 中的任何现有网段重叠。

**示例：** 10.0.0.0/22

若要详细了解针对每种私有云的 /22 CIDR 网络的拆分方式，请参阅[网络规划清单](tutorial-network-checklist.md#routing-and-subnet-considerations)。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="标识 - IP 地址段" border="false":::  

## <a name="define-the-ip-address-segment-for-vm-workloads"></a>定义用于 VM 工作负载的 IP 地址段

与任何 VMware 环境一样，VM 也必须连接到网段。 在 Azure VMware 解决方案中，有两种类型的段，L2 扩展段（稍后介绍）和 NSX-T 网段。 随着 Azure VMware 解决方案的生产部署的扩展，通常会组合使用本地 L2 扩展段和本地 NSX-T 网段。 若要规划初始部署，请在 Azure VMware 解决方案中确定一个网段（IP 网络）。 此网络不得与本地或 Azure 其余部分中的任何网段重叠，并且不得位于前面定义的 /22 网段内。

此网段主要用于在初始部署期间进行测试。

>[!NOTE]
>部署期间不需要此（这些）网络。 它们在部署后步骤中创建。
  
**示例：** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="标识 - 虚拟机工作负荷的 IP 地址段" border="false":::     

## <a name="define-the-virtual-network-gateway"></a>定义虚拟网络网关

>[!IMPORTANT]
>可连接到 Azure 虚拟 WAN 中的虚拟网络网关，但本快速入门不对此进行讨论。

Azure VMware 解决方案私有云需要一个 Azure 虚拟网络和一条 ExpressRoute 线路。  

定义是否要使用现有的 ExpressRoute 虚拟网络网关还是新建一个 。  如果决定使用新的虚拟网络网关，则将在创建私有云后创建它。 使用现有 ExpressRoute 虚拟网络网关是可接受的；出于规划目的，请记下要使用的 ExpressRoute 虚拟网络网关。 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="显示附加到 Azure VMware 解决方案的 Azure 虚拟网络的图表" border="false":::



## <a name="define-vmware-hcx-network-segments"></a>定义 VMware HCX 网段

VMware HCX 是与 Azure VMware 解决方案捆绑在一起的一项技术。 VMware HCX 的主要用例是工作负荷迁移和灾难恢复。 如果你计划执行上述任一操作，最好立即规划网络。 否则，可以跳到下一步。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="determine-whether-to-extend-your-networks"></a>确定是否要扩展网络

可选择性地将网段从本地扩展到 Azure VMware 解决方案。 

如果确实要扩展网络段，请现在按照以下准则定义这些网络：

- 网络必须连接到本地 VMware 环境中的 [vSphere 分布式交换机 (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)。  
- [vSphere 标准交换机](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上的网络无法扩展。

>[!IMPORTANT]
>这些网络在配置的最后一个步骤进行扩展，而不是在部署期间。


## <a name="next-steps"></a>后续步骤
收集并记录所需的信息后，请继续执行下一部分以创建 Azure VMware 解决方案私有云。

> [!div class="nextstepaction"]
> [部署 Azure VMware 解决方案](deploy-azure-vmware-solution.md)> 
