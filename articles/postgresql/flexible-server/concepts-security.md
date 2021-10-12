---
title: Azure Database for PostgreSQL（灵活服务器）中的安全性
description: 了解 Azure Database for PostgreSQL 的灵活服务器部署选项中的安全性。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 10/01/2021
ms.openlocfilehash: 8e474cd0ae6635034607a088aceee47a83c500de
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388767"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL（灵活服务器）中的安全性

可通过多层安全性来帮助保护 Azure Database for PostgreSQL 服务器上的数据。 本文概述了这些安全选项。

## <a name="information-protection-and-encryption"></a>信息保护和加密

Azure Database for PostgreSQL 通过两种方式加密数据：

- **传输中的数据**：Azure Database for PostgreSQL 使用安全套接字层和传输层安全性 (SSL/TLS) 加密传输中的数据。 默认情况下，强制实施加密。 如需更多详细信息，请参阅[此指南](how-to-connect-tls-ssl.md)。 为了提高安全性，可以选择启用 [SCRAM 身份验证](how-to-connect-scram.md)。

   如果需要，可以选择通过将 `require_secure_transport` 服务器参数更新为 OFF，来对 Azure Database for PostgreSQL 灵活服务器连接禁用 TLS\SSL，不过我们不建议这样做。 还可以通过设置 `ssl_min_protocol_version` 和 `ssl_max_protocol_version` 服务器参数来设置 TLS 版本。


- **静态数据**：对于存储加密，Azure Database for PostgreSQL 使用 FIPS 140-2 验证的加密模块。 数据（包括备份和运行查询时创建的临时文件）在磁盘上进行加密。 

  该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 这类似于其他静态加密技术，如 SQL Server 或 Oracle 数据库中的透明数据加密。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全性

运行 Azure Database for PostgreSQL 灵活服务器时，有两个主要的网络选项：

- **专用访问**：可将服务器部署到 Azure 虚拟网络中。 Azure 虚拟网络帮助提供专用的安全网络通信。 虚拟网络中的各个资源可通过专用 IP 地址进行通信。 有关详细信息，请参阅 [Azure Database for PostgreSQL 灵活服务器网络概述](concepts-networking.md)。

  通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 有关详细信息，请参阅[网络安全组概述](../../virtual-network/network-security-groups-overview.md)。

- **公共访问**：可通过公共终结点访问服务器。 公共终结点是可公开解析的 DNS 地址。 对它的访问是通过防火墙保护的，默认情况下，防火墙会阻止所有连接。 

  IP 防火墙规则基于每个请求的起始 IP 地址授予对服务器的访问权限。 有关详细信息，请参阅[防火墙规则概述](concepts-firewall-rules.md)。

## <a name="access-management"></a>访问管理

创建 Azure Database for PostgreSQL 服务器时，需要为管理员角色提供凭据。 可以通过此管理员角色创建更多 [PostgreSQL 角色](https://www.postgresql.org/docs/current/user-manag.html)。

例如，应用于对象的

```SQL
postgres=> create role demouser with password 'password123';
```

可以定期审核服务器中的角色列表。 例如，可以使用 `psql` 客户端进行连接，并查询 `pg_roles` 表，其中列出了所有角色以及权限（例如创建其他角色、创建数据库、复制，等等）。 

```SQL
postgres=> \x
Expanded display is on.
postgres=> select * from pg_roles where rolname='demouser';
-[ RECORD 1 ]--+---------
rolname        | demouser
rolsuper       | f
rolinherit     | t
rolcreaterole  | f
rolcreatedb    | f
rolcanlogin    | f
rolreplication | f
rolconnlimit   | -1
rolpassword    | ********
rolvaliduntil  |
rolbypassrls   | f
rolconfig      |
oid            | 24827

```

[审核日志记录](../concepts-audit.md)功能还可用于灵活服务器，以跟踪数据库中的活动。 

> [!NOTE]
> Azure Database for PostgreSQL 灵活服务器当前不支持 [Azure Defender 保护](../../security-center/azure-defender.md)。 

## <a name="updating-passwords"></a>更新密码

为了提高安全性，最好是定期轮换管理员密码和数据库用户密码。 建议使用包含大写和小写字母、数字和特殊字符的强密码。

### <a name="reset-administrator-password"></a>重置管理员密码

请按[操作指南](./how-to-manage-server-portal.md#reset-admin-password)重置管理员密码。

### <a name="update-database-user-password"></a>更新数据库用户密码

可以使用客户端工具来更新数据库用户密码。 例如，应用于对象的
```SQL
postgres=> alter role demouser with password 'Password123!';
ALTER ROLE
```
## <a name="next-steps"></a>后续步骤
- 为公共访问网络启用[用于 IP 地址的防火墙规则](concepts-firewall-rules.md)。
- 了解 [Azure Database for PostgreSQL 灵活服务器的专用访问网络](concepts-networking.md)。
- 了解 Azure Database for PostgreSQL 中的 [Azure Active Directory 身份验证](../concepts-aad-authentication.md)。