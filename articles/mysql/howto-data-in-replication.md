---
title: 配置数据传入复制 - Azure Database for MySQL
description: 本文介绍如何为 Azure Database for MySQL 设置复制中数据。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 04/08/2021
ms.openlocfilehash: fb12a8617396eb0c582ed2b00f856af7f91765fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611407"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>如何配置 Azure Database for MySQL 的数据传入复制

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍如何通过配置源服务器和副本服务器在 Azure Database for MySQL 中设置[数据传入复制](concepts-data-in-replication.md)。 本文假设读者在 MySQL 服务器和数据库方面有一定的经验。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
>

若要在 Azure Database for MySQL 服务中创建副本，[数据传入复制](concepts-data-in-replication.md)需同步本地 MySQL 源服务器、虚拟机 (VM) 或云数据库服务中的数据。 数据传入复制以基于二进制日志 (binlog) 文件位置或基于 GTID 的从本机到 MySQL 的复制为基础。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

在执行本文中的步骤之前，请查看数据传入复制的[限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

## <a name="create-an-azure-database-for-mysql-single-server-instance-to-use-as-a-replica"></a>创建要用作副本的 Azure Database for MySQL 单一服务器实例

1. 创建一个新的 Azure Database for MySQL 单一服务器实例（例如 `replica.mysql.database.azure.com`）。 请参阅[使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)，了解如何创建服务器。 此服务器是数据传入复制的“副本”服务器。

   > [!IMPORTANT]
   > 必须在“常规用途”或“内存优化”定价层中创建 Azure Database for MySQL 服务器，因为数据传入复制只在这些层中受支持。

2. 创建相同的用户帐户和对应的特权。

   用户帐户不会从源服务器复制到副本服务器。 如果打算为用户提供访问副本服务器的权限，则需在这个新创建的 Azure Database for MySQL 服务器上手动创建所有帐户和相应的特权。

3. 将源服务器的 IP 地址添加到副本的防火墙规则。

   使用 [Azure 门户](howto-manage-firewall-using-portal.md)或 [Azure CLI](howto-manage-firewall-using-cli.md) 更新防火墙规则。

4. 可选 - 如果想要使用从源服务器到 Azure Database for MySQL 副本服务器的[基于 GTID 的复制](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)，则需要在 Azure Database for MySQL 服务器上启用以下服务器参数，如以下门户图像所示：

   :::image type="content" source="./media/howto-data-in-replication/enable-gtid.png" alt-text="在 Azure Database for MySQL 服务器上启用 GTID":::

## <a name="configure-the-source-mysql-server"></a>配置源 MySQL 服务器

以下步骤准备并配置本地或虚拟机中托管的 MySQL 服务器或其他云提供程序托管的数据库服务，以便向内复制数据。 该服务器是数据传入复制的“源”。

1. 请先查看[源服务器要求](concepts-data-in-replication.md#requirements)，然后再继续。

2. 请确保源服务器允许端口 3306 上的入站和出站流量，并且源服务器具有公共 IP 地址，DNS 可供公开访问，或者 DNS 具有完全限定的域名 (FQDN)。

   通过尝试从工具（例如其他计算机上托管的 MySQL 命令行）或 Azure 门户中提供的 [Azure Cloud Shell](../cloud-shell/overview.md) 连接，测试与源服务器的连接。

   如果你的组织有严格的安全策略，并且不允许源服务器上的所有 IP 地址都能进行从 Azure 到源服务器的通信，那么你可能可以使用以下命令来确定 MySQL 服务器的 IP 地址。

   1. 使用 MySQL 命令行之类的工具登录 Azure Database for MySQL 服务器。

   2. 执行以下查询。

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      下面是一些示例输出：

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. 退出 MySQL 命令行。
   4. 若要获取 IP 地址，请在 ping 实用工具中执行以下命令：

      ```bash
      ping <output of step 2b>
      ```

      例如： 。

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 配置源服务器的防火墙规则，以便在端口 3306 上包括上一步的输出 IP 地址。

      > [!NOTE]
      > 此 IP 地址可能因维护/部署操作而发生更改。 这种连接方法仅适用于无法承受在 3306 端口上允许所有 IP 地址的客户。
  
3. 启用二进制日志记录。

   运行以下命令以检查是否已在源服务器上启用了二进制日志记录：

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果返回了值为“ON”的变量 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin)，则表示已在服务器上启用了二进制日志记录。

   如果 `log_bin` 返回的值为“OFF”，并且源服务器在本地或虚拟机上运行，而你可以访问配置文件 (my.cnf)，则可以执行以下步骤：
   1. 在源服务器中找到 MySQL 配置文件 (my.cnf)。 例如：/etc/my.cnf
   2. 打开配置文件进行编辑，并在文件中找到“mysqld”部分。
   3. 在 mysqld 部分中，添加以下行：

       ```bash
       log-bin=mysql-bin.log
       ```

   4. 重新启动 MySQL 源服务器以使更改生效。
   5. 重启服务器后，运行与之前相同的查询来验证是否已启用二进制日志记录：

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. 配置源服务器设置。

   “数据传入复制”要求参数 `lower_case_table_names` 在源服务器与副本服务器之间保持一致。 在 Azure Database for MySQL 中，该参数默认为 1。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

   可选 -如果想要使用[基于 GTID 的复制](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)，则需要检查源服务器上是否启用了 GTID。 可对源 MySQL 服务器执行以下命令，查看 gtid_mode 是否为 ON。

   ```sql
   show variables like 'gtid_mode';
   ```

   >[!IMPORTANT]
   > 所有服务器都将 gtid_mode 设置为默认值 OFF。 不需要专门在 MySQL 源服务器上启用 GTID 以设置数据传入复制。 如果已在源服务器上启用 GTID，则也可选择使用基于 GTID 的复制来设置 Azure Database for MySQL 单一服务器的数据传入复制。 可使用基于文件的复制为所有服务器设置数据传入复制，而不必考虑源服务器上的 gitd_mode 配置。

5. 创建新的复制角色并设置权限。

   在源服务器上创建一个配置有复制特权的用户帐户。 可通过 SQL 命令或 MySQL Workbench 等工具实现此目的。 考虑是否打算使用 SSL 进行复制，因为这需要在创建用户时指定。 请参阅 MySQL 文档，了解如何在源服务器上[添加用户帐户](https://dev.mysql.com/doc/refman/5.7/en/user-names.html)。

   在以下命令中，创建的新复制角色可从任何计算机访问源服务器，而不仅仅可从托管源服务器本身的计算机进行访问。 这可以通过在创建用户的命令中指定“syncuser@'%'”来完成。 请参阅 MySQL 文档，详细了解如何[指定帐户名称](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)。

   **SQL 命令**

   *使用 SSL 复制*

   如果所有用户连接都要求 SSL，请使用以下命令来创建用户：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *不使用 SSL 复制*

   如果并非所有连接都要求 SSL，请使用以下命令来创建用户：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中创建复制角色，请在“管理”面板中打开“用户和特权”面板，然后选择“添加帐户”。  

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="用户和特权":::

   在“登录名称”字段中键入用户名。

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="同步用户":::

   选择“管理角色”面板，然后从“全局特权”列表中选择“复制从属实例”。   然后，选择“应用”以创建复制角色。

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="复制从属实例":::

6. 将源服务器设置为只读模式。

   在开始转储数据库之前，需将服务器置于只读模式。 在只读模式下，源服务器将无法处理任何写入事务。 评估对业务的影响，根据需要在非高峰时间计划只读窗口。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. 获取二进制日志文件名和偏移量。

   运行 [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) 命令，确定当前的二进制日志文件名和偏移量。

   ```sql
    show master status;
   ```
   结果应如下所示。 请务必记下二进制文件名，以供在后续步骤中使用。

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="主机状态结果":::

## <a name="dump-and-restore-the-source-server"></a>转储并还原源服务器

1. 确定要将哪些数据库和表复制到 Azure Database for MySQL 并从源服务器执行转储。

    可以使用 mysqldump 从主服务器转储数据库。 有关详细信息，请参阅[转储和还原](concepts-migrate-dump-restore.md)。 不需转储 MySQL 库和测试库。

2. 可选 -如果想要使用[基于 gtid 的复制](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)，则需要确定在主服务器执行的最后一个事务的 GTID。 可使用以下命令，记下在主服务器上执行的最后一个事务的 GTID。

   ```sql
   show global variables like 'gtid_executed';
   ```

3. 将源服务器设置为读/写模式。

   转储数据库后，将 MySQL 源服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. 将转储文件还原到新服务器。

   将转储文件还原到在 Azure Database for MySQL 服务中创建的服务器。 请参阅[转储和还原](concepts-migrate-dump-restore.md)，了解如何将转储文件还原到 MySQL 服务器。 如果转储文件较大，请将它上传到副本服务器所在区域的 Azure 中的虚拟机。 将它从虚拟机还原到 Azure Database for MySQL 服务器。

5. 可选 - 注意 Azure Database for MySQL 上已还原服务器的 GTID，确保它与主服务器相同。 可使用以下命令，记下 Azure Database for MySQL 副本服务器上 GTID 清除值的 GTID。 gtid_purged 值应与步骤 2 中记下的主服务器上的 gtid_executed 值相同，以使基于 GTID 的复制可工作。

   ```sql
   show global variables like 'gtid_purged';
   ```

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>链接源服务器和副本服务器以启动“数据传入复制”

1. 设置源服务器。

   所有数据传入复制功能都是通过存储过程完成的。 可以在[数据传入复制存储过程](./reference-stored-procedures.md)中找到所有过程。 这些存储过程可以在 MySQL shell 或 MySQL Workbench 中运行。

   若要链接两个服务器并启动复制，请在 Azure DB for MySQL 服务中登录到目标副本服务器，并将外部实例设置为源服务器。 为此，可在 Azure DB for MySQL 服务器上使用 `mysql.az_replication_change_master` 存储过程。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   可选 - 如果想要使用[基于 gtid 的复制](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)，则需要使用以下命令链接这两个服务器

    ```sql
   call mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_ssl_ca>');
   ```

   - master_host：源服务器的主机名
   - master_user：源服务器的用户名
   - master_password：源服务器的密码
   - master_port：源服务器侦听连接的端口号。 （3306 是 MySQL 侦听的默认端口）
   - master_log_file：正在运行的 `show master status` 中的二进制日志文件名
   - master_log_pos：正在运行的 `show master status` 中的二进制日志位置
   - master_ssl_ca：CA 证书的上下文。 如果不使用 SSL，请传入空字符串。

     建议以变量形式传入此参数。 有关详细信息，请参阅以下示例。

   > [!NOTE]
   > 如果源服务器托管在 Azure VM 中，请将“允许访问 Azure 服务”设置为“启用”，以允许源服务器和副本服务器相互通信。 从“连接安全性”选项可更改此设置。 有关详细信息，请参阅[使用门户管理防火墙规则](howto-manage-firewall-using-portal.md)。

   **示例**

   *使用 SSL 复制*

   运行以下 MySQL 命令创建变量 `@cert`：

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   在域“companya.com”中托管的源服务器与 Azure Database for MySQL 中托管的副本服务器之间设置了使用 SSL 进行复制的功能。 将在副本上运行此存储过程。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *不使用 SSL 复制*

   在域“companya.com”中托管的源服务器与 Azure Database for MySQL 中托管的副本服务器之间设置了不使用 SSL 进行复制的功能。 将在副本上运行此存储过程。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. 设置筛选。

   如果要跳过从主副本复制某些表的操作，请更新副本服务器上的 `replicate_wild_ignore_table` 服务器参数。 可以使用逗号分隔的列表提供多个表模式。

   查看 [MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)详细了解此参数。

   若要更新该参数，可以使用 [Azure 门户](howto-server-parameters.md)或 [Azure CLI](howto-configure-server-parameters-using-cli.md)。

3. 启动复制。

   调用 `mysql.az_replication_start` 存储过程以启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

4. 检查复制状态。

   在副本服务器上调用 [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) 命令查看复制状态。

   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 状态为“yes”，并且 `Seconds_Behind_Master` 的值为“0”，则表示复制正常运行。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果其值不为“0”，则表示副本正在处理更新。

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>用于数据传入复制操作的其他有用的存储过程

### <a name="stop-replication"></a>停止复制

若要停止源服务器与副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>删除复制关系

若要删除源服务器与副本服务器之间的关系，请使用以下存储过程：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>跳过复制错误

若要跳过复制错误并允许复制继续，请使用以下存储过程：

```sql
CALL mysql.az_replication_skip_counter;
```

 可选 - 如果想要使用[基于 gtid 的复制](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)，需使用以下存储过程以跳过事务

```sql
call mysql. az_replication_skip_gtid_transaction(‘<transaction_gtid>’)
```

该过程可跳过给定 GTID 的事务。 如果 GTID 格式不正确或已执行 GTID 事务，则该过程将无法执行。 可通过分析二进制日志来检查事务事件，从而确定事务的 GTID。 MySQL 提供了一个实用工具 [mysqlbinlog](https://dev.mysql.com/doc/refman/5.7/en/mysqlbinlog.html)，用于分析二进制日志并以文本格式显示其内容，该工具可用于识别事务的 GTID。

若要跳过当前复制位置之后的下一个事务，使用以下命令来识别下一个事务的 GTID，如下所示。

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/howto-data-in-replication/show-binary-log.png" alt-text="显示二进制日志结果":::

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MySQL 的[向内复制数据](concepts-data-in-replication.md)。
