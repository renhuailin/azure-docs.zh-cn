---
title: 转储和还原 - Azure Database for PostgreSQL - 单一服务器
description: 可将 PostgreSQL 数据库提取到转储文件中。 然后，可以从 pg_dump 在 Azure Database for PostgreSQL 单一服务器中创建的文件进行还原。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450049"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>使用转储和还原迁移 PostgreSQL 数据库
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

可以使用 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 将 PostgreSQL 数据库提取到转储文件中。 然后，使用 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 从 `pg_dump` 创建的存档文件还原 PostgreSQL 数据库。

## <a name="prerequisites"></a>必备条件

若要逐步执行本操作方法指南，需要：
- 一个 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)，其中包含允许访问的防火墙规则。
- 已安装 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 命令行实用工具。

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>创建一个包含要加载的数据的转储文件

若要在本地或某个 VM 中备份现有 PostgreSQL 数据库，请运行以下命令：

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
例如，如果你有一个本地服务器，并且该服务器中包含一个名为 testdb 的数据库，请运行：

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>将数据还原到目标数据库中

创建目标数据库后，可以使用 `pg_restore` 命令和 `--dbname` 参数将数据从转储文件还原到目标数据库中。

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

包含 `--no-owner` 参数会导致还原过程中创建的所有对象由使用 `--username` 指定的用户拥有。 有关详细信息，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)。

> [!NOTE]
> 在 Azure Database for PostgreSQL 服务器上，默认已打开 TLS/SSL 连接。 如果你的 PostgreSQL 服务器需要 TLS/SSL 连接但未建立这种连接，请设置环境变量 `PGSSLMODE=require`，以便 pg_restore 工具使用 TLS 进行连接。 如果不使用 TLS，错误可能会显示：“严重错误: 需要 SSL 连接。 请指定 SSL 选项，然后重试。” 在 Windows 命令行中，先运行命令 `SET PGSSLMODE=require`，然后再运行 `pg_restore` 命令。 在 Linux 或 Bash 中，先运行命令 `export PGSSLMODE=require`，然后再运行 `pg_restore` 命令。 
>

此示例将数据从转储文件 testdb.dump 还原到目标服务器 mydemoserver.postgres.database.azure.com 上的数据库 mypgsqldb 中  。

以下示例演示如何对单一服务器使用此 `pg_restore`：

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

以下示例演示如何对灵活服务器使用此 `pg_restore`：

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>优化迁移过程

将现有 PostgreSQL 数据库迁移到 Azure Database for PostgreSQL 的一种方法是，在源上备份数据库并在 Azure 中进行还原。 若要最大限度地缩短完成迁移所需的时间，请考虑将以下参数与备份和还原命令结合使用。

> [!NOTE]
> 有关语法的详细信息，请参阅 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)。
>

### <a name="for-the-backup"></a>对于备份

使用 `-Fc` 开关进行备份，以便能够并行执行还原以提高速度。 例如：

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>对于还原

- 将备份文件移入你要迁移到的 Azure Database for PostgreSQL 服务器所在的同一区域中的 Azure VM。 在该 VM 中执行 `pg_restore` 以降低网络延迟。 在启用[加速网络](../virtual-network/create-vm-accelerated-networking-powershell.md)的情况下创建 VM。

- 打开转储文件，验证创建索引的语句是否在插入数据的语句之后。 如果不是这种情况，请将 create index 语句移动到插入的数据之后。 默认情况下应该是这样的，但最好是确认一下。

- 使用开关 `-Fc` 和 `-j`（并提供数字）进行并行还原。 指定的数字是目标服务器上的核心数。 还可以设置为目标服务器核心数的两倍，以查看产生的影响。

    以下示例演示如何对单一服务器使用此 `pg_restore`：

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    以下示例演示如何对灵活服务器使用此 `pg_restore`：

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- 此外，还可以通过在开头添加 `set synchronous_commit = off;` 命令并在末尾添加 `set synchronous_commit = on;` 命令来编辑转储文件。 如果在应用更改数据之前未在末尾打开该功能，可能会导致随后的数据丢失。

- 在目标 Azure Database for PostgreSQL 服务器上，请考虑在还原之前执行以下操作：
    
  - 关闭查询性能跟踪。 迁移期间不需要这些统计信息。 为此，可以将 `pg_stat_statements.track`、`pg_qs.query_capture_mode` 和 `pgms_wait_sampling.query_capture_mode` 设置为 `NONE`。

  - 使用高计算和高内存 SKU（如 32 vCore 内存优化）来加速迁移。 还原完成后，可以轻松缩减为所需的 SKU。 SKU 越高，通过增大 `pg_restore` 命令中相应 `-j` 参数可以实现的并行度就越高。

  - 增大目标服务器上的 IOPS 可以提高还原性能。 你可以通过增加服务器的存储大小来预配更多 IOPS。 此设置不可逆，但需要考虑到提高 IOPS 是否在将来有益于你的实际工作负载。

请记住先在测试环境中测试和验证这些命令，然后再将其用于生产。

## <a name="next-steps"></a>后续步骤

- 若要使用导出和导入迁移 PostgreSQL 数据库，请参阅[使用导入和导出迁移 PostgreSQL 数据库](howto-migrate-using-export-and-import.md)。
- 有关将数据库迁移到 Azure Database for PostgreSQL 的详细信息，请参阅[数据库迁移指南](https://aka.ms/datamigration)。


