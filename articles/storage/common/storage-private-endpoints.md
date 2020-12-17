---
title: 使用专用终结点
titleSuffix: Azure Storage
description: 从虚拟网络安全访问存储帐户的专用终结点概述。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7af2e6794d0d2f37c342a86b2f36b94c9601cc7e
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617249"
---
# <a name="use-private-endpoints-for-azure-storage"></a>为 Azure 存储使用专用终结点

你可以使用 Azure 存储帐户的 [专用终结点](../../private-link/private-endpoint-overview.md) ，以允许虚拟网络上的客户端 (VNet) 通过 [专用链接](../../private-link/private-link-overview.md)安全地访问数据。 专用终结点为你的存储帐户服务使用 VNET 地址空间中的 IP 地址。 VNet 上的客户端与存储帐户之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行传输，避免暴露给公共 Internet。

通过为存储帐户使用专用终结点，你可以：

- 通过将存储防火墙配置为阻止存储服务的公共终结点上的所有连接，来保护存储账户。
- 阻止数据从 VNet 渗出，从而提高虚拟网络 (VNet) 的安全性。
- 从本地网络安全地连接到存储帐户，这些帐户使用带有专用对等的 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoutes](../../expressroute/expressroute-locations.md) 连接到 VNet。

## <a name="conceptual-overview"></a>概念概述

