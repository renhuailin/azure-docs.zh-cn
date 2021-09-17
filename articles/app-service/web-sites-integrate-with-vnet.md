---
title: 将应用与 Azure 虚拟网络集成
description: 将 Azure 应用服务中的应用与 Azure 虚拟网络集成。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/04/2021
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ac90dadc93ce09bc2ce0af6314e4bd2c48ab79f8
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768668"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>将应用与 Azure 虚拟网络集成

本文介绍 Azure 应用服务 VNet 集成功能，并介绍如何为 [Azure 应用服务](./overview.md)中的应用设置此功能。 使用 [Azure 虚拟网络][VNETOverview] (VNet) 可将多个 Azure 资源置于无法通过 Internet 路由的网络中。 使用 VNet 集成功能，你的应用可以在 VNet 中访问资源，或者通过 VNet 来访问资源。 VNet 集成不允许以私密方式访问应用。

Azure 应用服务有两种变体：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>启用 VNet 集成

1. 在应用服务门户中转到“网络”UI。 在“VNet 集成”下，选择“单击此处进行配置”。 

1. 选择“添加 VNet”。

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-app.png" alt-text="选择 VNet 集成":::

1. 下拉列表包含订阅内位于相同区域中的所有 Azure 资源管理器虚拟网络。 下面是所有其他区域中资源管理器虚拟网络的列表。 选择要集成的 VNet。

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-add-vnet.png" alt-text="选择 VNet":::

    * 如果 VNet 位于同一区域，要么创建一个新的子网，要么选择一个已有的空子网。
    * 若要选择另一个区域中的 VNet，必须预配了一个已启用点到站点连接的 VNet 网关。
    * 若要与经典 VNet 集成，请不要选择“虚拟网络”下拉列表，而应选择“单击此处连接到经典 VNet”。  选择所需的经典虚拟网络。 目标 VNet 中必须已预配一个启用了点到站点连接的虚拟网关。

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-classic.png" alt-text="选择经典 VNet":::

在集成期间，应用会重启。 完成集成后，系统将显示你与之集成的 VNet 的详细信息。

## <a name="regional-vnet-integration"></a>区域 VNet 集成

区域 VNet 集成支持连接到同一区域中的 VNet，而不需要网关。 通过区域 VNet 集成，应用可以访问：

* 在应用所在区域的 VNet 中的资源。
* 与应用集成的 VNet 建立了对等互连的 VNet 中的资源。
* 服务终结点保护的服务。
* 跨 Azure ExpressRoute 连接的资源。
* 集成的 VNet 中的资源。
* 跨对等连接的资源，包括 Azure ExpressRoute 连接。
* 启用专用终结点的服务。

在同一区域中的 VNet 中使用 VNet 集成时，可以使用以下 Azure 网络功能：

* **网络安全组 (NSG)** ：可以使用集成子网上的 NSG 阻止出站流量。 入站规则不适用，因为无法使用 VNet 集成来提供对应用的入站访问。
* **路由表 (UDR)** ：可以在集成子网上放置路由表，以便将出站流量发送到所需的位置。

Windows 和 Linux 应用都完全支持此功能，包括[自定义容器](./quickstart-custom-container.md)。 所有行为在 Windows 应用和 Linux 应用之间都是相同的。

### <a name="how-regional-vnet-integration-works"></a>区域 VNet 集成的工作原理

应用服务中的应用托管在辅助角色上。 基本和更高的定价计划是专用托管计划，其中不会有任何其他客户的工作负载在同一辅助角色上运行。 区域 VNet 集成通过使用委托子网中的地址装载虚拟接口实现。 发送地址位于 VNet 中，因此它可以像 VNet 中的 VM 那样，访问位于 VNet 中或通过 VNet 传输的大多数内容。 网络实现不同于在 VNet 中运行 VM。 这就是一些网络功能尚不可用于此功能的原因。

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-regional-works.png" alt-text="区域 VNet 集成的工作原理":::

启用区域 VNet 集成时，应用会通过 VNet 进行出站。 应用属性门户中列出的出站地址是应用仍然在使用的地址。 如果启用了“所有流量路由”，则会将所有出站流量发送到 VNet。 如果未启用“所有流量路由”，则只会将在集成子网上配置的专用流量 (RFC1918) 和服务终结点发送到 VNet，而发送到 Internet 的出站流量将照常通过相同通道。

