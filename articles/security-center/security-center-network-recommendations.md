---
title: 在 Azure 安全中心内保护你的网络资源
description: 本文档介绍 Azure 安全中心中的建议，以帮助你保护 Azure 网络资源并保持符合安全策略。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: dd09279665230062c7a2567784731144ef438c6c
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237237"
---
# <a name="protect-your-network-resources"></a>保护你的网络资源
Azure 安全中心不断分析 Azure 资源的安全状态，以实现网络安全最佳做法。 在安全中心识别出潜在的安全漏洞时，它会创建一些建议，指导完成配置所需控件以强化和保护资源的过程。

有关网络建议的完整列表，请参阅[网络建议](recommendations-reference.md#recs-networking)。

本文从网络安全角度介绍适用于 Azure 资源的建议。 网络建议以下一代防火墙、网络安全组、JIT VM 访问过度宽容的入站流量规则等为中心。 有关网络建议和修复操作的列表，请参阅[管理 Azure 安全中心的安全建议](security-center-recommendations.md)。

安全中心的网络功能包括： 

- 网络映射（需要 Azure Defender）
- [自适应网络强化](security-center-adaptive-network-hardening.md)（需要 Azure Defender）
- 网络安全建议
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>查看网络资源及其建议

在[资产清单页面](asset-inventory.md)中，使用资源类型筛选器选择要调查的网络资源：

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="资产清单网络资源类型。" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>网络映射

交互式网络映射提供了带有安全覆盖的图形视图，提供了强化网络资源的建议和见解。 你可以通过映射查看 Azure 工作负载的拓扑、虚拟机和子网之间的连接、从映射向下钻取特定资源的功能以及这些资源的建议。

打开网络映射：

1. 从安全中心的菜单打开 Azure Defender 仪表板，然后选择“网络映射”。

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="从 Azure Defender 仪表板打开网络映射。" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. 选择“层”菜单，然后选择“拓扑” 。
 
拓扑图的默认视图显示：

- 在 Azure 中选择的订阅。 该图支持多个订阅。
- 资源管理器资源类型的 VM、子网和 VNet（不支持经典 Azure 资源）
- 对等互连的 VNet
- 仅限具有高或中等严重性的[网络建议](security-center-recommendations.md)的资源  
- 面向 Internet 的资源
- 该映射针对在 Azure 中选择的订阅进行了优化。 如果修改了选择，则会根据新设置重新计算并重新优化映射。  

[![网络拓扑图。](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>了解网络映射

网络映射可以在“拓扑”视图和“流量”视图中显示 Azure 资源 。 

### <a name="the-topology-view"></a>拓扑视图

在网络映射的“拓扑”视图中，可以查看有关网络资源的以下见解：

- 在内圈中，可以看到所选订阅中的所有 Vnet，下一个圈是所有子网，外圈是所有虚拟机。
- 可以在连接映射中资源的行中了解到哪些资源相互关联，以及 Azure 网络的结构。 
- 使用严重性指示器可以快速了解安全中心建议打开哪些资源。
- 可以单击任何资源深入查看这些资源，并在网络映射的上下文中直接查看该资源及其建议的详细信息。  
- 如果映射上显示的资源太多，Azure 安全中心将使用其专有算法对资源进行智能群集，突出显示处于最关键状态和具有最高严重性的建议的资源。 

由于映射是交互式和动态的，因此每个节点都是可点击的，并且视图可以根据筛选器进行更改：

1. 可以使用顶部的筛选器修改可在网络映射上看到的内容。 可以根据以下内容聚焦映射：

   -  **安全运行状况**：可以根据 Azure 资源的严重性（高、中、低）筛选映射。
   - **建议**：可以根据这些资源上处于活动状态的建议来选择显示的资源。 例如，只能查看安全中心建议启用网络安全组的资源。
   - **网络区域**：默认情况下，映射仅显示面向 Internet 的资源，也可以选择内部 VM。
 
2. 可以随时单击左上角的“重置”以将映射恢复为默认状态。

向下钻取资源：

1. 在映射上选择特定资源时，右侧窗格将打开，并提供有关资源、连接的安全解决方案（如果有）以及与资源相关的建议的常规信息。 对于选择的每种资源，它都是相同类型的行为。 
2. 将鼠标悬停在映射中的节点上时，可以查看有关资源的常规信息，包括订阅、资源类型和资源组。
3. 使用该链接可放大工具提示并将映射重新聚焦在该特定节点上。 
4. 要将映射的焦点从特定节点移开，请缩小。

### <a name="the-traffic-view"></a>流量视图

“流量”视图提供了资源之间所有可能流量的映射。 这提供了配置的所有规则的可视化映射，这些规则定义了哪些资源可以与谁通信。 由此可查看网络安全组的现有配置，以及快速识别工作负载中可能存在的有风险的配置。

### <a name="uncover-unwanted-connections"></a>发现不需要的连接

此视图的优势在于能够显示这些允许的连接以及存在的漏洞，因此可以使用此截面的数据对资源执行必要的强化。 

例如，可能会检测到两台你不知道可以通信的计算机，从而能够更好地隔离工作负载和子网。

### <a name="investigate-resources"></a>调查资源

向下钻取资源：

1. 在映射上选择特定资源时，右侧窗格将打开，并提供有关资源、连接的安全解决方案（如果有）以及与资源相关的建议的常规信息。 对于选择的每种资源，它都是相同类型的行为。 
2. 单击“流量”以查看资源上可能的出站和入站流量列表 - 这是一个全面的列表，列出谁可以与资源进行通信、可以与谁通信以及通过哪些协议和端口进行通信。 例如，当你选择某个 VM 时，将显示它可以与之通信的所有 VM，而当你选择某个子网时，将显示其可以与之通信的所有子网。

此数据基于对网络安全组的分析以及分析多个规则以了解其交叉和交互的高级机器学习算法。 

[![网络流量映射。](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>后续步骤

若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

- [在 Azure 安全中心保护计算机和应用程序](./asset-inventory.md)