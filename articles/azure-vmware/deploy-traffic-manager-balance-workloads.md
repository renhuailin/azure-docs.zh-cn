---
title: 部署流量管理器以平衡 Azure VMware 解决方案工作负载
description: 了解如何将流量管理器与 Azure VMware 解决方案集成，以平衡不同区域中多个终结点之间的应用程序工作负载。
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: ac8f402eadcced4e8592a68f13abd6d2693b8b0c
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601973"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>部署流量管理器以平衡 Azure VMware 解决方案工作负载

本文逐步讲解如何将 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) 与 Azure VMware 解决方案集成。 该集成可平衡多个终结点之间的应用程序工作负载。 本文还将介绍如何配置流量管理器来管理分布在多个 Azure VMware 解决方案区域的 [Azure 应用程序网关之](../application-gateway/overview.md) 间的流量。 

该网关将 Azure VMware 解决方案虚拟机 (VM) 配置为后端池成员，对传入的第 7 层请求实施负载平衡。 如需了解详细信息，请参阅[使用 Azure 应用程序网关保护 Azure VMware 解决方案上的 Web 应用](protect-azure-vmware-solution-with-application-gateway.md)

此图显示了流量管理器如何为不同区域终结点之间的 DNS 级别应用程序进行负载平衡。 网关将后端池成员配置为 IIS 服务器，并引用为 Azure VMware 解决方案外部终结点。 两个私有云区域之间通过虚拟网络建立的连接将使用 ExpressRoute 网关。   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="流量管理器与 Azure VMware 解决方案集成的关系图。" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

在开始之前，请先查看[先决条件](#prerequisites)，然后再完成以下步骤：

> [!div class="checklist"]
> * 验证应用程序网关和 NSX-T 段的配置
> * 创建流量管理器配置文件
> * 将外部终结点添加到流量管理器配置文件

## <a name="prerequisites"></a>先决条件

- 三个 VM 配置为 Microsoft IIS 服务器，在不同的 Azure VMware 解决方案区域中运行： 
   - 美国西部
   - 西欧
   - 美国东部（本地） 

- 上述 Azure VMware 解决方案区域中具有外部终结点的应用程序网关。

- 连接了网络的主机用于验证。 

- [在 Azure VMware 解决方案中创建的 NSX-T 网段](tutorial-nsx-t-network-segment.md)。

## <a name="verify-your-application-gateways-configuration"></a>验证应用程序网关配置

以下步骤用于验证应用程序网关的配置。

1. 在 Azure 门户中，选择“应用程序网关”，查看当前应用程序网关的列表：

   - AVS-GW-WUS
   - AVS-GW-EUS（本地）
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="“应用程序网关”页面屏幕截图显示了已配置的应用程序网关列表。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. 选择其中一个之前部署的应用程序网关。 

   此时会打开一个窗口，显示应用程序网关上的各种信息。 

   :::image type="content" source="media/traffic-manager/backend-pool-configuration.png" alt-text="“应用程序网关”页面屏幕截图显示了所选应用程序网关详细信息。" lightbox="media/traffic-manager/backend-pool-configuration.png":::

1. 选择“后端池”，验证其中一个后端池的配置。 用户可以看到该 VM 后端池成员配置为 Web 服务器，IP 地址为 172.29.1.10。
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="突出显示目标 IP 地址的“编辑后端池”页面屏幕截图。":::

1. 验证其他应用程序网关和后端池成员的配置。 

## <a name="verify-the-nsx-t-segment-configuration"></a>验证 NSX-T 网段配置

以下步骤用于验证 Azure VMware 解决方案环境中的 NSX-T 网段配置。

1. 选择“网段”查看已配置的网段。  用户将看到连接到 Contoso-T01 网关的 Contoso-segment1，它是第 1 层灵活路由器。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-azure-vmware-solution.png" alt-text="显示 NSX-T Manager 中网段配置文件的屏幕截图。" lightbox="media/traffic-manager/nsx-t-segment-azure-vmware-solution.png":::    

1. 选择“第 1 层网关”，查看包含已连接网段数量的第 1 层网关列表。 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="显示所选网段的网关地址的屏幕截图。":::    

1. 选择链接到 Contoso-T01 的网段。 此时会打开一个窗口，显示在第 01 层路由器上配置的逻辑接口。 它充当连接到该网段的后端池成员 VM 的网关。

1. 在 vSphere 客户端中，选择 VM 查看其详细信息。 

   >[!NOTE]
   >其 IP 地址与上一节中配置为 web 服务器的 VM 后端池成员相匹配：172.29.1.10。

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="显示 vSphere 客户端中 VM 详细信息的屏幕截图。" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. 选择 VM，然后选择“操作 > 编辑设置”，验证是否与 NSX-T 网段连接。

## <a name="create-your-traffic-manager-profile"></a>创建流量管理器配置文件

1. 登录 [Azure 门户](https://rc.portal.azure.com/#home)。 在“Azure 服务 > 网络”中，选择“流量管理器配置文件”。

2. 选择“新建”创建新的流量管理器配置文件。
 
3. 提供以下信息，然后选择“创建”：

   - 配置文件名称
   - 路由方法（使用 [加权](../traffic-manager/traffic-manager-routing-methods.md)）
   - 订阅
   - 资源组

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>将外部终结点添加到流量管理器配置文件

1. 从搜索结果窗格中选择“流量管理器配置文件”，依次选择“终结点”和“+添加”。

1. 对于不同区域中的每个外部终结点，请输入所需的详细信息，然后选择“添加”： 
   - 类型
   - 名称
   - 完全限定的域名 (FQDN) 或 IP
   - 权重（将权重 1 分配给每个终结点）。 

   创建后，所有三个终结点会在流量管理器配置文件中显示。 所有三个终结点的监视器状态都必须是“联机”。

3. 选择“概述”，然后在“DNS 名称”中复制 URL。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="显示流量管理器终结点概述的屏幕截图，其中突出显示了 DNS 名称。" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. 在浏览器中粘贴 DNS 名称 URL。 屏幕截图显示定向到西欧区域的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="显示路由到西欧的流量的浏览器窗口屏幕截图。"::: 

5. 刷新浏览器。 屏幕截图显示定向到美国西部区域中的另一组后端池成员的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="显示路由到美国西部的流量的浏览器窗口屏幕截图。"::: 

6. 刷新浏览器。 屏幕截图显示定向到最后一组本地后端池成员的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="显示路由到本地的流量的浏览器窗口屏幕截图。":::

## <a name="next-steps"></a>后续步骤

现在，你已了解了如何将 Azure 流量管理器与 Azure VMware 解决方案集成，你可能还想了解：

- [使用 Azure VMware 解决方案上的 Azure 应用程序网关](protect-azure-vmware-solution-with-application-gateway.md)
- [流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)
- [在 Azure 中结合使用负载均衡服务](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [测量流量管理器性能](../traffic-manager/traffic-manager-performance-considerations.md)