![Azure 存储的专用终结点概述](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

专用终结点是用于[虚拟网络](../../virtual-network/virtual-networks-overview.md) (VNet) 中的 Azure 服务的特殊网络接口。 为存储帐户创建专用终结点时，它将在 VNet 上的客户端和存储之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与存储服务之间的连接使用安全的专用链接。

VNet 中的应用程序可以使用通过其他方式连接时所用的相同连接字符串和授权机制，通过专用终结点无缝地连接到存储服务。 专用终结点可以与存储帐户支持的所有协议（包括 REST 和 SMB）一起使用。

可以在使用[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)的子网中创建专用终结点。 因此，子网中的客户端可以使用专用终结点连接到某个存储帐户，同时使用服务终结点访问其他存储帐户。

在 VNet 中创建用于存储服务的专用终结点时，会将一个申请批准的许可请求发送到存储帐户所有者。 如果请求创建专用终结点的用户还是存储帐户的所有者，则此许可请求会自动获得批准。

存储帐户所有者可以通过 [Azure 门户](https://portal.azure.com)中存储帐户的“专用终结点”选项卡来管理许可请求和专用终结点。

> [!TIP]
> 如果要仅通过专用终结点限制对存储帐户的访问，请将存储防火墙配置为拒绝或控制通过公用终结点进行的访问。

通过[配置存储防火墙](storage-network-security.md#change-the-default-network-access-rule)，使其默认拒绝通过其公共终结点进行的访问，可以保护存储帐户，使其仅接受来自 VNet 的连接。 无需防火墙规则来允许来自具有专用终结点的 VNet 的流量，因为存储防火墙只控制通过公共终结点进行的访问。 专用终结点则是依赖于“同意流”来授予子网对存储服务的访问权限。

> [!NOTE]
> 在存储帐户之间复制 blob 时，您的客户端必须具有对这两个帐户的网络访问权限。 因此，如果您选择只为一个帐户使用一个专用链接 (源或目标) ，请确保您的客户端具有对其他帐户的网络访问权限。 若要了解配置网络访问的其他方式，请参阅 [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md?toc=/azure/storage/blobs/toc.json)。 

### <a name="private-endpoints-for-azure-storage"></a>Azure 存储的专用终结点

创建专用终结点时，需要指定存储帐户及其连接到的存储服务。 需要为需要访问的存储帐户中的每个存储服务提供单独的专用终结点，即 [Blob](../blobs/storage-blobs-overview.md)、[Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)、[文件存储](../files/storage-files-introduction.md)、[队列](../queues/storage-queues-introduction.md)、[表](../tables/table-storage-overview.md)或[静态网站](../blobs/storage-blob-static-website.md)。

> [!TIP]
> 为存储服务的辅助实例创建单独的专用终结点，以便在 RA-GRS 帐户上获得更好的读取性能。
> 请确保创建常规用途 v2 (标准版或高级版) 存储帐户。

若要使用为异地冗余存储配置的存储帐户对次要区域进行读取访问，需要为服务的主实例和辅助实例使用单独的专用终结点。 无需为“故障转移”的辅助实例创建专用终结点。 专用终结点将在故障转移后自动连接到新的主实例。 有关存储冗余选项的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

有关为你的存储帐户创建专用终结点的详细信息，请参阅以下文章：

- [从 Azure 门户中的存储帐户体验私下连接到存储帐户](../../private-link/tutorial-private-endpoint-storage-portal.md)
- [使用 Azure 门户中的“专用链接中心”创建专用终结点](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 创建专用终结点](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 创建专用终结点](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

使用专用终结点的 VNet 上的客户端应该为存储帐户使用与连接到公共终结点的客户端相同的连接字符串。 我们依赖于 DNS 解析，通过专用链接自动将连接从 VNet 路由到存储帐户。

> [!IMPORTANT]
> 使用相同的连接字符串通过专用终结点连接到存储帐户，就像在其他情况下使用那样。 请不要使用存储帐户的“privatelink”子域 URL 连接到该帐户。

默认情况下，我们会创建一个附加到 VNet 的[专用 DNS 区域](../../dns/private-dns-overview.md)，并带有专用终结点的必要更新。 但是，如果使用自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。 下面关于 [DNS 更改](#dns-changes-for-private-endpoints)的部分描述了专用终结点所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，存储帐户的 DNS CNAME 资源记录将更新为具有前缀 privatelink 的子域中的别名。 默认情况下，我们还会创建一个与 privatelink 子域对应的[专用 DNS 区域](../../dns/private-dns-overview.md)，其中包含专用终结点的 DNS A 资源记录。

使用专用终结点从 VNet 外部解析存储终结点 URL 时，它会解析为存储服务的公共终结点。 从托管专用终结点的 VNet 进行解析时，存储终结点 URL 解析为专用终结点的 IP 地址。

对于上面所示的示例，存储帐户“StorageAccountA”的 DNS 资源记录在从托管专用终结点的 VNet 外部解析时将为：

| 名称                                                  | 类型  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

如前文所述，可以使用存储防火墙通过公共终结点拒绝或控制 VNet 外部的客户端的访问。

当 StorageAccountA 的 DNS 资源记录由托管专用终结点的 VNet 中的客户端解析时，将为：

| 名称                                                  | 类型  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

对于托管专用终结点的 VNet 上的客户端和 VNet 外部的客户端，此方法允许使用相同的连接字符串访问存储帐户。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将存储帐户终结点的 FQDN 解析为专用终结点 IP 地址。 应将 DNS 服务器配置为将专用链接子域委托给 VNet 的专用 DNS 区域，或使用专用终结点 IP 地址配置 "*StorageAccountA.privatelink.blob.core.windows.net*" 的 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应将 DNS 服务器配置为将 privatelink 子域中的存储帐户名称解析为专用终结点 IP 地址。 为此，可以将 privatelink 子域委托给 VNet 的专用 DNS 区域，或在 DNS 服务器上配置 DNS 区域并添加 DNS A 记录。

存储服务的专用终结点的建议 DNS 区域名称为：

| 存储服务        | 区域名称                            |
| :--------------------- | :----------------------------------- |
| Blob 服务           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 文件服务           | `privatelink.file.core.windows.net`  |
| 队列服务          | `privatelink.queue.core.windows.net` |
| 表服务          | `privatelink.table.core.windows.net` |
| 静态网站        | `privatelink.web.core.windows.net`   |

有关配置自己的 DNS 服务器以支持专用终结点的详细信息，请参阅以下文章：

- [Azure 虚拟网络中资源的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知问题

请记住以下关于 Azure 存储专用终结点的已知问题。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>针对专用终结点所在 VNet 中的客户端的存储访问约束

具有现有专用终结点的 VNet 中的客户端在访问具有专用终结点的其他存储帐户时面临约束。 例如，假设 VNet N1 有一个用于 Blob 存储的存储帐户 A1 的专用终结点。 如果存储帐户 A2 在 VNet N2 中有一个用于 Blob 存储的专用终结点，则 VNet N1 中的客户端也需要使用专用终结点访问帐户 A2 中的 Blob 存储。 如果存储帐户 A2 没有任何用于 Blob 存储的专用终结点，则 VNet N1 中的客户端可以在没有专用终结点的情况下访问该帐户中的 Blob 存储。

此约束是帐户 A2 创建专用终结点时 DNS 发生更改的结果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>专用终结点所在子网的网络安全组规则

目前，你不能为专用终结点配置[网络安全组](../../virtual-network/network-security-groups-overview.md) (NSG) 规则和用户定义的路由。 应用于托管专用终结点的子网的 NSG 规则仅适用于专用终结点以外的其他终结点（例如 NIC）。 解决此问题的一个有限的解决方法是在源子网上实现专用终结点的访问规则，但这种方法可能需要更高的管理开销。

## <a name="next-steps"></a>后续步骤

- [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md)
- [有关 Blob 存储的安全性建议](../blobs/security-recommendations.md)
