---
title: 规划 Azure VMware 解决方案部署
description: 本文概述了 Azure VMware 解决方案部署工作流。  最终结果是一个就绪的可用于创建和迁移虚拟机 (VM) 的环境。
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107238"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>规划 Azure VMware 解决方案部署

本文提供用于识别和收集要在部署期间使用的信息的规划过程。 规划部署时，请务必记录收集的信息，方便在部署过程中参考。

执行本快速入门中所述的步骤可以获得一个用于创建虚拟机 (VM) 和实现迁移的生产就绪式环境。 

要跟踪收集的数据，请获取 [HCX 计划清单](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/)。

> [!IMPORTANT]
> 在准备创建 Azure VMware 解决方案资源时，务必提前请求主机配额。 你可以现在请求主机配额，这样，在计划过程完成后，你便可立即部署 Azure VMware 解决方案私有云。 支持团队收到主机配额请求后，最多需要五个工作日来确认请求并分配主机。 如果你已有 Azure VMware 解决方案私有云，但想要分配更多主机，请完成相同的过程。 有关详细信息，请参阅以下链接（具体选择哪个链接取决于你拥有的订阅类型）：
> - [EA 客户](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [CSP 客户](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>订阅

标识你计划用来部署 Azure VMware 解决方案的订阅。  你可以创建新订阅，也可以重用现有的订阅。

>[!NOTE]
>订阅必须与 Microsoft 企业协议或云解决方案提供商 Azure 计划相关联。 有关详细信息，请参阅[如何启用 Azure VMware 解决方案资源](enable-azure-vmware-solution.md)。

## <a name="resource-group"></a>资源组

标识要用于 Azure VMware 解决方案的资源组。  通常会专门为 Azure VMware 解决方案创建一个资源组，但你可以使用现有的资源组。

## <a name="region"></a>区域

标识要部署 Azure VMware 解决方案的区域。  有关详细信息，请参阅[“可用 Azure 产品（按区域）”指南](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)。

## <a name="resource-name"></a>资源名称

定义要在部署过程中使用的资源名称。  资源名称是一个易记的描述性名称，用来为 Azure VMware 解决方案私有云命名。

>[!IMPORTANT]
>名称不得超过 40 个字符。 如果名称超出此限制，你将无法创建用于私有云的公共 IP 地址。 

## <a name="size-hosts"></a>调整主机大小

确定在部署 Azure VMware 解决方案时要使用的主机大小。  有关完整列表，请参阅 [Azure VMware 解决方案私有云和群集](concepts-private-clouds-clusters.md#hosts)文档。

## <a name="number-of-clusters-and-hosts"></a>群集和主机的数目

你执行的第一个 Azure VMware 解决方案部署将包含具有一个群集的私有云。 对于部署，你需要定义要部署到第一个群集的主机数。

>[!NOTE]
>每个群集的最小主机数目为 3 个，最大数目为 16 个。 每个私有云的最大群集数目为 4 个。 

有关详细信息，请参阅 [Azure VMware 解决方案私有云和群集](concepts-private-clouds-clusters.md#clusters)文档。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。

## <a name="ip-address-segment-for-private-cloud-management"></a>用于私有云管理的 IP 地址段

规划部署的第一步是规划 IP 分段。 Azure VMware 解决方案需要 /22 CIDR 网络。 此地址空间将分割成多个更小的网段（子网）并用于 Azure VMware 解决方案管理段，包括 vCenter、VMware HCX、NSX-T 和 vMotion 功能。 下面的可视化效果突出显示了此网段的使用位置。

此 /22 CIDR 网络地址块不应与本地或 Azure 中的任何现有网段重叠。

**示例：** 10.0.0.0/22

若要详细了解针对每种私有云的 /22 CIDR 网络的拆分方式，请参阅[网络规划清单](tutorial-network-checklist.md#routing-and-subnet-considerations)。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="标识 - IP 地址段" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>虚拟机工作负荷的 IP 地址段

与任何 VMware 环境一样，虚拟机必须连接到网段。 在 Azure VMware 解决方案中，有两种类型的段，L2 扩展段（稍后介绍）和 NSX-T 网段。 随着 Azure VMware 解决方案的生产部署的扩展，通常会组合使用本地 L2 扩展段和本地 NSX-T 网段。 若要规划初始部署，请在 Azure VMware 解决方案中确定一个网段（IP 网络）。 此网络不得与本地或 Azure 其余部分中的任何网段重叠，并且不得位于前面定义的 /22 网段内。

此网段主要用于在初始部署期间进行测试。

>[!NOTE]
>部署期间不需要此（这些）网络。 它们在部署后步骤中创建。
  
**示例：** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="标识 - 虚拟机工作负荷的 IP 地址段" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>将 Azure 虚拟网络连接到 Azure VMware 解决方案

为了提供与 Azure VMware 解决方案的连接，需要构建从 Azure VMware 解决方案私有云到 ExpressRoute 虚拟网络网关的 ExpressRoute。

可使用现有的 ExpressRoute 虚拟网络网关，也可新建一个 。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="标识 - 用于附加 Azure VMware 解决方案的 Azure 虚拟网络" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>使用现有的 ExpressRoute 虚拟网络网关

如果计划使用现有的 ExpressRoute 虚拟网络网关，则会在部署后步骤中建立 Azure VMware 解决方案 ExpressRoute 线路。 在这种情况下，请将“虚拟网络”字段留空。

一般建议使用现有 ExpressRoute 虚拟网络网关。 出于计划目的，请记下你将使用的 ExpressRoute 虚拟网络网关，并继续到[下一步](#vmware-hcx-network-segments)。

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>创建新的 ExpressRoute 虚拟网络网关

创建新的 ExpressRoute 虚拟网络网关时，可使用现有的 Azure 虚拟网络，也可创建新网络。  

- 对于现有的 Azure 虚拟网络：
   1. 识别预先不存在 ExpressRoute 虚拟网络网关的 Azure 虚拟网络。
   2. 在部署前，请在 Azure 虚拟网络中创建一个 [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。

- 选择“虚拟网络”列表下的“新建”链接，在部署期间创建新的 Azure 虚拟网络和虚拟网络网关 。  必须在部署之前定义地址空间和子网，以便在完成部署步骤时输入这些信息。

下图显示了“创建私有云”部署屏幕，其中突出显示了“虚拟网络”字段 。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Azure VMware 解决方案部署屏幕的屏幕截图，其中突出显示了“虚拟网络”字段。":::

> [!NOTE]
> 你的本地环境和 Azure VMware 解决方案可以看到任何要使用或创建的虚拟网络，因此请确保在此虚拟网络和子网中使用的任何 IP 段都不重叠。

## <a name="vmware-hcx-network-segments"></a>VMware HCX 网段

VMware HCX 是与 Azure VMware 解决方案捆绑在一起的一项技术。 VMware HCX 的主要用例是工作负荷迁移和灾难恢复。 如果你计划执行上述任一操作，最好立即规划网络。 否则，可以跳到下一步。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>（可选）扩展网络

你可以将网络段从本地扩展到 Azure VMware 解决方案。 如果扩展网络段，请现在标识这些网络。  

有两个因素需要考虑：

- 如果你计划从本地扩展网络，则这些网络必须连接到本地 VMware 环境中的 [vSphere 分布式交换机 (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)。  
- [vSphere 标准交换机](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上的网络无法扩展。

>[!NOTE]
>这些网络在配置的最后一个步骤进行扩展，而不是在部署期间。
>
## <a name="next-steps"></a>后续步骤
收集并记录所需的信息后，请继续执行下一部分以创建 Azure VMware 解决方案私有云。

> [!div class="nextstepaction"]
> [部署 Azure VMware 解决方案](deploy-azure-vmware-solution.md)
> 
