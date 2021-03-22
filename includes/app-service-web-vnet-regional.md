---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: a4eb22320a15cc76a7543c25583003d57ea4e538
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473831"
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

默认情况下，应用只将 RFC1918 流量路由到 VNet。 如果要将所有出站流量路由到 VNet，请将应用设置 WEBSITE_VNET_ROUTE_ALL 应用到你的应用。 配置应用设置：

1. 转到应用门户中的“配置”UI。 选择“新应用程序设置”。
1. 在“名称”框中输入“WEBSITE_VNET_ROUTE_ALL”，然后在“值”框中输入“1”   。

   ![提供应用程序设置][4]

1. 选择“确定”。
1. 选择“保存”。 

> [!NOTE]
> 如果将所有出站流量路由到 VNet 中，则它受应用于集成子网的 NSG 和 UDR 的约束。 将所有出站流量路由到 VNet 时，出站地址仍然是应用属性中列出的出站地址，除非提供将流量发送到其他位置的路由。
> 
> 区域 VNet 集成无法使用端口 25。

在同一区域的 VNet 中使用 VNet 集成存在一些限制：

* 无法跨全球对等互连连接访问资源。
* 高级版 V2 和高级版 V3 中的所有应用服务缩放单元均提供此功能。 此功能在标准版中也可用，但只能从新的应用服务缩放单元中使用。 如果使用旧的缩放单元，则只能通过高级版 V2 应用服务计划使用此功能。 如果想一定能够在标准应用服务计划中使用该功能，请在高级版 V3 应用服务计划中创建应用。 这些计划仅在我们最新的缩放单元上得到支持。 如果需要，之后可以纵向缩减。  
* 集成子网只能由一个应用服务计划使用。
* 应用服务环境中的独立计划应用无法使用此功能。
* 该功能需要一个未使用的子网，该子网在 Azure 资源管理器 VNet 中为 /28 或更大。
* 应用和 VNet 必须位于同一区域中。
* 不能删除带有集成应用的 VNet。 在删除 VNet 之前删除集成。
* 每个应用服务计划只能有一个区域 VNet 集成。 同一应用服务计划中的多个应用可以使用同一 VNet。
* 当某个应用使用区域 VNet 集成时，无法更改应用或计划的订阅。
* 如果不更改配置，应用无法解析 Azure DNS 专用区域中的地址

VNet 集成取决于专用子网的使用。  预配子网时，Azure 子网从一开始就丢失 5 个 IP。 每个计划实例使用集成子网中的一个地址。 如果将应用扩展到四个实例，则使用四个地址。 从子网大小中减去 5 个地址表示每个 CIDR 块的最大可用地址为：

- /28 包含 11 个地址
- /27 包含 27 个地址
- /26 包含 59 个地址

如果增加或减少规模，则需要在短时间内将你的地址扩大一倍。 大小限制表示每个子网大小的实际可用支持实例，如果子网是：

- /28，最大水平缩放为 5 个实例
- /27，最大水平缩放为 13 个实例
- /26，最大水平缩放为 29 个实例

在最大水平缩放上注明的限制假设你需要在某个点上增加或减少大小或 SKU。 

由于子网大小不能在分配后更改，请使用足够大的子网来容纳应用可能会达到的任何规模。 若要避免子网容量出现任何问题，建议的大小为包含 64 个地址的 /26。  

如果希望另一个计划中的应用访问已由另一个计划中的应用连接到的 VNet，请选择与预先存在的 VNet 集成所使用的子网不同的子网。

Windows 和 Linux 应用都完全支持此功能，包括[自定义容器](../articles/app-service/quickstart-custom-container.md)。 所有行为在 Windows 应用和 Linux 应用之间都是相同的。

### <a name="service-endpoints"></a>服务终结点

通过区域 VNet 集成可以使用服务终结点。 若要将服务终结点和应用配合使用，请使用区域 VNet 集成连接至所选 VNet，然后使用子网上用于集成的目标服务配置服务终结点。 如果要通过服务终结点访问服务：

