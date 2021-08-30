---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 399ce5e8714eb6935e3c2eac06ed44b712a14e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741416"
---
通过区域 VNet 集成，应用可以访问：

* 在应用所在区域的 VNet 中的资源。
* 与应用集成的 VNet 建立了对等互连的 VNet 中的资源。
* 服务终结点保护的服务。
* 跨 Azure ExpressRoute 连接的资源。
* 集成的 VNet 中的资源。
* 跨对等连接的资源，包括 Azure ExpressRoute 连接。
* 专用终结点 

在同一区域中的 VNet 中使用 VNet 集成时，可以使用以下 Azure 网络功能：

* **网络安全组 (NSG)** ：可以使用集成子网上的 NSG 阻止出站流量。 入站规则不适用，因为无法使用 VNet 集成来提供对应用的入站访问。
* **路由表 (UDR)** ：可以在集成子网上放置路由表，以便将出站流量发送到所需的位置。

默认情况下，应用只将 RFC1918 流量路由到 VNet。 如果要将所有出站流量路由到 VNet，请使用以下步骤在应用中添加 `WEBSITE_VNET_ROUTE_ALL` 设置： 

1. 转到应用门户中的“配置”UI。 选择“新应用程序设置”。
1. 在“名称”框中输入 `WEBSITE_VNET_ROUTE_ALL`，然后在“值”框中输入 `1` 。

   ![提供应用程序设置][4]

1. 选择“确定”。
1. 选择“保存”。

> [!NOTE]
> 如果将所有出站流量路由到 VNet，它会受应用于集成子网的 NSG 和 UDR 的约束。 如果将 `WEBSITE_VNET_ROUTE_ALL` 设置为 `1`，流向公共 IP 地址的出站流量还是会从应用属性中列出的地址发送，除非提供可将流量定向到其他位置的路由。
> 
> 区域 VNet 集成无法使用端口 25。

在同一区域的 VNet 中使用 VNet 集成存在一些限制：

* 无法跨全球对等互连连接访问资源。
* 高级版 V2 和高级版 V3 中的所有应用服务缩放单元均提供此功能。 此功能在标准版中也可用，但只能从较新的应用服务缩放单元中使用。 如果使用较旧的缩放单元，则只能通过高级版 V2 应用服务计划使用此功能。 如果想确保能够在标准应用服务计划中使用该功能，请在高级版 V3 应用服务计划中创建应用。 这些计划仅在我们最新的缩放单元上得到支持。 如果需要，之后可以纵向缩减。  
* 集成子网只能由一个应用服务计划使用。
* 应用服务环境中的独立计划应用无法使用此功能。
* 该功能需要一个未使用的子网，该子网在 Azure 资源管理器 VNet 中为 /28 或更大。
* 应用和 VNet 必须位于同一区域中。
* 不能删除带有集成应用的 VNet。 在删除 VNet 之前删除集成。
* 每个应用服务计划只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用同一 VNet。
* 当某个应用使用区域 VNet 集成时，无法更改应用或计划的订阅。
* 如果不更改配置，应用就无法解析 Azure DNS 专用区域中的地址。

VNet 集成取决于专用子网。 预配子网时，Azure 子网从一开始就丢失 5 个 IP。 每个计划实例使用集成子网中的一个地址。 如果将应用缩放到 4 个实例，则使用 4 个地址。 

增加或缩减大小时，所需的地址空间会在短时间内增加一倍。 对于指定的子网大小，这会影响其实际可用的受支持实例。 下表显示了每个 CIDR 块的最大可用地址数，以及这对横向缩放的影响：

| CIDR 块大小 | 最大可用地址数 | 最大横向缩放（实例）数<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>假设需要在某些时候增加或缩减大小或 SKU。 

由于子网大小不能在分配后更改，请使用足够大的子网来容纳应用可能会达到的任何规模。 若要避免子网容量出现任何问题，应使用包含 64 个地址的 /26。  

