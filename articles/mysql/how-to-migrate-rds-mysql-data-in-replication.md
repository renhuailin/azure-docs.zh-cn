---
title: 使用数据传入复制将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL
description: 本文介绍如何使用数据传入复制将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL。
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: d05806b9d69543fdd4f5584661a6b8b74ac1f01e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647150"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>使用数据传入复制将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 从软件中删除该术语后，我们会将其从本文中删除。

可以使用 MySQL 转储和还原、MySQL Workbench 导出/导入或 Azure 数据库迁移服务 (DMS) 等各种方法，将 MySQL 数据库迁移到 Azure Database for MySQL。 但是，将 MySQL 转储和还原或 mydumper/myloader 等开源工具和数据传入复制结合使用，可以在尽量减少停机时间的情况下迁移工作负载。

数据传入复制是基于二进制日志文件定位方法将源服务器中的数据更改复制到目标服务器的技术。 在此方案中，作为源（数据库更改的来源位置）运行的 MySQL 实例将更新和更改作为“事件”写入二进制日志。 二进制日志中的信息根据所记录的数据库更改以不同的日志记录格式进行存储。 副本配置为从源读取二进制日志，并执行副本的本地数据库上的二进制日志中的事件。

如果设置了[数据传入复制](/azure/mysql/flexible-server/concepts-data-in-replication)以将源 MySQL 服务器中的数据同步到目标 MySQL 服务器，则可以选择性地将应用程序从主服务器（或源数据库）切换到副本服务器（或目标数据库）。

本教程介绍如何在运行 Amazon RDS for MySQL 的源服务器和运行 Azure Database for MySQL 的目标服务器之间设置数据传入复制。

## <a name="performance-considerations"></a>性能注意事项

在开始本教程之前，请务必考虑用于执行操作的客户端计算机的位置和容量对性能的影响。

### <a name="client-location"></a>客户端位置

从与数据库服务器位于同一位置启动的客户端计算机执行转储/还原操作：

- 对于 Azure Database for MySQL 服务器，客户端计算机应与目标数据库服务器位于同一 VNet 和同一可用性区域中。
- 对于源 Amazon RDS 数据库实例，客户端实例应与源数据库服务器位于同一 Amazon 虚拟私有云 (Amazon VPC) 和可用性区域中。
在上述情况下，可以使用文件传输协议（例如 FTP 或 SFTP）在客户端计算机之间移动转储文件，或将转储文件上传到 Azure Blob 存储。 为了减少总迁移时间，可以在传输文件之前对其进行压缩。

### <a name="client-capacity"></a>客户端容量

无论客户端计算机位于何处，它都需要足够的计算、I/O 和网络容量来执行请求的操作。 一般建议如下：

