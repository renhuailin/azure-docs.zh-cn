---
title: Azure Active Directory 应用程序代理的网络拓扑注意事项
description: 介绍使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: a9b12750ec16ee5fe79c91e4ed931426f8dbaa36
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445084"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理优化流量流

本文介绍如何优化流量流，以及使用 Azure Active Directory (Azure AD) 应用程序代理远程发布和访问应用程序时的网络拓扑注意事项。

## <a name="traffic-flow"></a>流量流

通过 Azure AD 应用程序代理发布应用程序时，从用户发往应用程序的流量将通过三个连接流动：

1. 用户连接到 Azure 上的 Azure AD 应用程序代理服务公共终结点
1. 应用程序代理连接器连接到应用程序代理服务（出站）
1. 应用程序代理连接器连接到目标应用程序

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="显示从用户到目标应用程序的流量流的示意图。" lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>优化连接器组以使用最靠近的应用程序代理云服务（预览）

注册 Azure AD 租户时，租户所在的区域根据你指定的国家/地区来确定。 启用应用程序代理时，将在你的 Azure AD 租户所在的同一区域或者与它最靠近的区域中，选择你的租户的 **默认** 应用程序代理云服务实例。

例如，如果你的 Azure AD 租户所在的区域为英国，则位于 **默认** 位置的所有应用程序代理连接器都将分配为使用位于欧洲数据中心的服务实例。 当用户访问发布的应用程序时，其流量会流经此位置中的应用程序代理云服务实例。

如果你在非默认区域中安装了连接器，可能有利的做法是更改要对其优化连接器组的区域，以改善访问这些应用程序时的性能。 为连接器组指定区域后，该连接器组将连接到指定区域中的应用程序代理云服务。

若要优化流量流并降低连接到连接器组时的延迟，请将连接器组分配到最靠近的区域。 若要分配区域，请执行以下操作：

> [!IMPORTANT]
> 连接器必须至少使用版本 1.5.1975.0 才能使用此功能。

1. 以使用应用程序代理的目录的应用程序管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 例如，如果租户域为 contoso.com，则管理员应为 admin@contoso.com 或该域上的其他任何域别名。
1. 请在右上角选择用户名。 验证是否已登录到使用应用程序代理的目录。 如果需要更改目录，请选择“切换目录”，然后选择使用应用程序代理的目录  。
1. 在左侧导航面板中选择“Azure Active Directory”  。
1. 在“管理”下选择“应用程序代理”   。
1. 选择“新建连接器组”，并为连接器组提供一个 **名称**。
1. 接下来，在“高级设置”下，选择“针对特定区域进行优化”下的下拉菜单，并选择最靠近连接器的区域。
1. 选择“创建”。
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="配置新的连接器组。" lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. 创建新的连接器组后，可以选择要分配到此连接器组的连接器。 
   - 仅当连接器位于使用默认区域的连接器组中时，才能将连接器移到该连接器组。 最佳方法是始终先将连接器放入“默认组”，然后再将其移到相应的连接器组。
   - 仅当 **未** 将任何连接器或应用分配到连接器组时，才能更改该连接器组的区域。
1. 接下来，将连接器组分配到应用程序。 访问应用时，流量现在应转到已针对其优化了连接器组的区域中的应用程序代理云服务。

## <a name="considerations-for-reducing-latency"></a>有关降低延迟的注意事项

所有代理解决方案都会给网络连接造成延迟。 无论选择哪种代理或 VPN 解决方案作为远程访问解决方案，它始终会包含一组服务器来实现与企业网络内部的连接。

组织通常已在其外围网络中添置了服务器终结点。 但是，使用 Azure AD 应用程序代理时，流量将流经云中的代理服务，而连接器驻留在企业网络中。 不需要外围网络。

以下各部分包含可帮助进一步降低延迟的其他建议。 

### <a name="connector-placement"></a>连接器的位置

应用程序代理根据租户的位置选择实例的位置。 但是，需确定要将连接器安装在哪个位置，以便能够定义网络流量的延迟特征。

设置应用程序代理服务时，请提出以下问题：

