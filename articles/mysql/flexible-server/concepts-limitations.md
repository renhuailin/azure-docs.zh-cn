---
title: 限制 - Azure Database for MySQL（灵活服务器）
description: 本文介绍了 Azure Database for MySQL（灵活服务器）中的限制，例如连接数和存储引擎选项。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 48aef337326d58b2a503dc48862571efde0d37ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034514"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器（预览版）中的限制

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍了 Azure Database for MySQL 灵活服务器服务中的限制。 MySQL 数据库引擎中的[常规限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html)也适用。 如果想了解资源（计算、内存、存储）层，请参阅[计算和存储](concepts-compute-storage.md)一文。

## <a name="server-parameters"></a>服务器参数

> [!NOTE]
> 如果要查找服务器参数（如 `max_connections` 和 `innodb_buffer_pool_size`）的最小值/最大值，请参阅[服务器参数](./concepts-server-parameters.md)一文中的服务器参数概念。

Azure Database for MySQL 支持优化服务器参数的值。 某些参数（例如 `max_connections`、`join_buffer_size`、`query_cache_size`）由服务器的计算层和计算大小确定。 有关这些限制的详细信息，请参阅[服务器参数](./concepts-server-parameters.md)。

该服务不支持密码插件，例如“validate_password”和“caching_sha2_password”。

## <a name="storage-engines"></a>存储引擎

MySQL 支持许多存储引擎。 在 Azure Database for MySQL 灵活服务器上，支持和不支持的存储引擎如下所示：

### <a name="supported"></a>支持
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>不支持
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>权限和数据操作支持

许多服务器参数和设置可能会无意中导致服务器性能下降或使 MySQL 服务器的 ACID 属性无效。 为了在产品级别维护服务完整性和 SLA，此服务不公开多个角色。 

MySQL 服务不允许直接访问基础文件系统。 不支持某些数据操作命令。 

### <a name="unsupported"></a>不支持

不支持以下项：
- DBA 角色：受限制。 另外，使用管理员用户（在新建服务器的过程中创建）可执行大部分 DDL 和 DML 语句。 
- SUPER 特权：类似地，[SUPER 特权](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)也受到限制。
- DEFINER：需要创建并限制超级权限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `--skip-definer` 命令删除 `CREATE DEFINER` 命令。
- 系统数据库：[mysql 系统数据库](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)为只读数据库，用于支持各种 PaaS 功能。 不能对 `mysql` 系统数据库进行更改。
- `SELECT ... INTO OUTFILE`：在该服务中不受支持。

### <a name="supported"></a>支持
- 支持 `LOAD DATA INFILE`，但必须指定 `[LOCAL]` 参数，并将其定向到 UNC 路径（通过 SMB 装载的 Azure 存储空间）。

## <a name="functional-limitations"></a>功能限制

### <a name="zone-redundant-ha"></a>区域冗余 HA
- 只能在服务器创建过程中设置此配置。
- 在可突发计算层中不受支持。

### <a name="networking"></a>网络
- 创建服务器后，无法更改连接方法。 如果服务器是使用专用访问（VNet 集成）创建的，则在创建后，不能将其更改为公共访问（允许的 IP 地址），反之亦然 
- TLS/SSL 默认处于启用状态，并且无法禁用。
- 服务器支持的最低 TLS 版本为 TLS 1.2。 请参阅[使用 TLS/SSL 连接](./how-to-connect-tls-ssl.md)了解详情。

### <a name="stopstart-operation"></a>停止/启动操作
- 区域冗余 HA 配置（主要配置和备用配置）不支持。
- 只读副本配置（源配置和副本配置）不支持。

### <a name="scale-operations"></a>缩放操作
- 不支持减小预配的服务器存储。

### <a name="read-replicas"></a>只读副本
- 区域冗余 HA 配置（主要配置和备用配置）不支持。

### <a name="server-version-upgrades"></a>服务器版本升级
- 不支持在主要数据库引擎版本之间进行自动迁移。 如果要升级主版本，请进行[转储并将其还原](../concepts-migrate-dump-restore.md)到使用新引擎版本创建的服务器。

### <a name="restoring-a-server"></a>还原服务器
- 使用时间点还原功能时，将使用与新服务器所基于的源服务器相同的计算和存储配置创建新服务器。 创建服务器后，新还原的服务器的计算可以缩减。
- 不支持还原已删除的服务器。

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>单一服务器中可用的功能在灵活服务器中尚不受支持 
并非所有在 Azure Database for MySQL - 单一服务器中可用的功能都可以在灵活服务器中使用。 有关单一服务器和灵活服务器功能比较的完整列表，请参阅[在 Azure 文档中选择正确的 MySQL Server 选项。](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure 文档中选择正确的 MySQL Server 选项](../select-right-deployment-type.md)
- 了解[灵活服务器中可用于计算和存储选项的内容](concepts-compute-storage.md)
- 了解[受支持的 MySQL 版本](concepts-supported-versions.md)
- 快速入门：[使用 Azure 门户创建 Azure Database for MySQL 灵活服务器](quickstart-create-server-portal.md)
