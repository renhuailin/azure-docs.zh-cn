---
title: 网络注意事项
description: 了解 ASE 网络流量以及如何通过 ASE 设置网络安全组和用户定义的路由。
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: eaa4eafa283ad46ae977d9449dcf9f9273bef38c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669106"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>应用服务环境的网络注意事项 #

> [!NOTE]
> 本文介绍用于独立应用服务计划的应用服务环境 v2
> 

## <a name="overview"></a>概述 ##

 Azure [应用服务环境][Intro]是指将 Azure 应用服务部署到 Azure 虚拟网络 (VNet) 的子网中。 应用服务环境 (ASE) 具有两种部署类型：

- **外部 ASE**：在 Internet 可访问的 IP 地址上公开 ASE 托管的应用。 有关详细信息，请参阅[创建外部 ASE][MakeExternalASE]。
- **ILB ASE**：在 VNet 中的 IP 地址上公开 ASE 托管的应用。 内部终结点是一个内部负载均衡器 (ILB)，因此该类部署被称为 ILB ASE。 有关详细信息，请参阅[创建和使用 ILB ASE][MakeILBASE]。

所有 ASE、外部组件和 ILB 都有一个公共 VIP，该 VIP 用于入站管理流量，从 ASE 对 Internet 发出调用时，它还用作来源地址。 从 ASE 发出的、转到 Internet 的所有调用将通过分配给 ASE 的 VIP 离开 VNet。 此 VIP 的公共 IP 将成为从 ASE 发出的、转到 Internet 的所有调用的源 IP。 如果 ASE 中的应用调用了 VNet 中的资源或通过 VNet 发出调用，则源 IP 是 ASE 使用的子网中的某个 IP。 由于 ASE 在 VNet 中，因此也可以访问 VNet 中的资源，而不需要进行任何额外配置。 如果 VNet 连接到本地网络，则 ASE 中的应用也可访问此处的资源，不需其他配置。

![外部 ASE][1] 

如果拥有外部 ASE，则公共 VIP 还是 ASE 应用针对下述项解析到的终结点：

* HTTP/S 
* FTP/S
* Web 部署
* 远程调试

![ILB ASE][2]

如果你有一个 ILB ASE，则 ILB 的地址是用于 HTTP/S、FTP/S、Web 部署和远程调试的终结点。

## <a name="ase-subnet-size"></a>ASE 子网大小 ##

用于托管 ASE 的子网的大小在部署 ASE 后不能更改。  ASE 使用每个基础结构角色以及每个独立应用服务计划实例的地址。  此外，还有创建的每个子网的 Azure 网络使用的 5 个地址。  在创建应用前，根本不带应用服务计划的 ASE 将使用 12 个地址。  如果它是 ILB ASE，则在该 ASE 中创建应用前将使用 13 个地址。 横向扩展 ASE 时，基础结构角色按应用服务计划实例的 15 和 20 的倍数添加。

   > [!NOTE]
   > 子网中仅可具有 ASE。 请务必选择一个容许将来扩展的地址空间。 以后无法更改此设置。 建议使用包含 256 个地址的大小 `/24`。

纵向扩展或缩减时，将会添加具有相应大小的新角色，然后将工作负荷从当前大小迁移到目标大小。 只有迁移工作负荷后，才会删除原始 VM。 如果你的 ASE 有 100 个 ASP 实例，则在一段时间你需要双倍数目的 VM。  出于这个原因，建议使用“/24”来应对可能需要进行的任何更改。  

## <a name="ase-dependencies"></a>ASE 依赖项 ##

### <a name="ase-inbound-dependencies"></a>ASE 入站依赖项 ###

仅仅是为了让 ASE 保持正常运行，ASE 就需要打开以下端口：

| 用途 | 从 | 如果 |
|-----|------|----|
| 管理 | 应用服务管理地址 | ASE 子网：454、455 |
|  ASE 内部通信 | ASE 子网：所有端口 | ASE 子网：所有端口
|  允许 Azure 负载均衡器入站流量 | Azure 负载均衡器 | ASE 子网：16001