此功能仅支持每个辅助角色一个虚拟接口。 每个辅助角色一个虚拟接口意味着每个应用服务计划一个区域 VNet 集成。 同一个应用服务计划中的所有应用都可以使用相同的 VNet 集成。 如果需要使用一个应用来连接其他 VNet，你需要另外创建一个应用服务计划。 使用的虚拟接口不是客户可直接访问的资源。

由于此技术的性质，用于 VNet 集成的流量不显示在 Azure 网络观察程序或 NSG 流日志中。

### <a name="subnet-requirements"></a>子网要求

VNet 集成取决于专用子网。 预配子网时，Azure 子网从一开始就丢失 5 个 IP。 每个计划实例使用集成子网中的一个地址。 如果将应用缩放到 4 个实例，则使用 4 个地址。 

增加或缩减大小时，所需的地址空间会在短时间内增加一倍。 对于指定的子网大小，这会影响其实际可用的受支持实例。 下表显示了每个 CIDR 块的最大可用地址数，以及这对横向缩放的影响：

| CIDR 块大小 | 最大可用地址数 | 最大横向缩放（实例）数<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>假设需要在某些时候增加或缩减大小或 SKU。 

由于子网大小不能在分配后更改，请使用足够大的子网来容纳应用可能会达到的任何规模。 若要避免子网容量出现任何问题，应使用包含 64 个地址的 /26。

如果希望计划中的应用访问已由其他计划中的应用连接到的 VNet，请选择与预先存在的 VNet 集成所用的子网不同的子网。

### <a name="routes"></a>路由

配置区域 VNet 集成时，需要考虑两种类型的路由。 应用程序路由定义从应用程序路由的流量以及路由到 VNet 的流量。 网络路由能够控制流量从 VNet 路由以及路由出去的方式。

#### <a name="application-routing"></a>应用程序路由

配置应用程序路由时，可将所有流量或仅将专用流量（也称为 [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) 流量）路由到 VNet。 通过“全部路由”设置来配置此设置。 如果已禁用“全部路由”，你的应用只会将专用流量路由到 VNet。 如果要将所有出站流量路由到 VNet，请确保已启用“全部路由”。

> [!NOTE]
> * 如果启用了“全部路由”，则所有流量均受适用于集成子网的 NSG 和 UDR 的限制。 如果启用了“所有流量路由”，出站流量还是会从应用属性中列出的地址发送，除非提供可将流量定向到其他位置的路由。
> 
> * Windows 容器目前不支持“全部路由”。
>
> * 区域 VNet 集成无法使用端口 25。

你可按照以下步骤，通过门户在应用中禁用“全部路由”： 

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-enabled.png" alt-text="已启用“全部路由”":::

1. 请在应用门户中转到“VNet 集成”UI。
1. 将“全部路由”设置为“已禁用”。
    
    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-disabling.png" alt-text="禁用“全部路由”":::

1. 请选择“是”。

还可使用 CLI 配置“全部路由”（注意：所需的 `az version` 最低版本为 2.27.0）：

```azurecli-interactive
az webapp config set --resource-group myRG --name myWebApp --vnet-route-all-enabled [true|false]
```

“全部路由”配置设置是启用所有流量的路由的建议方法。 通过此配置设置，你将可以使用[内置策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef)来审核行为。 现有的 `WEBSITE_VNET_ROUTE_ALL` 应用设置仍可使用，你可以使用任一设置启用所有流量路由。

#### <a name="network-routing"></a>网络路由

