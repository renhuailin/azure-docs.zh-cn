---
title: Azure Database for PostgreSQL（灵活服务器）中的安全性
description: 了解 Azure Database for PostgreSQL 的灵活服务器部署选项中的安全性。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 6fcd772fce8a3f5e869f12e9b20c1219adf8155b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866848"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL（灵活服务器）中的安全性

可以使用多层安全性来保护 Azure Database for PostgreSQL 服务器上的数据。 本文概述了这些安全选项。

## <a name="information-protection-and-encryption"></a>信息保护和加密

Azure Database for PostgreSQL 通过两种方式加密数据：

- 传输中的数据：Azure Database for PostgreSQL 通过安全套接字层和传输层安全性 (SSL/TLS) 来加密传输中的数据。 默认情况下会强制实施加密。
- 静态数据：Azure Database for PostgreSQL 使用 FIPS 140-2 验证的加密模块进行存储加密。 数据（包括备份和运行查询时创建的临时文件）在磁盘上进行加密。 

  该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 这类似于其他静态加密技术，如 SQL Server 或 Oracle 数据库中的透明数据加密。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全性

运行 Azure Database for PostgreSQL 灵活服务器时，有两个主要的网络选项：

- 专用访问：可将服务器部署到 Azure 虚拟网络中。 Azure 虚拟网络提供专用的安全网络通信。 虚拟网络中的各个资源可通过专用 IP 地址进行通信。 有关详细信息，请参阅[Azure Database for PostgreSQL 灵活服务器网络概述](concepts-networking.md)。

  通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 有关详细信息，请参阅[网络安全组概述](../../virtual-network/network-security-groups-overview.md)。

- 公共访问：可通过公共终结点访问服务器。 公共终结点是可公开解析的 DNS 地址。 对它的访问是通过防火墙进行保护的，默认情况下，防火墙会阻止所有连接。 

  IP 防火墙规则基于每个请求的起始 IP 地址授予对服务器的访问权限。 有关详细信息，请参阅[防火墙规则概述](concepts-firewall-rules.md)。

## <a name="access-management"></a>访问管理

创建 Azure Database for PostgreSQL 服务器时，需要为管理员角色提供凭据。 此管理员角色可用于创建更多 [PostgreSQL 角色](https://www.postgresql.org/docs/current/user-manag.html)。

还可以使用 [Azure Active Directory 身份验证](../concepts-aad-authentication.md)连接到服务器。 [审核日志记录](../concepts-audit.md)可用于跟踪数据库中的活动。 

> [!NOTE]
> Azure Database for PostgreSQL 灵活服务器目前不支持 [Azure Defender 保护](../../security-center/azure-defender.md)。 

## <a name="next-steps"></a>后续步骤
- 为公共访问网络启用[用于 IP 地址的防火墙规则](concepts-firewall-rules.md)。
- 了解 [Azure Database for PostgreSQL 灵活服务器的专用访问网络](concepts-networking.md)。
- 了解 Azure Database for PostgreSQL 中的 [Azure Active Directory 身份验证](../concepts-aad-authentication.md)。