执行端口扫描时，还有其他 2 个端口可能显示为打开状态：7654 和 1221。 它们的回复中包含 IP 地址，此外不会包含任何其他信息。 可按需阻止这些端口。 

除系统监视以外，入站管理流量还提供对 ASE 的指挥与控制。 [ASE 管理地址][ASEManagement]文档中列出了此流量的源地址。 网络安全配置需要允许从端口 454 和 455 上的 ASE 管理地址进行访问。 如果阻止从这些地址进行访问，则 ASE 会变得不正常，然后变成暂停状态。 从端口 454 和 455 进来的 TCP 流量必须从同一 VIP 回去，否则会出现非对称路由问题。 

在 ASE 子网内，有多个用于内部组件通信的端口，并且可以更改。 这要求 ASE 子网中的所有端口均可从 ASE 子网访问。 

对于 Azure 负载均衡器和 ASE 子网间的通信，需要开放的最小端口是 454、455 和 16001。 端口 16001 用于使负载均衡器和 ASE 之间的流量保持活动状态。 如果使用 ILB ASE，则可将流量锁定为仅 454、455 和 16001 端口。  如果使用外部 ASE，则需要考虑常规的应用访问端口。  

需要自行考虑的其他端口是应用程序端口：

| 用途 | 端口 |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio 远程调试  |  4020, 4022, 4024 |
|  Web 部署服务 | 8172 |

如果阻止应用程序端口，ASE 仍可正常工作，但应用可能无法正常运行。  如果对外部 ASE 使用应用分配的 IP 地址，则需要允许分配给应用的 IP 发出的流量进入 ASE 门户上的“IP 地址”页中显示的端口上的 ASE 子网。

### <a name="ase-outbound-dependencies"></a>ASE 出站依赖项 ###

对于出站访问，ASE 依赖于多个外部系统。 在这些系统依赖项中，许多是使用 DNS 名称定义的，不会映射到一组固定的 IP 地址。 因此，ASE 需要从 ASE 子网跨各种端口对所有外部 IP 进行出站访问。 

ASE 在以下端口上与可通过 Internet 访问的地址通信：

| 使用 | 端口 |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL，Windows 更新，Linux 依赖项，Azure 服务 | 80/443 |
| Azure SQL | 1433 | 
| 监视 | 12000 |

出站依赖项已在介绍如何[锁定应用服务环境出站流量](./firewall-integration.md)的文档中列出。 如果失去其依赖项的访问权限，ASE 就会停止工作。 如果长时间发生此情况，将暂停 ASE。 

### <a name="customer-dns"></a>客户 DNS ###

如果在 VNet 中配置了客户定义的 DNS 服务器，则租户工作负荷将使用该服务器。 ASE 使用 Azure DNS 进行管理。 如果在 VNet 中配置了客户所选的 DNS 服务器，则必须可从包含 ASE 的子网访问 DNS 服务器。

若要从 Web 应用测试 DNS 解析，可以使用控制台命令 *nameresolver*。 转到应用的 scm 站点的调试窗口，或者在门户中转到应用，然后选择控制台。 在 shell 提示符下，可以结合要查找的 DNS 名称发出命令 *nameresolver*。 你取回的结果与应用进行同一查找时获取的结果相同。 如果使用 nslookup，则会改用 Azure DNS 进行查找。

如果更改 ASE 所在的 VNet 的 DNS 设置，则需重启 ASE。 为了避免重启 ASE，强烈建议在创建 ASE 之前配置 VNet 的 DNS 设置。  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>门户依赖项 ##

除了 ASE 功能依赖项，还有其他几项与门户体验相关。 Azure 门户中的某些功能依赖于对 _SCM 站点_ 的直接访问。 Azure 应用服务中的每个应用都有两个 URL。 第一个 URL 用于访问你的应用。 第二个 URL 用于访问 SCM 站点（也称为 _Kudu 控制台_）。 使用 SCM 站点的功能包括：

-   Web 作业
-   函数
-   日志流式处理
-   Kudu
-   扩展
-   进程资源管理器
-   控制台

使用 ILB ASE 时，无法从 VNet 外部访问 SCM 站点。 某些功能无法从应用门户运行，因为它们需要访问应用的 SCM 站点。 可以直接连接到 SCM 站点，而不使用门户。 