可以使用路由表将出站流量从应用路由到你所需的任何位置。 路由表影响目标流量。 在[应用程序路由](#application-routing)中禁用“全部路由”时，只有专用流量 (RFC1918) 受路由表的影响。 常见目标可以包括防火墙设备或网关。 在集成子网上设置的路由不会影响对入站应用请求的答复。 

如果要路由本地的所有出站流量，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果确实要将流量路由到网关，请确保在外部网络中设置路由以发回任何回复。

边界网关协议 (BGP) 路由也会影响你的应用流量。 如果具有来自 ExpressRoute 网关等位置的 BGP 路由，应用出站流量会受到影响。 与用户定义的路由相似，BGP 路由对流量的影响取决于路由范围设置。

### <a name="network-security-groups"></a>网络安全组

使用区域 VNet 集成的应用可使用[网络安全组][VNETnsg]阻止 VNet 或 Internet 中资源的出站流量。 若要阻止到公共地址的流量，必须确保启用了到 VNet 的[全部路由](#application-routing)。 如果未启用“全部路由”，则 NSG 仅适用于 RFC1918 流量。

无论应用到集成子网的路由表如何，应用到集成子网的 NSG 都有效。 

NSG 中的入站规则不适用于你的应用，因为 VNet 集成只影响来自你的应用的出站流量。 若要控制应用的入站流量，请使用访问限制功能。

### <a name="service-endpoints"></a>服务终结点

通过区域 VNet 集成能够访问通过服务终结点保护的 Azure 服务。 若要访问服务终结点保护的服务，必须执行以下操作：

* 配置 Web 应用的区域 VNet 集成，以连接到用于集成的特定子网。
* 转到目标服务并针对集成子网配置服务终结点。

### <a name="private-endpoints"></a>专用终结点

如果要调用[专用终结点][privateendpoints]，则必须确保 DNS 查找解析到专用终结点。 可以使用以下方式之一强制执行此行为： 

* 与 Azure DNS 专用区域集成。 如果 VNet 没有自定义 DNS 服务器，会在区域链接到 VNet 时自动执行此操作。
* 管理 DNS 服务器中由你的应用使用的专用终结点。 为此，你需要知道专用终结点地址，然后用 A 记录将你尝试连接的终结点指向该地址。
* 将你自己的 DNS 服务器配置为转发到 Azure DNS 专用区域。

### <a name="azure-dns-private-zones"></a>Azure DNS 专用区域 

应用与 VNet 集成后，会使用与 VNet 配置的相同 DNS 服务器；如果未指定任何自定义 DNS，则将使用 Azure 默认 DNS 以及链接到 VNet 的任意专用区域。

> [!NOTE]
> 对于 Linux 应用，Azure DNS 专用区域仅在已启用“全部路由”时才工作。

### <a name="limitations"></a>限制

在同一区域的 VNet 中使用 VNet 集成存在一些限制：

* 无法跨全球对等互连连接访问资源。
* 无法使用经典虚拟网络通过对等互连连接访问资源。
* 高级版 V2 和高级版 V3 中的所有应用服务缩放单元均提供此功能。 此功能在标准版中也可用，但只能从较新的应用服务缩放单元中使用。 如果使用较旧的缩放单元，则只能通过高级版 V2 应用服务计划使用此功能。 如果想确保能够在标准应用服务计划中使用该功能，请在高级版 V3 应用服务计划中创建应用。 这些计划仅在我们最新的缩放单元上得到支持。 如果需要，之后可以纵向缩减。  
* 集成子网只能由一个应用服务计划使用。
* 应用服务环境中的独立计划应用无法使用此功能。
* 该功能需要一个未使用的子网，该子网在 Azure 资源管理器 VNet 中为 /28 或更大。
* 应用和 VNet 必须位于同一区域中。
* 不能删除带有集成应用的 VNet。 在删除 VNet 之前删除集成。
* 每个应用服务计划只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用同一 VNet。
* 当某个应用使用区域 VNet 集成时，无法更改应用或计划的订阅。
* 如果不更改配置，应用就无法解析 Linux 计划上 Azure DNS 专用区域中的地址。

## <a name="gateway-required-vnet-integration"></a>需要网关的 VNet 集成

需要网关的 VNet 集成支持连接到另一区域中的 VNet，或连接到经典虚拟网络。 需要网关的 VNet 集成：

* 允许应用一次只连接到一个 VNet。
* 允许在一个应用服务计划中最多集成 5 个 VNet。
* 允许在应用服务计划中由多个应用使用同一个 VNet，不影响可供应用服务计划使用的总数。 如果有 6 个应用在使用同一应用服务计划中的同一 VNet，则算作是使用了一个 VNet。
* 由于 SLA 是基于网关，因此可实现 99.9% 的 SLA。
* 允许应用使用配置给 VNet 的 DNS。
* 需要在基于虚拟网络路由的网关中配置 SSTP 点到站点 VPN，然后才能将其连接到应用。

需要网关的 VNet 集成不可用于：

* 通过 Azure ExpressRoute 连接的 VNet。
* Linux 应用中。
* [Windows 容器中](quickstart-custom-container.md)。
* 访问服务终结点保护的资源。
* 既支持 ExpressRoute，也支持点到站点 VPN 或站点到站点 VPN 的共存网关。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>在 Azure 虚拟网络中设置网关

若要创建网关，请执行以下操作：

1. 在 VNet 中[创建网关子网][creategatewaysubnet]。

1. [创建 VPN 网关][creategateway]。 选择基于路由的 VPN 类型。

1. [设置点到站点地址][setp2saddresses]。 如果网关不在基本 SKU 中，则必须在点到站点配置中禁用 IKEV2 并选择 SSTP。 点到站点地址空间必须在 RFC 1918 地址块 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16 中。

如果创建用于应用服务 VNet 集成的网关，则不需要上传证书。 创建网关可能需要 30 分钟。 若要将应用与 VNet 集成，必须先预配网关。

### <a name="how-gateway-required-vnet-integration-works"></a>需要网关的 VNet 集成的工作原理

需要网关的 VNet 集成基于点到站点 VPN 技术。 点到站点 VPN 将网络访问限制于可托管应用的虚拟机。 应用受到限制，只能通过混合连接或 VNet 集成向外发送流量至 Internet。 通过门户将应用配置为使用需要网关的 VNet 集成后，系统会代你管理复杂的协商，以便在网关上和应用程序端创建并分配证书。 结果是，用于托管应用的辅助角色能够直接连接到所选 VNet 中的虚拟网关。

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-gateway-works.png" alt-text="需要网关的 VNet 集成的工作原理":::

### <a name="access-on-premises-resources"></a>访问本地资源

应用可以通过与具备站点到站点连接的 VNet 集成来访问本地资源。 如果使用需要网关的 VNet 集成，请使用点到站点地址块更新本地 VPN 网关路由。 先设置站点到站点 VPN，接着应通过用于配置该 VPN 的脚本来正确地设置路由。 如果在创建站点到站点地址后才添加点到站点 VPN，则需手动更新路由。 操作详情取决于每个网关，在此不作说明。 你不能使用站点到站点 VPN 连接配置 BGP。

区域 VNet 集成功能无需额外的配置即可通过 VNet 连接到本地资源。 只需使用 ExpressRoute 或站点到站点 VPN 将 VNet 连接到本地资源。

> [!NOTE]
> 需要网关的 VNet 集成功能不将应用与包含 ExpressRoute 网关的 VNet 集成。 即使以[共存模式][VPNERCoex]配置 ExpressRoute 网关，VNet 集成也不会生效。 如果需要通过 ExpressRoute 连接访问资源，请使用区域 VNet 集成功能或在 VNet 中运行的[应用服务环境][ASE]。
>
>

### <a name="peering"></a>对等互连

如果将对等互连与区域 VNet 集成结合使用，无需进行任何其他配置。

如果将需要网关的 VNet 集成与对等互连结合使用，需要额外配置几个项。 若要配置对等互连以使用应用，请执行以下操作：

1. 在应用所连接的 VNet 上添加对等互连连接。 在添加对等互连连接时，请启用“允许虚拟网络访问”并选择“允许转发流量”和“允许网关传输”  。
1. 在与所连接的 VNet 对等互连的 VNet 上添加对等互连连接。 在目标 VNet 上添加对等互连连接时，请启用“允许虚拟网络访问”并选择“允许转发流量”和“允许远程网关”  。
1. 在门户中转到“应用服务计划” > “网络” > “VNet 集成”UI。 选择应用连接的 VNet。 在路由部分，添加与应用所连接的 VNet 对等互连的 VNet 的地址范围。

## <a name="manage-vnet-integration"></a>管理 VNet 集成

与 VNet 连接和断开连接都在应用级别进行的。 可能影响多个应用的 VNet 集成的操作在应用服务计划级别执行。 可以通过应用 >“网络” > “VNet 集成”门户获取 VNet 的详细信息。 可以在“应用服务计划” > “网络” > “VNet 集成”门户中查看应用服务计划级别的类似信息。

在 VNet 集成实例的应用视图中，能够执行的唯一操作是断开应用与当前连接到的 VNet 的连接。 若要断开应用与 VNet 的连接，请选择“断开连接”。 断开与 VNet 的连接后，应用会重启。 断开连接操作不会更改 VNet。 不会删除子网或网关。 若要删除 VNet，请先断开应用与该 VNet 的连接，然后删除该 VNet 中的资源，例如网关。

应用服务计划 VNet 集成 UI 显示应用服务计划中应用使用的所有 VNet 集成。 若要查看单个 VNet 的详细信息，请选择你感兴趣的 VNet。 在此处，可以针对需要网关的 VNet 集成执行两项操作：

* **同步网络**：同步网络操作仅用于网关相关的 VNet 集成功能。 执行同步网络操作确保了证书与网络信息是同步的。如果添加或更改 VNet 的 DNS，请执行同步网络操作。 此操作重启使用此 VNet 的任何应用。 如果你使用的是属于不同订阅的应用和 VNet，此操作无效。
* **添加路由**：添加路由会促使出站流量进入 VNet。

分配给实例的专用 IP 是通过环境变量 WEBSITE_PRIVATE_IP 公开的。 Kudu 控制台 UI 也显示了可用于 Web 应用的环境变量的列表。 此 IP 是从集成子网的地址范围中分配的。 对于区域 VNet 集成，WEBSITE_PRIVATE_IP 的值是委托子网的地址范围中的一个 IP；对于需要网关的 VNet 集成，此值是在虚拟网络网关上配置的点到站点地址池的地址范围中的一个 IP。 这是 Web 应用通过虚拟网络连接到资源时将使用的 IP。 

> [!NOTE]
> WEBSITE_PRIVATE_IP 的值必然会变化。 但是，它将是集成子网的地址范围或点到站点地址范围内的一个 IP，因此你需要允许从整个地址范围进行访问。
>

### <a name="gateway-required-vnet-integration-routing"></a>需要网关的 VNet 集成路由
在 VNet 中定义的路由用于将流量从应用导入 VNet。 如果需要将其他出站流量发送到 VNet 中，请在此处添加地址块。 此功能仅适用于需要网关的 VNet 集成。 使用需要网关的 VNet 集成时，路由表不会像使用区域 VNet 集成时那样影响应用流量。

### <a name="gateway-required-vnet-integration-certificates"></a>需要网关的 VNet 集成证书
启用需要网关的 VNet 集成后，必须进行证书交换以确保连接的安全性。 除了证书，还有 DNS 配置、路由以及其他类似的用于描述网络的内容。

如果更改了证书或网络信息，请选择“同步网络”。 选择“同步网络”会导致应用与 VNet 之间的连接出现短暂的中断。 虽然应用不会重启，但失去连接会导致站点功能失常。

## <a name="pricing-details"></a>定价详细信息
除了应用服务计划定价层收费以外，区域 VNet 集成功能没有其他使用费。

使用需要网关的 VNet 集成功能涉及三项费用：

* 应用服务计划定价层费用：应用必须属于“标准”、“高级”、“高级 V2”或“高级 V3”应用服务计划。 有关这些费用的详细信息，请参阅[应用服务定价][ASPricing]。
* **数据传输费用**：传出数据会产生费用，即使 VNet 位于同一数据中心也是如此。 [数据传输定价详细信息][DataPricing]中对这些费用进行了说明。
* **VPN 网关费用**：点到站点 VPN 所需的虚拟网关会产生费用。 有关详细信息，请参阅 [VPN 网关定价][VNETPricing]。

## <a name="troubleshooting"></a>故障排除

> [!NOTE]
> 应用服务中的 Docker Compose 方案不支持 VNET 集成。
> 如果存在专用终结点，则会忽略 Azure Functions 访问限制。
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>自动化

为区域 VNet 集成提供了 CLI 支持。 要访问以下命令，请[安装 Azure CLI][installCLI]。

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.
```

还提供了对区域 VNet 集成的 PowerShell 支持，但你必须使用子网 resourceID 的属性数组创建通用资源

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

# Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

# Creation of the VNet Integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```

<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
[VNETnsg]: /azure/virtual-network/security-overview/
