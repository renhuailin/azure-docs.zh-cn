---
title: 网络概述 - Azure Database for MySQL 灵活服务器
description: 了解 Azure Database for MySQL 的灵活服务器部署选项中的连接和网络选项
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 44b13211bd8a7f3b71f1357db0097500801c2a0c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724294"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器（预览版）的连接和网络概念

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

本文介绍了如何控制与 Azure MySQL 灵活服务器连接的概念。 你需要详细了解 Azure Database for MySQL 灵活服务器的网络概念，以便在 Azure 中安全地创建和访问服务器。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器目前处于预览版阶段。

Azure Database for MySQL 灵活服务器支持通过以下两种方式配置与服务器的连接：
> [!NOTE]
> 创建服务器后，网络选择无法更改。

   * 专用访问（VNet 集成）- [专用访问](./concepts-networking-vnet.md)：你可以将灵活服务器部署到 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)。 Azure 虚拟网络提供专用的安全网络通信。 虚拟网络中的各个资源可通过专用 IP 地址进行通信。
   
   * 公共访问（允许的 IP 地址）- [公共访问](./concepts-networking-public.md)：你可以通过公共终结点访问灵活服务器。 公共终结点是可公开解析的 DNS 地址。 “允许的 IP 地址”一词是指你选择向其授予访问服务器的权限的一系列 IP。 这些权限称为“防火墙规则”

## <a name="choosing-a-networking-option"></a>选择网络选项

如需以下功能，请选择“专用访问（VNet 集成）”：
   * 从同一个虚拟网络或[对等互连虚拟网络](../../virtual-network/virtual-network-peering-overview.md)中的 Azure 资源连接到灵活服务器
   * 使用 VPN 或 ExpressRoute 从非 Azure 资源连接到灵活服务器
   * 无公共终结点

如需以下功能，请选择“公共访问（允许的 IP 地址）”方法：
   * 从不支持虚拟网络的 Azure 资源进行连接
   * 从 Azure 外部不通过 VPN 或 ExpressRoute 连接的资源进行连接 
   * 灵活服务器具有公共终结点

无论你是选择使用专用访问选项还是公共访问选项，以下特征都适用：
* 来自允许的 IP 地址的连接需要使用有效的凭据向 MySQL 服务器进行身份验证
* [连接加密](#tls-and-ssl)可用于你的网络流量
* 服务器具有完全限定的域名 (FQDN)。 对于连接字符串中的 hostname 属性，建议使用 FQDN 而不是 IP 地址。
* 这两个选项都是在服务器级别控制访问，而不是在数据库或表级别进行控制。 你将使用 MySQL 的角色属性来控制数据库、表和其他对象访问。


### <a name="unsupported-virtual-network-scenarios"></a>不受支持的虚拟网络场景

* 公共终结点（或公共 IP 或 DNS）- 部署到虚拟网络的灵活服务器不能有公共终结点
* 将灵活服务器部署到虚拟网络和子网后，不能将它移动到其他虚拟网络或子网。 * 部署灵活的服务器后，你不能将灵活服务器使用的虚拟网络移到另一个资源组或订阅中。
* 一旦子网中存在资源，子网大小（地址空间）便不能增加
* 创建服务器后，禁止从公共访问更改为专用访问。 建议使用时间点还原

了解如何通过 [Azure 门户](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 启用专用访问（VNet 集成）。

> [!NOTE]
> 如果使用自定义 DNS 服务器，则必须使用 DNS 转发器解析 Azure Database for MySQL 灵活服务器的 FQDN。 有关详细信息，请参阅[使用你自己的 DNS 服务器的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

## <a name="hostname"></a>主机名
无论你选择哪个网络选项，我们都建议你在连接到灵活服务器时始终使用连接字符串中的完全限定的域名 (FQDN)：`<servername>.mysql.database.azure.com`。 

## <a name="tls-and-ssl"></a>TLS 和 SSL
Azure Database for MySQL 灵活服务器支持使用安全套接字层 (SSL) 和传输层安全性 (TLS) 加密将客户端应用程序连接到 MySQL 服务器。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现加密的网络连接，使你能够满足合规性要求。

默认情况下，Azure Database for MySQL 灵活服务器支持使用传输层安全性 (TLS 1.2) 的加密连接，并且会拒绝所有使用 TLS 1.0 和 TLS 1.1 的传入连接。 可以配置和更改灵活服务器上的加密连接强制要求或 TLS 版本配置。 

下面是适用于灵活服务器的 SSL 和 TLS 设置的不同配置：

| 方案   | 服务器参数设置      | 说明                                    |
|------------|--------------------------------|------------------------------------------------|
|禁用 SSL（加密连接） | require_secure_transport 设置为 OFF |如果旧版应用程序不支持与 MySQL 服务器建立加密连接，则可以通过将 require_secure_transport 设置为 OFF，来禁用与灵活服务器建立加密连接的强制要求。|
|强制使用 SSL 和低于 TLS 版本 1.2 的版本 | require_secure_transport 设置为 ON，且 tls_version 设置为 TLSV1 或 TLSV1.1| 如果旧版应用程序支持加密连接，但需要低于 TLS 版本 1.2 的版本，你可以启用加密连接，但将灵活服务器配置为允许连接使用应用程序支持的 TLS 版本（v1.0 或 v1.1）|
|强制使用 SSL 和 TLS 版本 1.2（默认配置）|require_secure_transport 设置为 ON，且 tls_version 设置为 TLSV1.2| 这是为灵活服务器建议的配置，同时也是默认配置。|
|强制使用 SSL 和 TLS 版本 1.3（受 MySQL v8.0 和更高版本支持）| require_secure_transport 设置为 ON，且 tls_version 设置为 TLSV1.3| 这是为新应用程序开发建议的有用配置|

> [!Note]
> 不支持对灵活服务器上的 SSL 密码进行更改。 将 tls_version 设置为 TLS 版本 1.2 时，默认强制使用 FIPS 密码套件。 对于版本 1.2 以外的其他 TLS 版本，SSL 密码将设为 MySQL 社区安装附带的默认设置。

请参阅[使用 SSL/TLS 进行连接](how-to-connect-tls-ssl.md)了解详细信息。 


## <a name="next-steps"></a>后续步骤
* 了解如何使用 [Azure 门户](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 启用专用访问（VNet 集成）
* 了解如何使用 [Azure 门户](how-to-manage-firewall-portal.md)或 [Azure CLI](how-to-manage-firewall-cli.md) 启用公共访问（允许的 IP 地址）
* 了解如何[使用 TLS](how-to-connect-tls-ssl.md)
