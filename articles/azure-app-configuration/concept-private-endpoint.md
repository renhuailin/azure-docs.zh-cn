---
title: 为 Azure 应用配置使用专用终结点
description: 使用专用终结点保护你的应用配置存储
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: ded1007cd5d0268d68bcdbff87a3b703da247876
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108764038"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>为 Azure 应用配置使用专用终结点

你可以为 Azure 应用配置使用[专用终结点](../private-link/private-endpoint-overview.md)，以允许虚拟网络 (VNet) 上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问数据。 专用终结点为你的应用配置存储使用 VNET 地址空间中的 IP 地址。 VNet 上的客户端与应用配置存储之间的网络流量使用 Microsoft 主干网络上的专用链接穿过 VNet，避免暴露给公共 Internet。

为应用配置存储使用专用终结点可以实现以下目的：
- 通过将防火墙配置为阻止公共终结点上到应用配置的所有连接来保护你的应用程序配置详细信息。
- 提高虚拟网络 (VNet) 的安全性，确保数据不会离开 VNet。
- 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoutes](../expressroute/expressroute-locations.md) 通过专用对等互连从连接到 VNet 的本地网络安全地连接到应用配置存储。

## <a name="conceptual-overview"></a>概念概述

专用终结点是用于[虚拟网络](../virtual-network/virtual-networks-overview.md) (VNet) 中的 Azure 服务的特殊网络接口。 为应用配置存储创建专用终结点时，它会在 VNet 上的客户端与配置存储之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与配置存储之间的连接使用安全的专用链接。

VNet 中的应用程序可以 **使用通过其他方式连接时所用的相同连接字符串和授权机制** 通过专用终结点连接到配置存储。 专用终结点可以与应用配置存储支持的所有协议一起使用。

虽然应用配置不支持服务终结点，但你可以在使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的子网中创建专用终结点。 子网中的客户端可以使用专用终结点安全地连接到应用配置存储，而使用服务终结点来访问其他位置。  

在 VNet 中创建用于服务的专用终结点时，会将一个申请批准的许可请求发送到服务帐户所有者。 如果请求创建专用终结点的用户还是帐户的所有者，则此许可请求会自动获得批准。

服务帐户所有者可以通过 [Azure 门户](https://portal.azure.com)中的应用配置存储的“`Private Endpoints`”选项卡来管理同意请求和专用终结点。

### <a name="private-endpoints-for-app-configuration"></a>用于应用配置的专用终结点 

创建专用终结点时，必须指定它连接到的应用配置存储。 如果有多个应用配置存储，则每个存储都需要一个单独的专用终结点。

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

Azure 依赖于 DNS 解析通过专用链接对从 VNet 到配置存储的连接进行路由。 在 Azure 门户中，可以通过依次选择应用配置存储、“设置” > “访问密钥”来快速查找连接字符串。  

> [!IMPORTANT]
> 通过专用终结点连接到应用配置存储时所使用的连接字符串与通过公共终结点进行连接时所使用的连接字符串相同。 连接到存储时请勿使用其 `privatelink` 子域 URL。

> [!NOTE]
> 默认情况下，当专用终结点已添加到应用配置存储时，所有通过公用网络提出的对应用配置数据的请求都会被拒绝。 可以使用以下 Azure CLI 命令启用公用网络访问。 在这种情况下，请务必考虑启用公用网络访问的安全隐患。
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，配置存储的 DNS CNAME 资源记录将更新为具有前缀 `privatelink` 的子域中的别名。 Azure 还会创建一个与 `privatelink` 子域对应的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的 DNS A 资源记录。

从承载着专用终结点的 VNet 内解析终结点 URL 时，它会解析为存储的专用终结点。 从 VNet 外部解析时，终结点 URL 解析为公共终结点。 创建专用终结点时，会禁用公共终结点。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将服务终结点的完全限定的域名 (FQDN) 解析为专用终结点 IP 地址。 配置 DNS 服务器以将专用链接子域委托到 VNet 的专用 DNS 区域，或者使用专用终结点 IP 地址为 `[Your-store-name].privatelink.azconfig.io` 配置 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应将 DNS 服务器配置为将 `privatelink` 子域中的存储名称解析为专用终结点 IP 地址。 为此，可以将 `privatelink` 子域委托给 VNet 的专用 DNS 区域，或在 DNS 服务器上配置 DNS 区域并添加 DNS A 记录。

## <a name="pricing"></a>定价

启用专用终结点需要[标准层](https://azure.microsoft.com/pricing/details/app-configuration/)应用配置存储。  若要了解专用链接定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>后续步骤

若要详细了解如何为应用配置存储创建专用终结点，请参阅以下文章：

- [使用 Azure 门户中的“专用链接中心”创建专用终结点](../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 创建专用终结点](../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 创建专用终结点](../private-link/create-private-endpoint-powershell.md)

了解如何配置具有专用终结点的 DNS 服务器：

- [Azure 虚拟网络中资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](../private-link/private-endpoint-overview.md#dns-configuration)