1. 使用 Web 应用配置区域 VNet 集成
1. 转到目标服务并针对用于集成的子网配置服务终结点

### <a name="network-security-groups"></a>网络安全组

可以使用网络安全组阻止 VNet 中资源的入站和出站流量。 使用区域 VNet 集成的应用可以使用[网络安全组][VNETnsg]阻止 VNet 或 Internet 中资源的出站流量。 若要阻止流量进入公共地址，必须将应用程序设置 WEBSITE_VNET_ROUTE_ALL 设为 1。 NSG 中的入站规则不适用于你的应用，因为 VNet 集成只影响来自你的应用的出站流量。

若要控制应用的入站流量，请使用访问限制功能。 无论应用到集成子网的路由如何，应用到集成子网的 NSG 都有效。 如果 WEBSITE_VNET_ROUTE_ALL 设置为 1，并且集成子网上没有任何影响公共地址流量的路由，则所有出站流量仍受分配给集成子网的 NSG 的约束。 如果未设置 WEBSITE_VNET_ROUTE_ALL，则 NSG 仅应用于 RFC1918 流量。

### <a name="routes"></a>路由

可以使用路由表将出站流量从应用路由到你所需的任何位置。 默认情况下，路由表只影响 RFC1918 目标流量。 如果将 WEBSITE_VNET_ROUTE_ALL 设置为 1，则所有出站呼叫都会受到影响。 在集成子网上设置的路由不会影响对入站应用请求的答复。 常见目标可以包括防火墙设备或网关。

如果要路由本地的所有出站流量，可以使用路由表将所有出站流量发送到 ExpressRoute 网关。 如果确实要将流量路由到网关，请确保在外部网络中设置路由以发回任何回复。

边界网关协议 (BGP) 路由也会影响你的应用流量。 如果具有来自 ExpressRoute 网关等位置的 BGP 路由，应用出站流量将受到影响。 默认情况下，BGP 路由只影响 RFC1918 目标流量。 如果 WEBSITE_VNET_ROUTE_ALL 设置为 1，则所有出站流量都会受到 BGP 路由的影响。

### <a name="azure-dns-private-zones"></a>Azure DNS 专用区域 

在应用与 VNet 集成后，它将使用 VNet 配置的 DNS 服务器。 默认情况下，应用不能用于 Azure DNS 专用区域。 若要使用 Azure DNS 专用区域，你需要添加以下应用设置：


1. 值为 168.63.129.16 的 WEBSITE_DNS_SERVER
1. 值为 1 的 WEBSITE_VNET_ROUTE_ALL


这些设置不仅允许应用使用 Azure DNS 专用区域，而且还会将你所有的出站呼叫从应用发送到 VNet。   这些设置会将应用中的所有出站呼叫发送到 VNet 中。 此外，还会通过在辅助角色级别查询专用 DNS 区域，允许应用使用 Azure DNS。 当正在运行的应用访问专用 DNS 区域时，将使用此功能。

> [!NOTE]
>在 VNET 集成中，尝试使用专用 DNS 区域向 Web 应用添加自定义域是不可能的。 自定义域验证是在控制器级别完成的，而不是在辅助角色级别，后者会阻止显示 DNS 记录。 若要使用专用 DNS 区域中的自定义域，需要使用应用程序网关或 ILB 应用服务环境绕过验证。

### <a name="private-endpoints"></a>专用终结点

如果要调用[专用终结点][privateendpoints]，需要确保 DNS 查找解析到专用终结点。 若要确保应用的 DNS 查找指向专用终结点，可以执行以下操作：

* 与 Azure DNS 专用区域集成。 如果你的 VNet 没有自定义 DNS 服务器，则会自动执行此操作
* 管理 DNS 服务器中由你的应用使用的专用终结点。 为此，你需要知道专用终结点地址，然后用 A 记录将你尝试连接的终结点指向该地址。
* 将你自己的 DNS 服务器配置为转发到 Azure DNS 专用区域

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
