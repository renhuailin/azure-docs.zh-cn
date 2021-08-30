---
title: 配置数据传入复制 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何为 Azure Database for MySQL 灵活服务器设置数据传入复制。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: ee0bafdfe7d7caae2d4ba65e9967d9c46e6b3e3c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736436"
---
# <a name="how-to-configure-azure-database-for-mysql-flexible-server-data-in-replication"></a>如何配置 Azure Database for MySQL 灵活服务器数据传入复制

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

本文介绍如何通过配置源服务器和副本服务器在 Azure Database for MySQL 灵活服务器中设置[数据传入复制](concepts-data-in-replication.md)。 本文假设读者在 MySQL 服务器和数据库方面有一定的经验。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。

若要在 Azure Database for MySQL 灵活服务中创建副本，[数据传入复制](concepts-data-in-replication.md)需同步本地 MySQL 源服务器、虚拟机 (VM) 或云数据库服务中的数据。 数据传入复制依靠的是基于二进制日志 (binlog) 文件位置的复制。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

在执行本文中的步骤之前，请查看数据传入复制的[限制和要求](concepts-data-in-replication.md#limitations-and-considerations)。

## <a name="create-an-azure-database-for-mysql-flexible-server-instance-to-use-as-a-replica"></a>创建要用作副本的 Azure Database for MySQL 灵活服务器实例

1. 创建一个新的 Azure Database for MySQL 灵活服务器实例（例如 “replica.mysql.database.azure.com”）。 参阅[使用 Azure 门户创建 Azure Database for MySQL 灵活服务器服务器](quickstart-create-server-portal.md)，了解如何创建服务器。 此服务器是数据传入复制的“副本”服务器。

2. 创建相同的用户帐户和对应的特权。

   用户帐户不会从源服务器复制到副本服务器。 如果打算为用户提供访问副本服务器的权限，则需在这个新创建的 Azure Database for MySQL 灵活服务器上手动创建所有帐户和相应的特权。

## <a name="configure-the-source-mysql-server"></a>配置源 MySQL 服务器

以下步骤准备并配置本地或虚拟机中托管的 MySQL 服务器或其他云提供程序托管的数据库服务，以便进行数据传入复制。 该服务器是数据传入复制的“源”。

1. 请先查看[源服务器要求](concepts-data-in-replication.md#requirements)，然后再继续。

2. 网络要求 
    * 请确保源服务器允许端口 3306 上的入站和出站流量，并且源服务器具有公共 IP 地址，DNS 可供公开访问，或者 DNS 具有完全限定的域名 (FQDN)。

    * 如果使用的是专用访问，请确保源服务器与托管副本服务器的 Vnet 之间具有连接。 
    * 请确保使用 [ExpressRoute](../../expressroute/expressroute-introduction.md) 或 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 提供与本地源服务器的站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。
    * 如果在副本服务器中使用专用访问，并且源是 Azure VM，请确保已建立 VNet 到 VNet 的连接。 支持 VNet-Vnet 对等互连。 也可以使用其他连接方法在跨不同区域的 VNet 之间进行通信，例如 VNet 到 VNet 连接。 有关详细信息，请参阅 [VNet 到 VNet VPN 网关](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
    * 确保虚拟网络网络安全组规则不会阻止出站端口 3306（如果 MySQL 在 Azure VM 上运行，也请确保不会阻止入站端口）。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
    * 将源服务器的防火墙规则配置为允许副本服务器 IP 地址。

  
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

   4. 重启源服务器上的 MySQL 服务（或重启）以使更改生效。
   5. 重启服务器后，运行与之前相同的查询来验证是否已启用二进制日志记录：

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. 配置源服务器设置。

   数据传入复制要求参数 `lower_case_table_names` 在源服务器与副本服务器之间保持一致。 在 Azure Database for MySQL 灵活服务器中，此参数默认为 1。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

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

   :::image type="content" source="./media/how-to-data-in-replication/users-privileges.png" alt-text="用户和特权":::

   在“登录名称”字段中键入用户名。

   :::image type="content" source="./media/how-to-data-in-replication/sync-user.png" alt-text="同步用户":::

   选择“管理角色”面板，然后从“全局特权”列表中选择“复制从属实例”。   然后，选择“应用”以创建复制角色。

   :::image type="content" source="./media/how-to-data-in-replication/replication-slave.png" alt-text="复制从属实例":::

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

   :::image type="content" source="./media/how-to-data-in-replication/master-status.png" alt-text="主机状态结果":::

## <a name="dump-and-restore-the-source-server"></a>转储并还原源服务器

1. 确定要将哪些数据库和表复制到 Azure Database for MySQL 灵活服务器并从源服务器执行转储。

    可以使用 mysqldump 从主服务器转储数据库。 有关详细信息，请参阅[转储和还原](../concepts-migrate-dump-restore.md)。 不需转储 MySQL 库和测试库。


3. 将源服务器设置为读/写模式。

   转储数据库后，将 MySQL 源服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. 将转储文件还原到新服务器。

   将转储文件还原到在 Azure Database for MySQL 灵活服务器服务中创建的服务器。 请参阅[转储和还原](../concepts-migrate-dump-restore.md)，了解如何将转储文件还原到 MySQL 服务器。 如果转储文件较大，请将它上传到副本服务器所在区域的 Azure 中的虚拟机。 将转储文件从虚拟机还原到 Azure Database for MySQL 灵活服务器服务器。


## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>链接源服务器和副本服务器以启动数据传入复制

1. 设置源服务器。

   所有数据传入复制函数均由存储过程执行。 可以在[数据传入复制存储过程](../reference-stored-procedures.md)中找到所有过程。 这些存储过程可以在 MySQL shell 或 MySQL Workbench 中运行。

   若要链接两个服务器并启动复制，请在 Azure DB for MySQL 服务中登录到目标副本服务器，并将外部实例设置为源服务器。 为此，可在 Azure DB for MySQL 服务器上使用 `mysql.az_replication_change_master` 存储过程。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
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
   > 如果源服务器托管在 Azure VM 中，请将“允许访问 Azure 服务”设置为“启用”，以允许源服务器和副本服务器相互通信。 从“连接安全性”选项可更改此设置。 有关详细信息，请参阅[使用门户管理防火墙规则](how-to-manage-firewall-portal.md)。

   **示例**

   *使用 SSL 复制*

   运行以下 MySQL 命令创建变量 `@cert`：

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   在域“companya.com”中托管的源服务器与 Azure Database for MySQL 灵活服务器中托管的副本服务器之间设置了使用 SSL 进行复制的功能。 将在副本上运行此存储过程。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *不使用 SSL 复制*

   在域“companya.com”中托管的源服务器与 Azure Database for MySQL 灵活服务器中托管的副本服务器之间设置了不使用 SSL 进行复制的功能。 将在副本上运行此存储过程。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```
2. 启动复制。

   调用 `mysql.az_replication_start` 存储过程以启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 检查复制状态。

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

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/how-to-data-in-replication/show-binary-log.png" alt-text="显示二进制日志结果":::

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MySQL 灵活服务器的[数据传入复制](concepts-data-in-replication.md)。
