---
title: 公用网络访问概述 - Azure Database for MySQL 灵活服务器
description: 了解 Azure Database for MySQL 灵活服务器部署选项中的公用网络访问选项
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: 414b2f8d6e64112c737fe220003ca2c58b3e9d7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779682"
---
# <a name="public-network-access-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器的公用网络访问（预览版）

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

本文介绍服务器的公共连接选项。 你将详细了解创建可安全通过 Internet 访问的 Azure Database for MySQL 灵活服务器的概念。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器目前处于预览版阶段。

## <a name="public-access-allowed-ip-addresses"></a>公共访问（允许的 IP 地址）

在灵活服务器上配置公共访问将允许通过公共终结点访问该服务器，即可通过 Internet 访问该服务器。 公共终结点是可公开解析的 DNS 地址。 “允许的 IP 地址”一词是指你选择向其授予访问服务器的权限的一系列 IP。 这些权限称为“防火墙规则”。

公共访问方法的特征包括：

* 只有你允许的 IP 地址才有权访问 MySQL 灵活服务器。 默认情况下，不允许任何 IP 地址。 可以在服务器创建期间或之后添加 IP 地址。
* MySQL 服务器具有可公开解析的 DNS 名称
* 灵活服务器不在 Azure 虚拟网络之一中
* 服务器之间的网络流量不会通过专用网络传输。 该流量使用一般的 Internet 路径。

### <a name="firewall-rules"></a>防火墙规则

向 IP 地址授予权限称为防火墙规则。 如果连接尝试来自你不允许的 IP 地址，则发起客户端会看到错误。

### <a name="allowing-all-azure-ip-addresses"></a>允许所有 Azure IP 地址

如果某个固定的传出 IP 地址不适用于 Azure 服务，可以考虑启用来自所有 Azure 数据中心 IP 地址的连接。

> [!IMPORTANT]
> “允许来自 Azure 服务和 Azure 中资源的公共访问”选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。

了解如何通过 [Azure 门户](how-to-manage-firewall-portal.md)或 [Azure CLI](how-to-manage-firewall-cli.md) 启用和管理公共访问（允许的 IP 地址）。

### <a name="troubleshooting-public-access-issues"></a>排查公共访问问题

在对 Microsoft Azure Database for MySQL 服务器服务的访问与预期不符时，请考虑以下几点：

* 对允许列表的更改尚未生效：对 Azure Database for MySQL 服务器防火墙配置所做的更改生效之前，可能会有长达 5 分钟的延迟。

* 身份验证失败：如果某个用户对 Azure Database for MySQL 服务器没有权限或者使用的密码不正确，则与 Azure Database for MySQL 服务器的连接会被拒绝。 创建防火墙设置只是为客户端提供尝试连接到服务器的机会。 每个客户端必须提供必需的安全凭据。

* 动态客户端 IP 地址：如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

  * 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机（用于访问 Azure Database for MySQL 服务器）的 IP 地址范围，然后将该 IP 地址范围作为防火墙规则添加。
  * 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。
  
* 防火墙规则不适用于 IPv6 格式：防火墙规则必须采用 IPv4 格式。 如果以 IPv6 格式指定防火墙规则，则会显示验证错误。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure 门户](how-to-manage-firewall-portal.md)或 [Azure CLI](how-to-manage-firewall-cli.md) 启用公共访问（允许的 IP 地址）
* 了解如何[使用 TLS](how-to-connect-tls-ssl.md)
