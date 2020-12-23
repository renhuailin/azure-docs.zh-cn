---
title: 部署流量管理器以平衡 Azure VMware 解决方案 (AVS) 工作负荷
description: 了解如何将流量管理器与 Azure VMware 解决方案 (AVS) 集成，以在不同区域中跨多个终结点平衡应用程序工作负载。
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: ed74bb0dfc533abadd50af32afc06c9cb4106193
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874268"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>部署流量管理器以平衡 Azure VMware 解决方案 (AVS) 工作负荷

本文逐步讲解如何将流量管理器与 Azure VMware 解决方案集成 (AVS) ，以跨多个终结点平衡应用程序工作负荷。 我们将探讨这样一种情况：流量管理器会将三个应用程序网关之间的流量定向到多个 AVS 区域（美国西部、西欧和本地）。 

Azure 流量管理器是一种基于 DNS 的流量负载均衡器，可让你以最佳方式将流量分配到全球 Azure 区域内的服务。 它将在 Azure 运行的工作负荷和外部公共终结点之间对应用程序流量进行负载均衡。 有关流量管理器的详细信息，请参阅 [什么是流量管理器？](../traffic-manager/traffic-manager-overview.md)

首先查看 [先决条件](#prerequisites) ;接下来，我们将指导完成以下过程：

> [!div class="checklist"]
> * 验证应用程序网关的配置
> * 验证 NSX 段的配置
> * 创建流量管理器配置文件
> * 将外部终结点添加到流量管理器配置文件

## <a name="topology"></a>拓扑

如下图所示，Azure 流量管理器在区域终结点之间的 DNS 级别为应用程序提供负载均衡。 应用程序网关将后端池成员配置为 IIS 服务器，并将其作为 AVS 外部终结点进行引用。

通过虚拟网络连接到两个 AVS 私有云区域（美国西部和西欧）和美国东部的本地服务器之间的连接，使用 ExpressRoute 网关。   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="流量管理器与 Azure VMware 解决方案集成的体系结构示意图" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::
 
## <a name="prerequisites"></a>先决条件

- 三个虚拟机配置为在不同的 AVS 区域中运行的 Microsoft IIS 服务器：美国西部、西欧以及本地。 

- 在美国西部、西欧和本地使用外部终结点的应用程序网关。

- 具有 internet 连接的主机用于验证。 

## <a name="verify-configuration-of-your-application-gateways"></a>验证应用程序网关的配置

[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/) 是第7层 web 流量负载均衡器，可用于管理 web 应用程序的流量。 有关应用程序网关的详细信息，请参阅 [什么是 Azure 应用程序的网关？](../application-gateway/overview.md) 

在此方案中，将三个应用程序网关实例配置为外部 AVS 终结点。 应用程序网关具有配置为后端池成员的 AVS 虚拟机，用于对传入第7层请求进行负载均衡。  (若要了解如何使用 AVS 虚拟机作为后端池配置应用程序网关，请参阅 [使用 Azure 应用程序的网关保护 Azure VMware 解决方案上的 web 应用](protect-azure-vmware-solution-with-application-gateway.md)。 )   

以下步骤验证是否正确配置了应用程序网关。

1. 打开 Azure 门户，然后选择 " **应用程序网关** "，查看当前应用程序网关的列表。 

    对于此方案，我们配置了三个应用程序网关：
    - AVS-GW-WUS
    - AVS-EUS (本地) 
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="显示已配置应用程序网关列表的 &quot;应用程序网关&quot; 页的屏幕截图。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. 选择以前部署的应用程序网关之一。 此时会打开一个窗口，其中显示了应用程序网关上的各种信息。 选择 " **后端池** "，验证某个后端池的配置。

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="显示所选应用程序网关详细信息的 &quot;应用程序网关&quot; 页的屏幕截图。" lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. 在这种情况下，我们会看到一个虚拟机后端池成员配置为具有 172.29.1.10 IP 地址的 web 服务器。
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="突出显示目标 IP 地址的 &quot;编辑后端池&quot; 页的屏幕截图。":::

    同样，可以验证其他应用程序网关和后端池成员的配置。 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>验证 NSX 段的配置

在 NSX-T Manager 中创建的网络段用作 vCenter 中虚拟机的网络。 有关详细信息，请参阅教程： [在 Azure VMware 解决方案中创建 NSX-T 网段 (AVS) ](tutorial-nsx-t-network-segment.md)。

在我们的方案中，在附加了后端池成员虚拟机的 AVS 环境中配置了 NSX-T 段。

1. 选择 " **段** " 可查看已配置的段。 在这种情况下，我们会看到 Contoso-segment1 连接到 T01 网关，后者是第1层灵活路由器。

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="显示 NSX-T Manager 中段配置文件的屏幕截图。" lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

2. 选择 **第1层网关** ，查看具有链接段数的第1层网关的列表。 选择链接到 T01 的段。 此时会打开一个窗口，其中显示了在第01层路由器上配置的逻辑接口。 这将充当连接到段的后端池成员虚拟机的网关。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="显示选定段的网关地址的屏幕截图。":::    

3. 在 VM vSphere 客户端中，选择要查看其详细信息的虚拟机。 请注意，它的 IP 地址与我们在上一节的步骤3中看到的内容相匹配：172.29.1.10。

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="显示 VSphere 客户端中的 VM 详细信息的屏幕截图。" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. 选择虚拟机，然后单击 " **操作" > "编辑设置** "，验证是否与 "NSX" 段连接。

## <a name="create-your-traffic-manager-profile"></a>创建流量管理器配置文件

1. 登录到 [Azure 门户](https://rc.portal.azure.com/#home)。 在 " **Azure 服务 > 网络**" 下，选择 " **流量管理器配置文件**"。

2. 选择 " **+ 添加** " 创建新的流量管理器配置文件。
 
3. 提供配置文件名称、路由方法 (在此方案中将使用加权;请参阅 [流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)) 、订阅和资源组，并选择 " **创建**"。

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>将外部终结点添加到流量管理器配置文件

1. 从搜索结果窗格中选择流量管理器配置文件，依次选择 " **终结点** " 和 " **+ 添加**"。

2. 输入所需的详细信息：类型、名称、完全限定的域名 (FQDN) 或 IP，权重 (在此方案中，我们将为每个终结点) 指定权重1。 选择 **添加** 。 这会创建外部终结点。 监视器状态必须为 " **联机**"。 重复相同的步骤，创建两个以上的外部终结点，一个在不同的区域，另一个在本地。 创建后，这三个将显示在流量管理器配置文件中，所有三个的状态都应该是 **联机** 的。

3. 选择“概述”。 复制 " **DNS 名称**" 下的 URL。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="显示流量管理器终结点概述的屏幕截图，其中突出显示了 DNS 名称。" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. 在浏览器中粘贴 DNS 名称 URL。 以下屏幕截图显示定向到西欧区域的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="显示路由到西欧的流量的浏览器窗口的屏幕截图。"::: 

5. 刷新浏览器。 以下屏幕截图显示流量现在定向到美国西部区域中的另一组后端池成员。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="显示路由到美国西部的流量的浏览器窗口的屏幕截图。"::: 

6. 再次刷新浏览器。 以下屏幕截图显示流量现在定向到本地的后端池成员组。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="显示路由到本地的流量的浏览器窗口的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [使用 Azure VMware 解决方案上的 Azure 应用程序网关 (AVS) ](protect-azure-vmware-solution-with-application-gateway.md)
- [流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)
- [在 Azure 中组合负载平衡服务](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [测量流量管理器性能](../traffic-manager/traffic-manager-performance-considerations.md)