如果希望另一个计划中的应用访问已由其他计划中的应用连接到的 VNet，请选择与预先存在的 VNet 集成所用的子网不同的子网。

Windows 和 Linux 应用都完全支持此功能，包括[自定义容器](../articles/app-service/quickstart-custom-container.md)。 所有行为在 Windows 应用和 Linux 应用之间都是相同的。

### <a name="service-endpoints"></a>服务终结点

通过区域 VNet 集成能够访问通过服务终结点保护的 Azure 服务。 若要访问服务终结点保护的服务，必须执行以下操作：

1. 配置 Web 应用的区域 VNet 集成，以连接到用于集成的特定子网。
1. 转到目标服务并针对集成子网配置服务终结点。

### <a name="network-security-groups"></a>网络安全组

可以使用网络安全组阻止 VNet 中资源的入站和出站流量。 使用区域 VNet 集成的应用可以使用[网络安全组][VNETnsg]阻止 VNet 或 Internet 中资源的出站流量。 若要阻止流量进入公共地址，必须将应用程序设置 `WEBSITE_VNET_ROUTE_ALL` 设为 `1`。 NSG 中的入站规则不适用于你的应用，因为 VNet 集成只影响来自你的应用的出站流量。

若要控制应用的入站流量，请使用访问限制功能。 无论应用到集成子网的路由如何，应用到集成子网的 NSG 都有效。 如果 `WEBSITE_VNET_ROUTE_ALL` 设置为 `1`，并且集成子网上没有任何影响公共地址流量的路由，则所有出站流量仍受分配给集成子网的 NSG 的影响。 如果未设置 `WEBSITE_VNET_ROUTE_ALL`，则 NSG 仅应用于 RFC1918 流量。

### <a name="routes"></a>路由

可以使用路由表将出站流量从应用路由到你所需的任何位置。 默认情况下，路由表只影响 RFC1918 目标流量。 如果将 `WEBSITE_VNET_ROUTE_ALL` 设为 `1`，则所有出站调用都会受到影响。 在集成子网上设置的路由不会影响对入站应用请求的答复。 常见目标可以包括防火墙设备或网关。

如果要路由本地的所有出站流量，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果确实要将流量路由到网关，请确保在外部网络中设置路由以发回任何回复。

边界网关协议 (BGP) 路由也会影响你的应用流量。 如果具有来自 ExpressRoute 网关等位置的 BGP 路由，应用出站流量会受到影响。 默认情况下，BGP 路由只影响 RFC1918 目标流量。 如果 `WEBSITE_VNET_ROUTE_ALL` 设为 `1`，则所有出站流量都会受到 BGP 路由的影响。

### <a name="azure-dns-private-zones"></a>Azure DNS 专用区域 

在应用与 VNet 集成后，它将使用 VNet 配置的 DNS 服务器。 默认情况下，应用不能用于 Azure DNS 专用区域。 若要使用 Azure DNS 专用区域，需要添加以下应用设置：

1. 值为 `168.63.129.16` 的 `WEBSITE_DNS_SERVER`
1. 值为 `1` 的 `WEBSITE_VNET_ROUTE_ALL`

这些设置会将所有出站调用从应用发送到 VNet，还允许应用访问 Azure DNS 专用区域。 借助这些设置，应用可通过在辅助角色级别查询 DNS 专用区域来使用 Azure DNS。  

### <a name="private-endpoints"></a>专用终结点

如果要调用[专用终结点][privateendpoints]，必须确保 DNS 查找解析到专用终结点。 可以使用以下方式之一强制执行此行为： 

* 与 Azure DNS 专用区域集成。 如果 VNet 没有自定义 DNS 服务器，则会自动执行此操作。
* 管理 DNS 服务器中由你的应用使用的专用终结点。 为此，你需要知道专用终结点地址，然后用 A 记录将你尝试连接的终结点指向该地址。
* 将你自己的 DNS 服务器配置为转发到 Azure DNS 专用区域。

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
