---
title: Azure Database for PostgreSQL（灵活服务器）中的安全性
description: 了解 Azure Database for PostgreSQL（灵活服务器）的灵活服务器部署选项中的安全性
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 489ae3e9593ed5fa0865fb6f3fbe2eb617113d71
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181489"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL（灵活服务器）中的安全性

可以通过多层安全性来保护 Azure Database for PostgreSQL 服务器上的数据。 本文概述了这些安全选项。

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="in-transit"></a>动态
 Azure Database for PostgreSQL 使用传输层安全性来加密动态数据，通过这种方式来保护数据。 默认情况下，强制实施加密 (SSL/TLS)。

### <a name="at-rest"></a>静态
Azure Database for PostgreSQL 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 数据（包括备份）在磁盘上加密，包括运行查询时创建的临时文件。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 这类似于其他静态加密技术，如 SQL Server 或 Oracle 数据库中的透明数据加密 (TDE)。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全性

运行 Azure Database for PostgreSQL（灵活服务器）时，可选择两个主要网络选项。 这两个选项是“专用访问(VNet 集成)”和“公共访问(允许的 IP 地址)” 。 利用专用访问，灵活服务器可部署到 Azure 虚拟网络中。 Azure 虚拟网络提供专用的安全网络通信。 虚拟网络中的各个资源可通过专用 IP 地址进行通信。
利用公共访问，可通过公共终结点访问灵活服务器。 公共终结点是可公开解析的 DNS 地址，对该地址的访问可通过防火墙进行保护，默认情况下防火墙会阻止所有连接。 



### <a name="ip-firewall-rules"></a>IP 防火墙规则
IP 防火墙规则基于每个请求的起始 IP 地址授予对服务器的访问权限。 有关详细信息，请参阅[防火墙规则概述](concepts-firewall-rules.md)。


### <a name="private-vnet-access"></a>专用 VNET 访问
可将灵活服务器部署到 Azure 虚拟网络中。 Azure 虚拟网络提供专用的安全网络通信。 有关详细信息，请参阅[灵活服务器](concepts-networking.md)

### <a name="network-security-groups-nsg"></a>网络安全组 (NSG)
通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。  有关详细信息，请参阅[网络安全组概述](../../virtual-network/network-security-groups-overview.md)

## <a name="access-management"></a>访问管理

在创建 Azure Database for PostgreSQL 服务器时，我们会提供管理员角色的凭据。 可以通过此管理员角色创建其他 [PostgreSQL 角色](https://www.postgresql.org/docs/current/user-manag.html)。

还可以使用 [Azure Active Directory (AAD) 身份验证](../concepts-aad-authentication.md)连接到服务器。


### <a name="azure-defender-protection"></a>Azure Defender 保护

 Azure Database for PostgreSQL（灵活服务器）当前不支持 [Azure Defender 保护](../../security-center/azure-defender.md)。 


[审核日志记录](../concepts-audit.md)可用于跟踪数据库中的活动。 


## <a name="next-steps"></a>后续步骤
  - 为公共访问网络启用 [IP](concepts-firewall-rules.md) 的防火墙规则
  - 了解 [Azure Database for PostgreSQL（灵活服务器）的专用访问网络](concepts-networking.md)
  - 了解 Azure Database for PostgreSQL 中的 [Azure Active Directory 身份验证](../concepts-aad-authentication.md)