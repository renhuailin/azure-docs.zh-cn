---
title: 配置命名副本安全性以允许进行独立访问
description: 了解在配置和管理命名副本，使用户能够访问命名副本但不能访问其他副本时的安全注意事项。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 88410573dbefdfdedb4628babfffc601b19bed36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744865"
---
# <a name="configure-isolated-access-to-a-hyperscale-named-replica"></a>配置对超大规模命名副本的隔离访问
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍了向 Azure SQL 超大规模[命名副本](service-tier-hyperscale-replicas.md)授予访问权限的过程，无需授予对主要副本或其他命名副本的访问权限。 此方案允许命名副本隔离资源和安全性（因为命名副本将使用其自身的计算节点运行）。每当需要对 Azure SQL 超大规模数据库进行独立的只读访问时，此方案就非常有用。 在此上下文中，“独立”是指主要副本与命名副本之间不共享 CPU 和内存，针对命名副本运行的查询不会使用主要副本或任何其他副本的计算资源，并且访问命名副本的主体不能访问包括主要副本在内的其他副本。

## <a name="create-a-login-in-the-master-database-on-the-primary-server"></a>在主服务器上的 master 数据库中创建登录名

在托管主数据库的逻辑服务器上的 `master` 数据库中，执行以下各项以创建新登录名。 使用你自己的强密码和唯一密码。

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!';
```

从 `sys.sql_logins` 系统视图中检索所创建的登录名的 SID 十六进制值：

```sql
select sid from sys.sql_logins where name = 'third-party-login';
```

禁用登录名。 这会阻止此登录名访问托管主要副本的服务器中的任何数据库。

```sql
alter login [third-party-login] disable;
```

## <a name="create-a-user-in-the-primary-read-write-database"></a>在主读写数据库中创建用户

创建登录名后，连接到数据库的主读写副本，例如 WideWorldImporters（你可以在此处找到要还原的示例脚本：[在 Azure SQL 中还原数据库](https://github.com/yorek/azure-sql-db-samples/tree/master/samples/01-restore-database)）并为该登录名创建一个数据库用户：

```sql
create user [third-party-user] from login [third-party-login];
```

作为一个可选步骤，一旦创建了数据库用户，如果担心以任何方式重新启用登录名，则可以删除上一步中创建的服务器登录名。 连接到托管主数据库的逻辑服务器上的 master 数据库，然后执行以下操作：

```sql
drop login [third-party-login];
```

## <a name="create-a-named-replica-on-a-different-logical-server"></a>在不同的逻辑服务器上创建命名副本

创建新的 Azure SQL 逻辑服务器用于对命名副本进行独立访问。 按照[在 Azure SQL 数据库中创建和管理服务器及单一数据库](single-database-manage.md)中提供的说明进行操作。 若要创建命名副本，此服务器必须与承载主要副本的服务器位于同一 Azure 区域中。

例如，使用 AZ CLI：

```azurecli
az sql server create -g MyResourceGroup -n MyNamedReplicaServer -l MyLocation --admin-user MyAdminUser --admin-password MyStrongADM1NPassw0rd!
```

然后，为此服务器上的主数据库创建命名副本。 例如，使用 AZ CLI：

```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyPrimaryServer --secondary-type Named --partner-database WideWorldImporters_NR --partner-server MyNamedReplicaServer
```

## <a name="create-a-login-in-the-master-database-on-the-named-replica-server"></a>在命名副本服务器上的 master 数据库中创建登录名

在承载上一步中创建的命名副本的逻辑服务器上，连接到 `master` 数据库。 使用从主要副本中检索到的 SID 添加登录名：

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!', sid = 0x0...1234;
```

此时，使用 `third-party-login` 的用户和应用程序可以连接到命名副本，但不能连接到主要副本。

## <a name="grant-object-level-permissions-within-the-database"></a>授予数据库中的对象级权限

按照说明设置登录身份验证后，可以使用常规 `GRANT`、`DENY` 和 `REVOKE` 语句来管理授权或数据库中的对象级权限。 在这些语句中，引用你在数据库中创建的用户名称，或者引用包含此用户作为成员的数据库角色。 务必要在主要副本上执行这些命令。 这些更改将传播到所有次要副本，但它们仅在创建服务器级登录名的命名副本上有效。

请记住，默认情况下，新创建的用户仅被授予最小权限集（例如，它不能访问任何用户表）。 如果要允许 `third-party-user` 读取表中的数据，需要显式授予 `SELECT` 权限：

```sql
grant select on [Application].[Cities] to [third-party-user];
```

作为单独授予每个表的权限的替代方法，你可以将该用户添加到 `db_datareaders` [数据库角色](/sql/relational-databases/security/authentication-access/database-level-roles)，以允许对所有表进行读取访问，也可以使用[架构](/sql/relational-databases/security/authentication-access/create-a-database-schema)来[允许访问](/sql/t-sql/statements/grant-schema-permissions-transact-sql)架构中的所有现有表和新表。

## <a name="test-access"></a>测试访问权限

可以通过使用任意客户端工具来测试此配置，并尝试连接到主要副本和命名副本。 例如，可以使用 `sqlcmd` 尝试以 `third-party-login` 用户身份连接到主要副本：

```
sqlcmd -S MyPrimaryServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters
```

这会导致出错，因为不允许该用户连接到服务器：

```
Sqlcmd: Error: Microsoft ODBC Driver 13 for SQL Server : Login failed for user 'third-party-login'. Reason: The account is disabled.
```

连接到命名副本成功：

```
sqlcmd -S MyNamedReplicaServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters_NR
```

不会返回任何错误，并且可以通过授予的对象级别权限，在命名副本上执行查询。

更多相关信息：

* Azure SQL 逻辑服务器：参阅 [Azure SQL 数据库中的服务器是什么](logical-servers.md)
* 管理数据库访问和登录：参阅 [SQL 数据库安全性：管理数据库访问和登录安全性](logins-create-manage.md)
* 数据库引擎权限：参阅[权限](/sql/relational-databases/security/permissions-database-engine) 
* 授予对象权限：参阅[授予对象权限](/sql/t-sql/statements/grant-object-permissions-transact-sql)



