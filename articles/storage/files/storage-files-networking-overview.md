---
title: Azure 文件存储的网络注意事项 | Microsoft Docs
description: Azure 文件存储的网络选项概述。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8771c79aa788627fb73745e98e924bbaa3ab1236
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224672"
---
# <a name="azure-files-networking-considerations"></a>Azure 文件存储的网络注意事项 
可以通过两种方式连接到 Azure 文件共享：

- 直接通过服务器消息块 (SMB)、网络文件系统 (NFS)（预览版）或 FileREST 协议访问共享。 要删除尽可能多的本地服务器时，主要使用此访问模式。
- 使用 Azure 文件同步在本地服务器上（或在 Azure VM 上）创建 Azure 文件共享的缓存，并使用适合自己用例的所选协议（SMB、NFS、FTPS 等）从本地服务器访问文件共享的数据。 此访问模式非常方便，因为它结合了本地性能以及云缩放和无服务器可附加服务（例如，Azure 备份）的优势。

本文重点介绍如何在用例要求直接访问 Azure 文件共享而不是使用 Azure 文件同步时配置网络。要详细了解 Azure 文件同步部署的网络注意事项，请参阅[Azure 文件同步网络注意事项](../file-sync/file-sync-networking-overview.md)。

Azure 文件共享的网络配置是在 Azure 存储帐户中完成的。 存储帐户是代表共享存储池的管理结构，你可以在其中部署多个文件共享以及其他存储资源（例如，Blob 容器或队列）。 存储帐户公开多种设置用于帮助保护对文件共享的网络访问：网络终结点、存储帐户防火墙设置和传输中加密。 

> [!Important]  
> Azure 文件存储支持多个网络路由选项。 默认选项“Microsoft 路由”适用于所有 Azure 文件存储配置。 Internet 路由选项不支持 AD 域加入方案或 Azure 文件同步。

在阅读本概念指南之前，我们建议先阅读[规划 Azure 文件存储部署](storage-files-planning.md)。

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        此视频是有关如何通过五个简单步骤将 Azure 文件共享直接安全地公开给信息工作者和应用的指南和演示。 以下部分提供的链接和其他上下文适用于视频中引用的文档。
   :::column-end:::
:::row-end:::

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

## <a name="accessing-your-azure-file-shares"></a>访问 Azure 文件共享
SMB Azure 文件共享可立即通过存储帐户 SMB 3.1.1 和 SMB 3.0 的公共终结点访问。 这意味着，已经过身份验证的请求（例如已由用户登录标识授权的请求）可以安全地从 Azure 内部或外部发起。 如果公共终结点仅限于 Azure 虚拟网络，则只能通过存储帐户的公共终结点访问 NFS Azure 文件共享。

在许多环境中，你可能希望将其他网络配置应用于其 Azure 文件共享：

- 对于 SMB 文件共享，许多组织和 Internet 服务提供商 (ISP) 阻止 SMB 用来通信的端口 445。 这种做法源自于有关已弃用版和非 Internet 安全版本的 SMB 协议的旧安全指导原则。 尽管 SMB 3.x 是 Internet 安全协议，但组织或 ISP 策略可能无法更改。 

- 对于 NFS 文件共享，受限的公共终结点访问仅限于装载到 Azure 内部。

- 某些组织要求流量发送到 Azure，以遵循确定性路径。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>通过虚拟专用网络或 ExpressRoute 以隧道方式传输流量
在本地网络与 Azure 之间建立网络隧道时，会将本地网络对等互连到 Azure 中的一个或多个虚拟网络。 [虚拟网络](../../virtual-network/virtual-networks-overview.md) (VNet) 类似于在本地运行的传统网络。 与 Azure 存储帐户或 Azure VM 一样，VNet 是在资源组中部署的 Azure 资源。 

Azure 文件存储支持通过以下机制在本地工作站和服务器与 Azure SMB/NFS 文件共享之间以隧道方式传输流量：

