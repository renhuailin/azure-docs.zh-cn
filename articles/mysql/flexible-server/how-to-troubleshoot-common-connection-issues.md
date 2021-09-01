---
title: 排查连接问题 - Azure Database for MySQL - 灵活服务器
description: 了解如何排查 Azure Database for MySQL 灵活服务器的连接问题。
keywords: mysql 连接,连接字符串,连接问题,永久性错误,连接错误
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: d3ac694134ada7a5dcb5ef4226d9de7eb6492f56
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652421"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>排查 Azure Database for MySQL 灵活服务器的连接问题

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

连接问题可能由多种因素造成，包括：

* 防火墙设置
* 连接超时
* 不正确的登录信息
* 某些 Azure Database for MySQL 灵活服务器资源达到了最大限制

在本文中，我们将讨论如何排查一些常见错误，并提供解决这些错误的步骤。

## <a name="troubleshoot-common-errors"></a>解决常见问题

如果应用程序一直无法连接到 Azure Database for MySQL 灵活服务器，通常表示下列其中一项出现了问题：

* 使用 TLS/SSL 加密的连接：灵活服务器仅支持使用传输层安全性 (TLS 1.2) 的加密连接，并且所有使用 TLS 1.0 和 TLS 1.1 的传入连接默认都将遭拒。 你可以禁用加密连接的强制执行，或更改 TLS 版本。 详细了解 [Azure Database for MySQL 灵活服务器中使用传输层安全性 (TLS 1.2) 的加密连接](./how-to-connect-tls-ssl.md)。
- 专用访问（VNet 集成）中的灵活服务器：请确保从灵活服务器所在的虚拟网络内部进行连接。 请参阅 [Azure Database for MySQL 灵活服务器中的虚拟网络]<!--(./concepts-networking-virtual-network.md)-->
- 具有公共访问权限（允许的 IP 地址）的灵活服务器；请确保将防火墙配置为允许来自客户端的连接。 请参阅[使用 Azure 门户创建和管理灵活服务器防火墙规则](./how-to-manage-firewall-portal.md)。
* 客户端防火墙配置：客户端的防火墙必须允许连接到数据库服务器。 不能访问的服务器的 IP 地址和端口以及一些防火墙中的应用程序名称（如 MySQL）必须被允许。
* 用户失误：连接参数（例如连接字符串中的服务器名称）可能键入错误。

### <a name="resolve-connectivity-issues"></a>解决连接问题

* 若要详细了解加密连接，请参阅 [Azure Database for MySQL 灵活服务器中使用传输层安全性 (TLS 1.2) 的加密连接](./how-to-connect-tls-ssl.md)。
* 如果你使用的是公共访问权限（允许的 IP 地址），请设置[防火墙规则](./how-to-manage-firewall-portal.md)来允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 这样会使服务器向所有 IP 地址开放。 如果这样可以解决连接性问题，请删除此规则，再针对适当限制的 IP 地址或地址范围创建防火墙规则。
* 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 3306。
* 验证连接字符串和其他连接设置。 对于常见语言，请在 Azure 门户中参阅“连接字符串”页面中可用于你的服务器的预定义的连接字符串。

## <a name="next-steps"></a>后续步骤
- [使用 MySQL Workbench 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-workbench.md)。
- [使用 PHP 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-php.md)。
- [使用 Python 连接到 Azure Database for MySQL 灵活服务器并查询其中的数据](./connect-python.md)。
