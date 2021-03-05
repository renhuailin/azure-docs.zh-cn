---
title: 教程：配置 ExpressRoute 线路的对等互连 - Azure 门户
description: 本教程介绍如何使用 Azure 门户创建和预配 ExpressRoute 专用对等互连和 Microsoft 对等互连。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 11a4798c0cb3bc010bbdbae1fcb709951c67781a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721854"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>教程：使用 Azure 门户创建和修改 ExpressRoute 线路的对等互连

本教程介绍如何使用 Azure 门户创建和管理 Azure 资源管理器 ExpressRoute 线路的路由配置。 还可以检查 ExpressRoute 线路的状态，更新、删除和取消预配其对等互连。 如果想使用不同的方法处理线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [公共对等互连](about-public-peering.md)
> * [视频 - 专用对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [视频 - Microsoft 对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-routing-classic.md)
> 

可以配置 ExpressRoute 线路的专用对等互连和 Microsoft 对等互连（对于新线路，Azure 公共对等互连已弃用）。 可以按所选的任意顺序配置对等互连。 但是，必须确保一次只完成一个对等互连的配置。 有关路由域和对等互连的详细信息，请参阅 [ExpressRoute 路由域](expressroute-circuit-peerings.md)。 有关公共对等互连的信息，请参阅 [ExpressRoute 公共对等互连](about-public-peering.md)。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 配置、更新和删除线路的 Microsoft 对等互连
> - 配置、更新和删除线路的 Azure 专用对等互连

## <a name="prerequisites"></a>必备条件

* 在开始配置之前，确保已查看以下页面：
    * [先决条件](expressroute-prerequisites.md) 
    * [路由要求](expressroute-routing.md)
    * [工作流](expressroute-workflows.md)
* 必须有一个活动的 ExpressRoute 线路。 在继续之前，请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)，并通过连接提供商启用该线路。 若要配置对等互连，ExpressRoute 线路必须处于已预配且已启用状态。 
* 如果计划使用共享密钥/MD5 哈希，请确保同时在隧道两侧使用密钥。 其限制为最多 25 个字母数字的字符。 不支持特殊字符。 

这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商会配置和管理路由。 

> [!IMPORTANT]
> 我们目前无法通过服务管理门户播发服务提供商配置的对等互连。 我们正在努力不久就实现这一功能。 请在配置 BGP 对等互连之前与服务提供商协商。
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft 对等互连

本文介绍如何为 ExpressRoute 线路创建、获取、更新和删除 Microsoft 对等互连配置。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。 有关详细信息，请参阅[配置用于 Microsoft 对等互连的路由筛选器](how-to-routefilter-powershell.md)。
> 
> 

### <a name="to-create-microsoft-peering"></a>创建 Microsoft 对等互连

1. 配置 ExpressRoute 线路。 在进一步继续之前，请检查 **提供程序状态** 以确保线路完全由连接提供商预配。

   如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Microsoft 对等互连。 无需遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后继续执行这些步骤。

   **线路 - 提供商状态：未预配**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="显示 ExpressRoute 演示线路的“概述”页面的屏幕截图，其中用红色框突出显示了设置为“未预配”的提供商状态":::

   **线路 - 提供商状态：已预配**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="显示 ExpressRoute 演示线路的“概述”页面的屏幕截图，其中用红色框突出显示了设置为“已预配”的提供商状态":::

2. 配置线路的 Microsoft 对等互连。 在继续之前，请确保已准备好以下信息。

   * 由你拥有并在 RIR/IRR 中注册的一对子网。 一个子网将用于主链路，而另一个子网将用于辅助链路。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。 对于这对子网，你有三个选择：
       * IPv4：两个/30 个子网。 它们必须是有效的公共 IPv4 前缀。
       * IPv6：两个/126 个子网。 它们必须是有效的公共 IPv6 前缀。
       * IPv4 和 IPv6：两个/30 个子网和两个/126 个子网。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。 主要链接和次要链接必须使用相同的 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * 播发的前缀：提供你计划要通过 BGP 会话播发的所有前缀的列表。 只接受公共 IP 地址前缀。 如果打算发送一组前缀，可以发送逗号分隔列表。 这些前缀必须已在 RIR/IRR 中注册。
   * “可选”- 客户 ASN：如果要播发的前缀未注册到对等互连 AS 编号，可以指定要注册到的 AS 编号。
   * 路由注册表名称：可以指定 AS 编号和前缀要注册到的 RIR/IRR。
   * **可选** - MD5 哈希（如果选择使用）。
1. 可以选择想要配置的对等互连，如以下示例中所示。 选择 Microsoft 对等互连行。

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="选择 Microsoft 对等互连行":::