如果 ILB ASE 是域名 contoso.appserviceenvironment.net，应用名称是 testapp，则将在 testapp.contoso.appserviceenvironment.net 中访问此应用。   通过 testapp.scm.contoso.appserviceenvironment.net 访问其随附的 SCM 站点。

## <a name="ase-ip-addresses"></a>ASE IP 地址 ##

ASE 具有一些需要注意的 IP 地址。 它们具有以下特点：

- **公共入站 IP 地址**：用于外部 ASE 中的应用流量，以及外部 ASE 和 ILB ASE 中的管理流量。
- **出站公共 IP**：用作 ASE 发出、离开 VNet 且不经过 VPN 的出站连接的“来源”IP。
- **ILB IP 地址**：ILB IP 地址仅在 ILB ASE 中存在。
- 应用分配的基于 IP 的 TLS/SSL 地址：只能在外部 ASE 上使用，并且仅当配置了基于 IP 的 TLS/SSL 绑定时才可使用。

所有这些 IP 地址会显示在 Azure 门户上的 ASE UI 中。 若使用 ILB ASE，将列出 ILB 的 IP。

   > [!NOTE]
   > 只要 ASE 处于启动和运行状态，这些 IP 地址就不会更改。  如果 ASE 变成暂停和还原状态，ASE 所使用的地址就会更改。 ASE 变成暂停状态的通常原因是阻止了入站管理访问或阻止了对 ASE 依赖项的访问。 

![IP 地址][3]

### <a name="app-assigned-ip-addresses"></a>应用分配的 IP 地址 ###

使用外部 ASE 时，可将 IP 地址分配到各个应用。 无法使用 ILB ASE 实现这一点。 若要详细了解如何将应用配置为具有自己的 IP 地址，请参阅[在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](../configure-ssl-bindings.md)。

当应用使用其自身的基于 IP 的 SSL 地址时，ASE 将保留两个映射到该 IP 地址的端口。 它们分别用于 HTTP 流量和 HTTPS 流量。 这些端口列在 ASE UI 上的“ IP 地址”部分中。 流量必须能够从 VIP 抵达这些端口，否则无法访问应用。 配置网络安全组 (NSG) 时，请务必牢记此要求。

## <a name="network-security-groups"></a>网络安全组 ##

[网络安全组][NSGs]可用于控制 VNet 中的网络访问。 使用门户时，有一个最低优先级的隐式拒绝规则可拒绝任何流量。 因此，只需生成允许规则。

在 ASE 中，你无权访问用于托管 ASE 本身的 VM。 它们位于 Microsoft 管理订阅中。 若要限制对 ASE 上的应用的访问，请在 ASE 子网中设置 NSG。 操作时，需特别注意 ASE 依赖项。 如果阻止任何依赖项，ASE 将停止工作。

可通过 Azure 门户或 PowerShell 配置 NSG。 此处仅介绍了 Azure 门户中的操作。 在门户中的“网络”下面创建和管理 NSG 顶级资源。 

要使 ASE 正常运行，必须在 NSG 中添加允许流量的条目：

**入站**
* 来自端口 454、455 上 IP 服务标记 AppServiceManagement 的 TCP 流量
* 来自端口 16001 上负载均衡器的 TCP 流量
* 在所有端口上允许不同 ASE 子网之间发送的流量

**Outbound**
* 发往端口 53 上所有 IP 的 UDP 流量
* 发往端口 123 上所有 IP 的 UDP 流量
* 发往端口 80、443 上所有 IP 的 TCP 流量
* 端口 1433 上 IP 服务标记 `Sql` 的 TCP
* 发往端口 12000 上所有 IP 的 TCP 流量
* 在所有端口上允许发往 ASE 子网的流量

这些端口不包括成功使用应用所需的端口。 例如，应用可能需要在端口 3306 上调用 MySQL 服务器。 端口 123 上的网络时间协议 (NTP) 是操作系统使用的时间同步协议。 NTP 终结点不特定于应用服务，可因操作系统而异，并且不是定义完善的地址列表。 若要防止时间同步问题，则需要允许 UDP 流量发送到端口 123 上的所有地址。 端口 12000 的出站 TCP 流量用于系统支持和分析。 终结点是动态的，并且不是一组定义完善的地址。