- VPN 网关是特定类型的虚拟网关，用于通过 Internet 在 Azure 虚拟网络和备用位置（例如，本地）之间发送加密的流量。 Azure VPN 网关是一种 Azure 资源，可以与存储帐户或其他 Azure 资源一起部署在资源组中。 VPN 网关公开了两种不同类型的连接：
    - [点到站点 (P2S) VPN](../../vpn-gateway/point-to-site-about.md) 网关连接，这是 Azure 与单个客户端之间建立的 VPN 连接。 对于那些不属于组织的本地网络的设备（例如，希望能够在家中、咖啡店或酒店随时随地装载 Azure 文件共享的远程办公人员），此解决方案非常有用。 若要将 P2S VPN 连接与 Azure 文件存储一起使用，需要为每个要连接的客户端配置 P2S VPN 连接。 若要简化 P2S VPN 连接的部署，请参阅[在 Windows 上配置用于 Azure 文件存储的点到站点 (P2S) VPN](storage-files-configure-p2s-vpn-windows.md)，以及[在 Linux 上配置用于 Azure 文件存储的点到站点 (P2S) VPN](storage-files-configure-p2s-vpn-linux.md)。
    - [站点到站点 (S2S) VPN](../../vpn-gateway/design.md#s2smulti)，这是 Azure 与组织的网络之间建立的 VPN 连接。 通过 S2S VPN 连接，你可以为组织的网络上托管的 VPN 服务器或设备一次性配置 VPN 连接，而不是为需要访问 Azure 文件共享的每个客户端设备都进行一次配置。 若要简化 S2S VPN 连接的部署，请参阅[配置用于 Azure 文件存储的站点到站点 (S2S) VPN](storage-files-configure-s2s-vpn.md)。
- [ExpressRoute](../../expressroute/expressroute-introduction.md)，使你可以在 Azure 与不经过 Internet 的本地网络之间创建定义的路由。 因为 ExpressRoute 在本地数据中心和 Azure 之间提供了专用路径，所以当存在网络性能方面的顾虑时，ExpressRoute 可能会很有用。 组织的策略或法规要求使用确定的路径访问云中的资源时，ExpressRoute 也是一个不错的选择。

无论使用哪种隧道方法来访问 Azure 文件共享，都需要通过某种机制来确保发往存储帐户的流量经过隧道，而不是经过普通的 Internet 连接。 在技术上可以将流量路由到存储帐户的公共终结点，但这需要对区域中 Azure 存储群集的所有 IP 地址进行硬编码，因为存储帐户随时都可能在存储群集之间移动。 此外，还需要经常更新 IP 地址映射，因为新群集在不断地添加。

我们不建议在 VPN 路由规则中对存储帐户的 IP 地址进行硬编码，而是使用专用终结点，以便为存储帐户提供 Azure 虚拟网络地址空间中的某个 IP 地址。 由于与 Azure 建立隧道可在本地网络与一个或多个虚拟网络之间建立对等互连，因此可以持久实现正确的路由。

### <a name="private-endpoints"></a>专用终结点
除了存储帐户的默认公共终结点以外，Azure 文件存储还提供创建一个或多个专用终结点的选项。 专用终结点是只能在 Azure 虚拟网络内部访问的终结点。 为存储帐户创建专用终结点时，存储帐户将从虚拟网络地址空间中获取一个专用 IP 地址，这非常类似于本地文件服务器或 NAS 设备接收本地网络专用地址空间中的 IP 地址。 

单个专用终结点与特定的 Azure 虚拟网络子网相关联。 存储帐户可以在多个虚拟网络中具有专用终结点。

对 Azure 文件存储使用专用终结点可以：
- 结合专用对等互连使用 VPN 或 ExpressRoute 连接，从本地网络安全连接到 Azure 文件共享。
- 通过将存储帐户防火墙配置为阻止公共终结点上的所有连接，来保护 Azure 文件共享。 默认情况下，创建专用终结点不会阻止连接到公共终结点。
- 阻止数据从虚拟网络（和对等互连边界）渗出，从而提高虚拟网络的安全性。

若要创建专用终结点，请参阅[配置 Azure 文件存储的专用终结点](storage-files-networking-endpoints.md)。

### <a name="private-endpoints-and-dns"></a>专用终结点和 DNS
创建专用终结点时，默认还会创建（或更新现有的）对应于 `privatelink` 子域的专用 DNS 区域。 严格地讲，无需创建专用 DNS 区域即可使用存储帐户的专用终结点，但是，我们强烈建议在一般情况下创建此专用区域，并且明确要求在使用 Active Directory 用户主体装载 Azure 文件共享或者从 FileREST API 访问时要这样做。

> [!Note]  
> 本文将使用 Azure 公共区域的存储帐户 DNS 后缀 `core.windows.net`。 此段注释也适用于 Azure 主权云（例如 Azure 美国政府云和 Azure 中国云）- 只需根据环境替换相应的后缀即可。 

在专用 DNS 区域中，我们将为 `storageaccount.privatelink.file.core.windows.net` 创建 A 记录，并为存储帐户的常规名称创建遵循模式 `storageaccount.file.core.windows.net` 的 CNAME 记录。 由于 Azure 专用 DNS 区域将连接到包含专用终结点的虚拟网络，因此，可以通过在 Azure VM 上的 PowerShell 中调用 `Resolve-DnsName` cmdlet（或者在 Windows 和 Linux 中运行 `nslookup`）来观察 DNS 配置：

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

对于本示例，存储帐户 `storageaccount.file.core.windows.net` 解析为专用终结点的专用 IP 地址（正好是 `192.168.0.4`）。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

如果从本地运行同一命令，则会看到同一存储帐户名称解析为存储帐户的公共 IP 地址；`storageaccount.file.core.windows.net` 是 `storageaccount.privatelink.file.core.windows.net` 的 CNAME 记录，后者又是托管存储帐户的 Azure 存储群集的 CNAME 记录：

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

这反映了存储帐户可以同时公开公共终结点以及一个或多个专用终结点这一事实。 为了确保存储帐户名称解析为专用终结点的专用 IP 地址，必须更改本地 DNS 服务器上的配置。 可通过多种方式实现此目的：

- 修改客户端上的 hosts 文件，使 `storageaccount.file.core.windows.net` 解析为所需的专用终结点专用 IP 地址。 对于生产环境，强烈建议不要这样做，因为需要对装载 Azure 文件共享的每个客户端进行这些更改，而系统不会自动处理对存储帐户或专用终结点所做的更改。
- 在本地 DNS 服务器中为 `storageaccount.file.core.windows.net` 创建 A 记录。 这种方法的优势在于，本地环境中的客户端能够自动解析存储帐户，而无需配置每个客户端，但是，与修改 hosts 文件一样，此解决方法较不可靠，因为不会反映所做的更改。 尽管此解决方法不太可靠，但在某些环境中，它却是最佳的选择。
- 将 `core.windows.net` 区域从本地 DNS 服务器转发到 Azure 专用 DNS 区域。 可以通过特殊 IP 地址 (`168.63.129.16`) 访问 Azure 专用 DNS 主机，该 IP 地址只能在链接到 Azure 专用 DNS 区域的虚拟网络内部访问。 若要解决此限制，可以在虚拟网络中运行其他 DNS 服务器，以便将 `core.windows.net` 转发到 Azure 专用 DNS 区域。 为了简化此设置，我们提供了 PowerShell cmdlet 用于在 Azure 虚拟网络中自动部署 DNS 服务器并根据需要对其进行配置。 若要了解如何设置 DNS 转发，请参阅[配置 Azure 文件存储的 DNS](storage-files-networking-dns.md)。

## <a name="storage-account-firewall-settings"></a>存储帐户防火墙设置
防火墙是一种网络策略，控制允许哪些请求访问存储帐户的公共终结点。 使用存储帐户防火墙，可以仅限特定的 IP 地址、IP 范围或虚拟网络访问存储帐户的公共终结点。 通常，大多数针对存储帐户的防火墙策略仅限一个或多个虚拟网络进行网络访问。 

可通过两种方法来仅限虚拟网络访问存储帐户：
- 为存储帐户创建一个或多个专用终结点，并限制对公共终结点的所有访问。 这可以确保只有源自所需虚拟网络内部的流量才能访问存储帐户中的 Azure 文件共享。
- 仅限一个或多个虚拟网络访问公共终结点。 为此，可以使用称作“服务终结点”的虚拟网络功能。 通过服务终结点限制发往存储帐户的流量时，仍会通过公共 IP 地址访问存储帐户。

> [!NOTE]
> NFS 文件共享只能通过虚拟网络访问存储帐户的公共终结点。 NFS 共享可以随时访问存储帐户的专用终结点。

若要详细了解如何配置存储帐户防火墙，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="see-also"></a>另请参阅
- [Azure 文件存储概述](storage-files-introduction.md)
- [规划 Azure 文件部署](storage-files-planning.md)