- 应用位于何处？
- 访问应用的大部分用户位于何处？
- 应用程序代理实例位于何处？
- 是否已经与 Azure 数据中心建立专用网络连接，例如，使用 Azure ExpressRoute 或类似的 VPN？

连接器必须与 Azure 和应用程序通信（流量流程图中的步骤 2 和步骤 3），因此，连接器的位置会影响这两个连接的延迟时间。 评估连接器的位置时，请考虑以下几点：

- 如果要使用 Kerberos 约束委派 (KCD) 进行单一登录，连接器需要与数据中心保持直通。 此外，连接器服务器需加入域。  
- 如果不很确定，请将连接器安装在比较靠近应用程序的位置。

### <a name="general-approach-to-minimize-latency"></a>最小化延迟的常规方法

可通过优化每个网络连接来尽量降低端到端流量的延迟。 可通过以下方式优化每个连接：

- 减少跃点两个端点之间的距离。
- 选择要遍历的适当网络。 例如，遍历专用网络而不遍历公共 Internet 可能速度更快，因为前者是专用链路。

如果在 Azure 与企业网络之间建立了专用 VPN 或 ExpressRoute 链路，可以利用它。

## <a name="focus-your-optimization-strategy"></a>注重优化策略

很难控制用户与应用程序代理之间的连接。 用户可能会通过家庭网络、咖啡店或在其他国家/地区访问你的应用。 不过，可以优化从应用程序代理服务到应用程序代理连接器再到应用的连接。 考虑在环境中整合以下模式。

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>模式 1：将连接器放在靠近应用程序的位置

将连接器放在客户网络中靠近目标应用程序的位置。 由于连接器靠近应用程序，所以此配置可最小化拓扑图中的步骤 3。

如果连接器需连接到域控制器，此模式十分有利。 这种方法适用于大多数方案，因此大多数客户都使用此模式。 也可以将此模式与模式 2 结合使用，优化服务与连接器之间的流量。

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>模式 2：结合利用 ExpressRoute 和 Microsoft 对等互连

如果你为 ExpressRoute 设置了 Microsoft 对等互连，则可为应用程序代理与连接器之间的流量使用更快的 ExpressRoute 连接。 连接器仍在网络中靠近应用的位置。

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>模式 3：利用结合专用对等互连的 ExpressRoute

如果已在 Azure 与企业网络之间结合专用对等互连设置了专用 VPN 或 ExpressRoute，则还可以使用另一个选项。 在此配置中，Azure 中的虚拟网络通常被视为企业网络的扩展。 因此，可将连接器安装在 Azure 数据中心，同时仍可满足连接器到应用的连接的低延迟要求。

因为流量通过专用连接流动，因此延迟不会加重。 由于连接器安装在 Azure 数据中心内靠近 Azure AD 租户的位置，因此可以改善应用程序代理服务与连接器之间的网络延迟。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="显示 Azure 数据中心内安装的连接器的示意图" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

### <a name="other-approaches"></a>其他方法

尽管本文的重点是连接器的位置，但是，也可以通过改变应用程序的位置来获得更好的延迟特征。

越来越多的组织正在将其网络移入托管环境。 这样，便可以将应用放在同样属于企业网络一部分的托管环境中，而这些应用仍会保留在域中。 在这种情况下，可向新的应用程序位置应用前面部分中所述的模式。 考虑此选项时，请参阅 [Azure AD 域服务](../../active-directory-domain-services/overview.md)。

此外，考虑使用[连接器组](application-proxy-connector-groups.md)针对不同位置和网络中的应用进行连接器排列组织。

## <a name="common-use-cases"></a>常见用例

本部分将演练几个常见方案。 假设 Azure AD 租户（因此也包括代理服务终结点）位于美国 (US)。 在全球的其他区域，这些用例中所述的注意事项也适用。

对于这些方案，为了方便讨论，我们将每个连接称为“跃点”并为其编号：

- 跃点 1：从用户到应用程序代理服务
- 跃点 2：从应用程序代理服务到应用程序代理连接器
- 跃点 3：从应用程序代理连接器到目标应用程序 

### <a name="use-case-1"></a>用例 1