常规应用访问端口为：

| 使用 | 端口 |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio 远程调试  |  4020, 4022, 4024 |
|  Web 部署服务 | 8172 |

若要考虑到入站和出站要求，NSG 应与本例中所示的 NSG 相似。 

![入站安全规则][4]

默认规则允许 VNet 中的 IP 与 ASE 子网对话。 另一条默认规则允许负载均衡器（亦称为公共 VIP）与 ASE 通信。 选择“添加”图标旁边的“默认规则”即可查看此规则。 如果在默认规则的前面放置一条拒绝其他任何流量的规则，则会阻止 VIP 与 ASE 之间的流量。 要阻止来自 Vnet 内部的流量，请自行添加规则以允许入站。 使用等效于 AzureLoadBalancer 的源，其目标为“任何”，端口范围为 \*。 由于 ASE 子网将应用 NSG 规则，因此无需指定具体的目标。

若向应用分配了 IP 地址，请确保端口保持打开。 可在“应用服务环境” > “IP 地址”中查看端口。   

下列出站规则中显示的所有项均是必需项，最后一项除外。 使用这些端口可以通过网络访问本文前面所述的 ASE 依赖项。 阻止其中的任意一个，ASE 都将停止工作。 列表中的最后一项可让 ASE 与 VNet 中的其他资源通信。

![入站安全规则][5]

定义 NSG 之后，请将其分配到 ASE 所在的子网。 如果你不记得 ASE VNet 或子网，可以通过 ASE 门户页查看。 若要将 NSG 分配到子网，请转到子网 UI 并选择该 NSG。

## <a name="routes"></a>路由 ##

强制隧道是指，在 VNet 中设置路由时，使出站流量不直接前往 Internet，而是前往诸如 ExpressRoute 网关或虚拟设备的其他位置。  如果需要以这样的方式配置 ASE，请阅读有关[为应用服务环境配置强制隧道][forcedtunnel]的文档。  该文档将介绍可用于 ExpressRoute 和强制隧道的选项。

在门户中创建 ASE 时，我们还在随 ASE 创建的子网上创建一组路由表。  这些路由只是指示将出站流量直接发送到 Internet。  
若要手动创建同样的路由，请执行以下步骤：

1. 转到 Azure 门户。 选择“网络” > “路由表”。 

2. 在 Vnet 所在的位置新建一个路由表。

3. 在路由表 UI 中选择“路由” > “添加”。 

4. 将“下一跃点类型”设置为 Internet，将“地址前缀”设置为 0.0.0.0/0。 选择“保存” 。

    然后将看到如下内容：

    ![功能路由][6]

5. 创建新路由表后，请转到包含 ASE 的子网。 从门户中的列表处选择路由表。 保存更改后，会看到子网旁显示有 NSG 和路由。

    ![NSG 和路由][7]

## <a name="service-endpoints"></a>服务终结点 ##

可以通过服务终结点将多租户服务的访问权限限制给一组 Azure 虚拟网络和子网。 若要详细了解服务终结点，请参阅[虚拟网络服务终结点][serviceendpoints]文档。 

在资源上启用服务终结点时，有些已创建路由的优先级高于所有其他路由。 如果在包含强制隧道 ASE 的任意 Azure 服务中使用服务终结点，则不会对发往这些服务的流量应用强制隧道。 

在包含 Azure SQL 实例的子网上启用服务终结点时，所有与该子网有连接的 Azure SQL 实例必定会启用服务终结点。 如果需要从同一子网访问多个 Azure SQL 实例，则不能在一个 Azure SQL 实例上启用服务终结点，而在另一个实例上不启用。 对于服务终结点，任何其他 Azure 服务的行为都不类似于 Azure SQL。 对 Azure 存储启用服务终结点时，可以锁定从子网对该资源进行的访问，但仍可访问其他 Azure 存储帐户，即使这些帐户未启用服务终结点。  

![服务终结点][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
