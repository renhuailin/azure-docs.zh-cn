---
title: 解决数据库损坏问题 - Azure Database for MySQL
description: 在本文中，你将了解如何修复 Azure Database for MySQL 中的数据库损坏问题。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d9b79ba83613a854610eae8fee6392d6e12ef935
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653172"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>排查 Azure Database for MySQL 中的数据库损坏问题

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

数据库损坏可能导致应用程序停止工作。 此外，及时解决损坏问题以避免数据丢失也是至关重要的。 在发生数据库损坏时，服务器日志中将会出现此错误：`InnoDB: Database page corruption on disk or a failed.`

在本文中，你将了解如何解决数据库或表的损坏问题。 Azure Database for MySQL 使用 InnoDB 引擎。 该引擎具有自动执行损坏检查和修复操作的功能。 InnoDB 通过在其读取的每一页上运行校验和来检查是否有损坏的页。 如果它发现校验和差异，那么它将会自动停止 MySQL 服务器。

请尝试以下选项，以快速缓解数据库损坏问题。

## <a name="restart-your-mysql-server"></a>重启 MySQL 服务器

你通常会在应用程序访问表或数据库时发现该数据库或表已损坏。 InnoDB 具有崩溃恢复机制，该机制可在服务器重启时解决大多数问题。 因此，重启服务器可以帮助服务器从导致数据库处于不良状态的故障中恢复。

## <a name="use-the-dump-and-restore-method"></a>使用“转储并还原”方法

建议通过使用“转储并还原”方法来解决损坏问题。 此方法涉及以下操作：
1. 访问损坏的表。
1. 使用 mysqldump 实用工具来创建表的逻辑备份。 该备份将保留表结构和表中的数据。
1. 将该表重新加载到数据库中。

### <a name="back-up-your-database-or-tables"></a>备份数据库或表

> [!Important]
> - 请确保已经配置了防火墙规则，以便从客户端计算机访问服务器。 有关详细信息，请参阅[在单一服务器上配置防火墙规则](howto-manage-firewall-using-portal.md)和[在灵活服务器上配置防火墙规则](flexible-server/how-to-connect-tls-ssl.md)。
> - 如果已启用 SSL，请将 SSL 选项 `--ssl-cert` 用于 mysqldump。

通过使用 mysqldump 从命令行创建备份文件。 使用此命令：

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

参数说明：
- `[ssl-cert=/path/to/pem]`：SSL 证书的路径。 在客户端计算机上下载 SSL 证书，并在该命令中设置这台计算机中的路径。 如果已禁用 SSL，请勿使用此参数。
- `[host]`：Azure Database for MySQL 服务器。
- `[uname]`：服务器管理员用户名。
- `[pass]`：管理员用户的密码。
- `[dbname]`：数据库名称。
- `[backupfile.sql]`：数据库备份的文件名。

> [!Important]
> - 对于单一服务器，请使用 `admin-user@servername` 格式来替换下面命令中的 `myserveradmin`。
> - 对于灵活服务器，请使用 `admin-user` 格式来替换下面命令中的 `myserveradmin`。

如果某个特定表已损坏，请选择数据库中要备份的特定表：
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

若要备份一个或多个数据库，请使用 `--database` 开关并列出数据库名称（用空格分隔）：

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>还原数据库或表

以下步骤说明了如何还原数据库或表。 在创建备份文件后，可以通过使用 mysql 实用工具来还原表或数据库。 运行以下命令：

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
下面的示例说明了如何从使用 mysqldump 创建的备份文件中还原 `testdb`： 

> [!Important]
> - 对于单一服务器，请使用 `admin-user@servername` 格式来替换下面命令中的 `myserveradmin`。
> - 对于灵活服务器，请使用 ```admin-user``` 格式来替换下面命令中的 `myserveradmin`。 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>后续步骤
如果上述步骤未解决问题，那么你总是还可以还原整个服务器：
- [在 Azure Database for MySQL（单一服务器）中还原服务器](howto-restore-server-portal.md)
- [在 Azure Database for MySQL 中还原服务器 - 灵活服务器](flexible-server/how-to-restore-server-portal.md)