**方案：** 该应用位于组织在美国部署的网络中，其用户位于同一区域。 Azure 数据中心与企业网络之间不存在 ExpressRoute 或 VPN。

**建议：** 遵循前一部分中所述的模式 1。 为了降低延迟，可以根据需要考虑使用 ExpressRoute。

这是一个简单的模式。 可以通过将连接器放置在应用附近来优化跃点 3。 这也是一种自然而然的选择，因为安装的连接器通常与应用和数据中心建立直通连接，以便执行 KCD 操作。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="显示用户、代理、连接器和应用都位于美国的示意图。" lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>用例 2

**方案：** 该应用位于组织在美国部署的网络中，其用户遍布全球各地。 Azure 数据中心与企业网络之间不存在 ExpressRoute 或 VPN。

**建议：** 遵循前一部分中所述的模式 1。

同样，常见的模式是优化跃点 3，其中的连接器放置在应用附近。 如果将整个跃点 3 放在同一区域，则它的系统开销通常不大。 但是，根据用户所在的位置，跃点 1 的系统开销可能更大，因为世界各地的用户必须访问位于美国的应用程序代理实例。 值得注意的是，由于用户遍布全球各地，任何代理解决方案将具有类似的特征。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="用户分散在全球各地，但其他所有对象都在美国" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>用例 3

**方案：** 该应用位于组织在美国部署的网络中。 Azure 与企业网络之间存在采用 Microsoft 对等互连的 ExpressRoute。

建议：遵循前一部分中所述的模式 1 和模式 2。

首先，使连接器尽量靠近应用。 然后，系统自动为跃点 2 使用 ExpressRoute。

如果 ExpressRoute 链路使用 Microsoft 对等互连，则代理与连接器之间的流量将通过该链路流动。 跃点 2 的延迟将得到优化。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="显示代理与连接器之间的 ExpressRoute 连接的示意图" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>用例 4

**方案：** 该应用位于组织在美国部署的网络中。 Azure 与企业网络之间存在结合专用对等互连的 ExpressRoute。

建议：遵循前一部分中所述的模式 3。

将连接器放置在通过 ExpressRoute 专用对等互连连接到企业网络的 Azure 数据中心内。

可将连接器放置在 Azure 数据中心内。 由于连接器仍旧通过专用网络与应用程序和数据中心建立直通连接，跃点 3 将保持优化。 此外，跃点 2 会进一步优化。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Azure 数据中心内的连接器，连接器与应用之间的 ExpressRoute" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>用例 5

**场景：** 应用位于组织的欧洲网络中，默认租户区域为美国，大部分用户位于欧洲。

**建议：** 将连接器放置在应用的附近。 更新连接器组，以便将其优化为使用欧洲应用程序代理服务实例。 有关步骤，请参阅[优化连接器组以使用最靠近的应用程序代理云服务](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)。

由于欧洲用户访问的应用程序代理实例正好处于同一区域，因此跃点 1 的系统开销不会太高。 跃点 3 经过优化。 考虑使用 ExpressRoute 来优化跃点 2。

### <a name="use-case-6"></a>用例 6

**场景：** 应用位于组织的欧洲网络中，默认租户区域为美国，大部分用户位于美国。

**建议：** 将连接器放置在应用的附近。 更新连接器组，以便将其优化为使用欧洲应用程序代理服务实例。 有关步骤，请参阅[优化连接器组以使用最靠近的应用程序代理云服务](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)。 跃点 1 的开销可能更高，因为所有美国用户必须访问位于欧洲的应用程序代理实例。

还可以考虑对此用例使用另一种变化形式。 如果组织中的大多数用户位于美国，则组织有可能也会将网络扩展到美国。 将连接器放在美国，继续为连接器组使用默认的美国区域，并对位于欧洲的应用程序使用专用的内部企业网络线路。 因此，跃点 2 和跃点 3 将得到优化。

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="显示用户、代理和连接器位于美国，应用位于欧洲的示意图。" lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>后续步骤

- [启用应用程序代理](application-proxy-add-on-premises-application.md)
- [启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
- [启用条件访问](./application-proxy-integrate-with-sharepoint-server.md)
- [解决使用应用程序代理时遇到的问题](application-proxy-troubleshoot.md)
