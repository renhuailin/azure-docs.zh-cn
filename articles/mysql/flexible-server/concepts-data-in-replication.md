---
title: 数据传入复制 - Azure Database for MySQL Flexible
description: 了解如何使用数据传入复制从外部服务器同步到 Azure Database for MySQL Flexible 服务。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 071672c5c2d3c741abd14dad94c8c150e427a3ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464767"
---
# <a name="replicate-data-into-azure-database-for-mysql-flexible--server-preview"></a>将数据复制到 Azure Database for MySQL Flexible 服务器（预览）

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

通过数据传入复制可以将数据从外部 MySQL 服务器同步到 Azure Database for MySQL Flexible 服务中。 外部服务器可以处于本地、虚拟机中、Azure Database for MySQL 单台服务器中或是其他云提供商托管的数据库服务。 复制中数据是基于二进制日志 (binlog) 文件位置。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

> [!Note]
> Azure Database for MySQL Flexible 服务器目前不支持基于 GTID 的复制。<br>
> 不支持为区域冗余高可用性服务器配置数据传入复制 

## <a name="when-to-use-data-in-replication"></a>何时使用数据传入复制

可以考虑使用数据传入复制的主要场景有：

- 混合数据同步：借助复制中数据，可以在本地服务器和 Azure Database for MySQL Flexible 服务器之间同步数据。 此同步可用于创建混合应用程序。 如果有现有的本地数据库服务器，但想要将数据移到更靠近最终用户的区域，那么此方法很有吸引力。
- 多云同步：对于复杂的云解决方案，使用复制中数据在 Azure Database for MySQL Flexible 服务器和不同云提供程序之间同步数据，包括虚拟机和托管在这些云中的数据库服务。
- 迁移：客户可以将 [MyDumper/MyLoader](https://centminmod.com/mydumper.html) 之类的开源工具与数据传入复制配合使用，以执行时间最短的迁移。 通过数据传入复制，可以选择性地将生产负载从源数据库切换到目标数据库。 

对于迁移方案，请使用 [Azure 数据库迁移服务](https://azure.microsoft.com/services/database-migration/) (DMS)。

## <a name="limitations-and-considerations"></a>限制和注意事项

### <a name="data-not-replicated"></a>不会复制的数据

不会复制源服务器上的 [mysql 系统数据库](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)。 此外，不会复制对源服务器上的帐户和权限所做的更改。 如果在源服务器上创建帐户，并且此帐户需要访问副本服务器，则在副本服务器手动创建相同的帐户。 若要了解哪些表包含在系统数据库中，请参阅 [MySQL 手册](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)。

### <a name="data-in-replication-not-supported-on-ha-enabled-servers"></a>已启用 HA 的服务器不支持数据传入复制 
不支持为区域冗余高可用性服务器配置数据传入复制。 在已启用 HA 的服务器上，复制 `mysql.az_replication_*` 的存储过程将不可用。 

### <a name="filtering"></a>筛选

Azure Database for MySQL 灵活服务器当前不支持修改参数 `replicate_wild_ignore_table`，该参数用于为表创建复制筛选器。 

### <a name="requirements"></a>要求

- 源服务器版本必须至少是 MySQL 5.7 版。
- 建议为源服务器版本和复制服务器版本提供相同的版本。 例如，两者必须同时是 MySQL 5.7 版或 MySQL 8.0 版。
- 建议在每个表中使用主键。 如果表中没有主键，则在复制时可能会速度缓慢。
- 源服务器应使用 MySQL InnoDB 引擎。
- 用户必须有权在源服务器上配置二进制日志记录和创建新用户。
- 如果源服务器启用了 SSL，请确保为域提供的 SSL CA 证书已包含在 `mysql.az_replication_change_master` 存储过程中。 请参阅以下[示例](./how-to-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)和 `master_ssl_ca` 参数。
- 请确保托管源服务器的计算机在端口 3306 上允许入站和出站流量。
- 请确保源服务器具有公共 IP 地址，DNS 可公开访问，或者源服务器具有完全限定的域名 (FQDN)。
- 在公开访问的情况下，请确保源服务器具有公共 IP 地址，DNS 可公开访问，或者源服务器具有完全限定的域名 (FQDN)。
- 在私有访问的情况下，请确保源服务器名称可以解析，并且可从运行 Azure Database for MySQL 实例的 VNet 访问。有关详细信息，请参阅 [Azure 虚拟网络中资源的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="next-steps"></a>后续步骤

- 了解如何[设置复制中数据](how-to-data-in-replication.md)
- 了解[使用只读副本在 Azure 中进行复制](concepts-read-replicas.md)