4. 配置 Microsoft 对等互连。 指定所有参数后，请保存配置。 下图显示了一个示例配置：

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="配置所需的 Microsoft 对等互连验证":::

> [!IMPORTANT]
> Microsoft 会验证指定的“播发的公用前缀”和“对等 ASN”（或“客户 ASN”）是否已在 Internet 路由注册表中分配给你。 如果要从其他实体获取公用前缀，并且没有在路由注册表中记录分配，则自动验证不会完成，需要手动验证。 如果自动验证失败，则你会看到消息“需要验证”。 
>
> 如果看到消息“需要验证”，请收集相关文档，它们显示公用前缀已由在路由注册表中作为前缀所有者列出的实体分配给你的组织，然后通过开具支持票证来提交这些文档以进行手动验证。 
>

   如果线路达到“需要验证”状态，则必须打开支持票证以向我们的支持团队显示前缀所有权的证明。 可以直接从门户中打开支持票证，如以下示例中所示：

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="需要验证 - 支持票证":::

5. 成功接受配置后，你将看到类似于下图的内容：

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="对等互连状态：已配置":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>查看 Microsoft 对等互连详细信息

可以通过选择对等互连行来查看 Microsoft 对等互连的属性。

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="查看 Microsoft 对等互连属性":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>更新 Microsoft 对等互连配置

可以选择要修改的对等互连行，然后修改对等互连属性并保存修改。

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="选择对等互连行":::

## <a name="azure-private-peering"></a><a name="private"></a>Azure 专用对等互连

本文介绍了如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。

> [!IMPORTANT]
> 对专用对等互连的 IPv6 支持目前为公共预览版。 如果要将虚拟网络连接到配置了基于 IPv6 的专用对等互连的 ExpressRoute 线路，请确保虚拟网络为双重堆栈，并遵循[此处](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)所述的指南。
> 
> 

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连

1. 配置 ExpressRoute 线路。 在继续之前，请确保线路完全由连接提供商设置。 

   如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 无需遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后继续执行后续步骤。

   **线路 - 提供商状态：未预配**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="显示 ExpressRoute 演示线路的“概述”页面的屏幕截图，其中用红色框突出显示了设置为“未预配”的提供商状态":::

   **线路 - 提供商状态：已预配**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="显示 ExpressRoute 演示线路的“概述”页面的屏幕截图，其中用红色框突出显示了设置为“已预配”的提供商状态":::

2. 配置线路的 Azure 专用对等互连。 在继续执行后续步骤之前，确保已准备好以下各项：

   * 不属于为虚拟网络保留的任何地址空间的一对子网。 一个子网将用于主链路，而另一个子网将用于辅助链路。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。 对于这对子网，你有三个选择：
       * IPv4：两个/30 个子网。
       * IPv6：两个/126 个子网。
       * IPv4 和 IPv6：两个/30 个子网和两个/126 个子网。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。 主要链接和次要链接必须使用相同的 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以使用专用 AS 编号建立对等互连（65515 到 65520 之间的数字除外）。
   * 配置专用对等互连后，必须通过 BGP 将路由从本地边缘路由器播发到 Azure。
   * **可选** - MD5 哈希（如果选择使用）。
3. 选择 Azure 专用对等互连行，如以下示例中所示：

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="选择专用对等互连行":::

4. 配置专用对等互连。 指定所有参数后，请保存配置。

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="配置专用对等互连":::

5. 成功接受配置后，会看到类似于以下示例的内容：

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="已保存专用对等互连":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>查看 Azure 专用对等互连详细信息

可以通过选择对等互连查看 Azure 专用对等互连的属性。

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="查看专用对等互连属性":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>更新 Azure 专用对等互连配置

可以选择用于对等互连的行并修改对等互连属性。 更新后，保存所做的更改。

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="更新专用对等互连":::

## <a name="clean-up-resources"></a>清理资源

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>删除 Microsoft 对等互连

可以通过右键单击对等互连，然后选择“删除”来删除 Microsoft 对等互连配置，如下图所示：

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="删除 Microsoft 对等互连":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>删除 Azure 专用对等互连

可以通过右键单击对等互连，然后选择“删除”来删除专用对等互连配置，如下图所示：

> [!WARNING]
> 运行此操作前，必须确保已删除所有虚拟网络和 ExpressRoute Global Reach 连接。 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="删除专用对等互连":::

## <a name="next-steps"></a>后续步骤

配置 Azure 专用对等互连后，可以创建 ExpressRoute 网关，以将虚拟网络链接到线路。 

> [!div class="nextstepaction"]
> [配置 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)
