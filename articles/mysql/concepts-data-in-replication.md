---
title: 数据传入复制 - Azure Database for MySQL
description: 了解如何使用数据传入复制从外部服务器同步到 Azure Database for MySQL 服务。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 25fdaa37ea7355edd9b27dce7f60d45655299fa8
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653577"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>将数据复制到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

通过数据传入复制可以将数据从外部 MySQL 服务器同步到 Azure Database for MySQL 服务中。 外部服务器可以处于本地、虚拟机中或是其他云提供商托管的数据库服务。 数据传入复制以基于二进制日志 (binlog) 文件位置或基于 GTID 的从本机到 MySQL 的复制为基础。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

## <a name="when-to-use-data-in-replication"></a>何时使用配置复制中数据

可以考虑使用数据传入复制的主要场景有：

-  混合数据同步：借助复制中数据，可以在本地服务器和 Azure Database for MySQL 之间同步数据。 此同步可用于创建混合应用程序。 如果有现有的本地数据库服务器，但想要将数据移到更靠近最终用户的区域，那么此方法很有吸引力。
-  多云同步：对于复杂的云解决方案，使用复制中数据在 Azure Database for MySQL 和不同云提供程序之间同步数据，包括虚拟机和托管在这些云中的数据库服务。

对于迁移方案，请使用 [Azure 数据库迁移服务](https://azure.microsoft.com/services/database-migration/) (DMS)。

## <a name="limitations-and-considerations"></a>限制和注意事项

### <a name="data-not-replicated"></a>不会复制的数据

不会复制源服务器上的 [mysql 系统数据库](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)。 此外，不会复制对源服务器上的帐户和权限所做的更改。 如果在源服务器上创建帐户，并且此帐户需要访问副本服务器，则在副本服务器手动创建相同的帐户。 若要了解哪些表包含在系统数据库中，请参阅 [MySQL 手册](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)。

### <a name="filtering"></a>Filtering

若要跳过从源服务器（托管在本地、虚拟机中或其他云提供程序托管的数据库服务中）复制表的操作，需支持 `replicate_wild_ignore_table` 参数。 （可选）使用 [Azure 门户](howto-server-parameters.md)或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 在副本服务器（在 Azure 中托管）上更新此参数。

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)。

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>仅在常规用途或内存优化层中受支持

数据传入复制仅可在常规用途和内存优化定价层中受支持。

### <a name="requirements"></a>要求

- 源服务器版本必须至少是 MySQL 5.6 版。
- 源服务器版本和副本服务器版本必须相同。 例如，两者必须同时是 MySQL 5.6 版或 MySQL 5.7 版。
- 每个表都必须有主键。
- 源服务器应使用 MySQL InnoDB 引擎。
- 用户必须有权在源服务器上配置二进制日志记录和创建新用户。
- 如果源服务器启用了 SSL，请确保为域提供的 SSL CA 证书已包含在 `mysql.az_replication_change_master` 或 `mysql.az_replication_change_master_with_gtid` 存储过程中。 请参阅以下[示例](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)和 `master_ssl_ca` 参数。
- 请确保源服务器的 IP 地址已添加到 Azure Database for MySQL 副本服务器的防火墙规则中。 使用 [Azure 门户](./howto-manage-firewall-using-portal.md)或 [Azure CLI](./howto-manage-firewall-using-cli.md) 更新防火墙规则。
- 请确保托管源服务器的计算机在端口 3306 上允许入站和出站流量。
- 请确保源服务器具有公共 IP 地址，DNS 可公开访问，或者源服务器具有完全限定的域名 (FQDN)。

## <a name="next-steps"></a>后续步骤

- 了解如何[设置复制中数据](howto-data-in-replication.md)
- 了解[使用只读副本在 Azure 中进行复制](concepts-read-replicas.md)
- 了解如何[使用 DMS 在最短的停机时间内迁移数据](howto-migrate-online.md)
