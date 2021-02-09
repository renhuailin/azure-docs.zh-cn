---
title: 部署流量管理器以平衡 Azure VMware 解决方案工作负荷
description: 了解如何将流量管理器与 Azure VMware 解决方案集成，以在不同区域中跨多个终结点平衡应用程序工作负载。
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988596"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>部署流量管理器以平衡 Azure VMware 解决方案工作负荷

本文逐步讲解如何将 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) 与 Azure VMware 解决方案集成。 集成跨多个终结点平衡应用程序工作负荷。 本文还介绍了如何配置流量管理器以将三个 [Azure 应用程序网关](../application-gateway/overview.md) 之间的流量定向到跨多个 Azure VMware 解决方案区域的步骤。 

网关将 Azure VMware 解决方案虚拟机 (Vm) 配置为后端池成员，以对传入第7层请求进行负载均衡。 有关详细信息，请参阅 [使用 Azure 应用程序网关保护 Azure VMware 解决方案上的 web 应用](protect-azure-vmware-solution-with-application-gateway.md)

此关系图显示了流量管理器如何在区域终结点之间的 DNS 级别为应用程序提供负载平衡。 网关将后端池成员配置为 IIS 服务器，并将其作为 Azure VMware 解决方案外部终结点引用。 在两个私有云区域之间通过虚拟网络建立的连接使用 ExpressRoute 网关。   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="流量管理器与 Azure VMware 解决方案集成的体系结构关系图" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

在开始之前，请先查看 [先决条件](#prerequisites) ，然后将指导完成以下过程：

> [!div class="checklist"]
> * 验证应用程序网关和 NSX-T 段的配置
> * 创建流量管理器配置文件
> * 将外部终结点添加到流量管理器配置文件

## <a name="prerequisites"></a>先决条件

- 配置为在不同 Azure VMware 解决方案区域中运行的 Microsoft IIS 服务器的三个 Vm： 
   - 美国西部
   - 西欧
   - 美国东部 (本地)  

- 在上面提到的 Azure VMware 解决方案区域中具有外部终结点的应用程序网关。

- 具有 internet 连接的主机用于验证。 

- [在 Azure VMware 解决方案中创建的 NSX T 网络分段](tutorial-nsx-t-network-segment.md)。

## <a name="verify-your-application-gateways-configuration"></a>验证你的应用程序网关配置

以下步骤验证你的应用程序网关的配置。

1. 在 Azure 门户中，选择 " **应用程序网关** "，查看当前应用程序网关的列表：

   - AVS-GW-WUS
   - AVS-EUS (本地) 
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="显示已配置应用程序网关列表的 &quot;应用程序网关&quot; 页的屏幕截图。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. 选择以前部署的应用程序网关之一。 

   此时会打开一个窗口，其中显示了应用程序网关上的各种信息。 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="显示所选应用程序网关详细信息的 &quot;应用程序网关&quot; 页的屏幕截图。" lightbox="media/traffic-manager/backend-pool-config.png":::

1. 选择 " **后端池** "，验证某个后端池的配置。 你会看到一个 VM 后端池成员配置为 IP 地址为172.29.1.10 的 web 服务器。
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="突出显示目标 IP 地址的 &quot;编辑后端池&quot; 页的屏幕截图。":::

1. 验证其他应用程序网关和后端池成员的配置。 

## <a name="verify-the-nsx-t-segment-configuration"></a>验证 NSX 段配置

以下步骤验证 Azure VMware 解决方案环境中的 NSX 部分的配置。

1. 选择 " **段** " 可查看已配置的段。  你会看到 segment1 连接到 Contoso-T01 gateway，后者是第1层灵活路由器。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="显示 NSX-T Manager 中段配置文件的屏幕截图。" lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. 选择 **第1层网关** ，查看具有链接的段数的第1层网关的列表。 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="显示选定段的网关地址的屏幕截图。":::    

1. 选择链接到 T01 的段。 此时会打开一个窗口，其中显示了在第01层路由器上配置的逻辑接口。 它充当连接到段的后端池成员 VM 的网关。

1. 在 vSphere 客户端中，选择 VM 以查看其详细信息。 

   >[!NOTE]
   >其 IP 地址与上一节中配置为 web 服务器的 VM 后端池成员匹配：172.29.1.10。

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="显示 vSphere 客户端中的 VM 详细信息的屏幕截图。" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. 选择 VM，然后选择 " **操作" > "编辑设置** "，验证是否与 "NSX" 段连接。

## <a name="create-your-traffic-manager-profile"></a>创建流量管理器配置文件

1. 登录到 [Azure 门户](https://rc.portal.azure.com/#home)。 在 " **Azure 服务 > 网络**" 下，选择 " **流量管理器配置文件**"。

2. 选择 " **+ 添加** " 创建新的流量管理器配置文件。
 
3. 提供以下信息，然后选择 " **创建**"：

   - 配置文件名称
   - 使用[加权](../traffic-manager/traffic-manager-routing-methods.md) (路由方法
   - 订阅
   - 资源组

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>将外部终结点添加到流量管理器配置文件

1. 从搜索结果窗格中选择流量管理器配置文件，依次选择 " **终结点**" 和 " **+ 添加**"。

1. 对于不同区域中的每个外部终结点，请输入所需的详细信息，然后选择 " **添加**"： 
   - 类型
   - 名称
   - 完全限定的域名 (FQDN) 或 IP
   - 权重 (将权重分配给每个终结点) 。 

   创建后，会显示流量管理器配置文件中的所有三个。 所有三个监视器的状态都必须是 **联机** 的。

3. 选择 " **概述** "，然后在 " **DNS 名称**" 下复制 URL。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="显示流量管理器终结点概述的屏幕截图，其中突出显示了 DNS 名称。" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. 在浏览器中粘贴 DNS 名称 URL。 屏幕截图显示定向到西欧区域的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="显示路由到西欧的流量的浏览器窗口的屏幕截图。"::: 

5. 刷新浏览器。 屏幕截图显示定向到美国西部区域中的另一组后端池成员的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="显示路由到美国西部的流量的浏览器窗口的屏幕截图。"::: 

6. 再次刷新浏览器。 屏幕截图显示定向到本地后端池成员的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="显示路由到本地的流量的浏览器窗口的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

现在，你已介绍了如何将 Azure 流量管理器与 Azure VMware 解决方案集成，你可能想要了解：

- [使用 Azure VMware 解决方案上的 Azure 应用程序网关](protect-azure-vmware-solution-with-application-gateway.md)。
- [流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)。
- [结合 Azure 中的负载均衡服务](../traffic-manager/traffic-manager-load-balancing-azure.md)。
- [测量流量管理器性能](../traffic-manager/traffic-manager-performance-considerations.md)。