- 如果转储或还原涉及到数据的实时处理（例如压缩或解压缩），请选择至少为每个转储/还原线程提供一个 CPU 核心的实例类。
- 确保有足够的网络带宽可供客户端实例使用。 建议使用支持加速网络功能的实例类型。 有关详细信息，请参阅 [Azure 虚拟机网络指南](/azure/virtual-network/create-vm-accelerated-networking-cli)中的“加速网络”部分。
- 确保客户端计算机的存储层提供所需的读/写容量，我们建议使用具有高级 SSD 存储的 Azure 虚拟机。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- 在客户端计算机上安装 [mysqlclient](https://dev.mysql.com/downloads/)，以在目标 Azure Database for MySQL 服务器上创建转储并执行还原操作。
- 对于较大的数据库，请安装 [mydumper/myloader](https://centminmod.com/mydumper.html) 以并行转储和还原数据库。

    > [!NOTE]
    > Mydumper 只能在 Linux 发行版上运行，请参阅[如何安装 mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)。

- 创建运行版本 5.7 或 8.0 的 Azure Database for MySQL 服务器实例。

    > [!IMPORTANT]
    > 如果目标是具有区域冗余高可用性的 Azure Database for MySQL 灵活服务器，请注意此配置不支持数据传入复制。 解决方法是在创建服务器期间，使用以下步骤设置区域冗余 HA：
    >
    > 1. 创建已启用区域冗余 HA 的服务器。
    > 2. 禁用高可用性。
    > 3. 遵循本文设置数据传入复制。
    > 4. 切换后删除数据传入复制配置。
    > 5. 启用高可用性。

此外，建议确保正确配置并设置多个参数和功能，如下所述。

- 出于兼容性原因，建议将源和目标数据库服务器置于相同的 MySQL 版本上。
- 在每个表中提供主键。 表中缺少主键可能会减慢复制速度。
- 确保源数据库和目标数据库的字符集相同。
- 根据要导入或迁移的数据或工作负载数量，将 `wait_timeout` 参数设置为合理的时间。
- 验证所有表是否都使用 InnoDB，因为 Azure Database for MySQL Server 仅支持 InnoDB 存储引擎。
- 对于具有多个辅助索引的表或极大的表，性能开销所造成的影响在还原期间尤为明显。请修改转储文件，使 `CREATE TABLE` 语句不包括辅助键定义，并在导入数据后重新创建辅助索引，以避免在还原期间出现性能损失。

最后，若要准备数据传入复制，请执行以下操作：

- 验证目标 Azure Database for MySQL 服务器能否通过端口 3306 连接到源 Amazon RDS for MySQL 服务器。
- 确保源 Amazon RDS for MySQL 服务器允许端口 3306 上的入站和出站流量。
- 确保使用 [ExpressRoute](/azure/expressroute/expressroute-introduction) 或 [VPN 网关](/azure/vpn-gateway/vpn-gateway-about-vpngateways)来提供与源服务器的[站点到站点连接](/azure/vpn-gateway/tutorial-site-to-site-portal)。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](/azure/virtual-network/)，尤其是提供了分步详细信息的快速入门文章。
- 配置源数据库服务器的网络安全组，以允许目标 Azure Database for MySQL 的服务器 IP 地址。

> [!IMPORTANT]
> 如果源 Amazon RDS for MySQL 实例的 GTID_mode 设置为 ON，则 Azure Database for MySQL 灵活服务器目标实例的 GTID_mode 也必须设置为 ON。

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>配置 Azure Database for MySQL 的目标实例

若要配置 Azure Database for MySQL 的目标实例（数据传入复制的目标），请执行以下步骤：

1. 将 `max_allowed_packet` 参数值设置为最大值 1073741824（即 1 GB），这可以防止出现任何与长行相关的溢出问题。
2. 在迁移期间将 `slow_query_log`、`general_log`、`audit_log_enabled` 和 `query_store_capture_mode` 参数设置为 OFF，以帮助消除任何与查询日志记录相关的开销。
3. 将目标 Azure Database for MySQL 服务器的计算大小纵向扩展为最多 64 个 vCore，以便在从源服务器还原数据库转储时提供额外的计算资源。

    迁移完成后，始终可以根据应用程序需求缩减计算。

4. 纵向扩展存储大小，以便在迁移期间获得更多的 IOPS，或增加用于迁移的最大 IOPS。

   > [!NOTE]
   > 可用的最大 IOPS 取决于计算大小。 有关详细信息，请参阅 [Azure Database for MySQL 灵活服务器中的计算和存储选项](/azure/mysql/flexible-server/concepts-compute-storage#iops)中的“IOPS”部分。

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>配置源 Amazon RDS for MySQL 服务器

若要准备和配置 Amazon RDS 中托管的 MySQL 服务器（数据传入复制的“源”），请执行以下步骤。

1. 通过检查是否已启用自动备份来确认是否在源 Amazon RDS for MySQL 服务器上启用了二进制日志记录，或确保源 Amazon RDS for MySQL 服务器存在只读副本。

2. 确保源服务器上的二进制日志文件保留到在 Azure Database for MySQL 的目标实例上应用了更改为止。

    如果使用数据传入复制，则 Azure Database for MySQL 无需管理复制过程。

3. 若要检查源 Amazon RDS 服务器上的二进制日志保留期以确定二进制日志的保留小时数，请调用“mysql.rds_show_configuration”存储过程：

    ```
    mysql> call mysql.rds_show_configuration;
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | name                   | value | description                                                                                               |
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | binlog retention hours | 24    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
    | source delay           | 0     | source delay specifies replication delay in seconds between current instance and its master.              |
    | target delay           | 0     | target delay specifies replication delay in seconds between current instance and its future read-replica. |
    +------------------------+-------            +-----------------------------------------------------------------------------------------------------------+
    3 rows in set (0.00 sec)
    ```
4. 若要配置二进制日志保留期，请运行 `rds_set_configuration` 存储过程，以确保在源服务器上将二进制日志保留所需的时间，例如：

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours’, 96);
    ```

    如果你要创建转储，然后进行还原以快速与增量更改同步，则此操作很有帮助。

   > [!NOTE]
   > 确保有足够的磁盘空间用于根据定义的保留期在源服务器上存储二进制日志。

可通过两种方法从源 Amazon RDS for MySQL 服务器转储数据。 其中一种方法涉及到直接从源服务器转储数据，而另一种方法涉及到从 Amazon RDS for MySQL 只读副本捕获转储。

- 如果要直接从源服务器捕获数据转储，请执行以下步骤：

    1. 确保停止从应用程序写入几分钟，以获取事务性一致性数据转储。

       还可以暂时将 `read_only` 参数设置为值 1，以便在捕获数据转储时不会处理写入。

    2. 停止源服务器上的写入后，通过命令 `Mysql> Show master status;` 来收集二进制日志文件名称和偏移量。
    3. 保存这些值，以从 Azure Database for MySQL 服务器启动复制。
    4. 若要创建数据执行 mysqldump 的转储，请运行以下命令：

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- 如果无法停止源服务器上的写入，或者在源服务器上转储数据造成的性能影响不可接受，请执行以下步骤来捕获副本服务器上的转储：

    1. 使用与源服务器相同的配置创建 Amazon MySQL 只读副本，然后在该副本中创建转储。
    2. 让 Amazon RDS for MySQL 只读副本与源 Amazon RDS for MySQL 服务器同步。
    3. 如果只读副本上的副本滞后时间达到 0，请调用 `mysql.rds_stop_replication` 存储过程停止复制。

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    4. 复制停止后，连接到副本，然后运行 `SHOW SLAVE STATUS` 命令从 Relay_Master_Log_File 字段检索当前二进制日志文件名称，并从 Exec_Master_Log_Pos 字段检索日志文件位置 。
    5. 保存这些值，以从 Azure Database for MySQL 服务器启动复制。
    6. 若要从 Amazon RDS for MySQL 只读副本创建数据转储，请运行以下命令执行 mysqldump：

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > 还可以使用 mydumper 从源 Amazon RDS for MySQL 数据库创建数据的并行化转储。 有关详细信息，请参阅[使用 mydumper/myloader 将大型数据库迁移到 Azure Database for MySQL](/azure/mysql/concepts-migrate-mydumper-myloader)。

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>链接源服务器和副本服务器以启动数据传入复制

1. 若要使用 mysql 本机还原来还原数据库，请运行以下命令：

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > 如果要改用 myloader，请参阅[使用 mydumper/myloader 将大型数据库迁移到 Azure Database for MySQL](/azure/mysql/concepts-migrate-mydumper-myloader)。

2. 登录到源 Amazon RDS for MySQL 服务器，设置复制用户，然后向此用户授予所需的特权。

    - 如果使用 SSL，请运行以下命令：

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - 如果未使用 SSL，请运行以下命令：

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    所有数据传入复制函数均由存储过程执行。 有关所有过程的信息，请参阅[数据传入复制存储过程](/azure/mysql/reference-stored-procedures#data-in-replication-stored-procedures)。 可以在 MySQL shell 或 MySQL Workbench 中运行这些存储过程。

3. 若要链接 Amazon RDS for MySQL 源服务器和 Azure Database for MySQL 目标服务器，请登录到目标 Azure Database for MySQL 服务器，并运行以下命令将 Amazon RDS for MySQL 服务器设置为源服务器：

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

4. 若要在源 Amazon RDS for MySQL 服务器和目标 Azure Database for MySQL 服务器之间开始复制，请运行以下命令：

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

5. 若要检查复制状态，请在副本服务器上运行以下命令：

    ```
    Mysql> show slave status\G
    ```

    如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 参数的状态为 Yes，则表示复制已启动并处于运行中状态。

6. 检查 `Seconds_Behind_Master` 参数的值以确定目标服务器的延迟程度。

    如果该值为 0，则表示目标已处理来自源服务器的所有更新。 如果该值不为 0，则表示目标服务器仍在处理更新。

## <a name="ensure-a-successful-cutover"></a>确保切换成功

为确保切换成功，请执行以下任务：

1. 在目标 Azure Database for MySQL 服务器中配置适当的登录名和数据库级权限。
2. 停止向源 Amazon RDS for MySQL 服务器的写入。
3. 确保目标 Azure Database for MySQL 服务器已与源服务器同步，并且 `show slave status` 返回的 `Seconds_Behind_Master` 值为 0。
4. 调用存储过程 `mysql.az_replication_stop` 以停止复制，因为所有更改已复制到目标 Azure Database for MySQL 服务器。
5. 调用 `mysql.az_replication_remove_master` 以删除数据传入复制配置。
6. 将客户端和客户端应用程序重定向到目标 Azure Database for MySQL 服务器。

此时迁移已完成，你的应用程序已连接到运行 Azure Database for MySQL 的服务器。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将数据库迁移到 Azure Database for MySQL，请参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。
- 观看视频[轻松将 MySQL/PostgreSQL 应用迁移到 Azure 托管服务](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)，其中包含一个演示，演示如何将 MySQL 应用迁移到 Azure Database for MySQL。
