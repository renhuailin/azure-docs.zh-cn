---
title: 教程：在尽量减少停机时间的情况下将 Azure Database for MySQL - 单一服务器迁移到 Azure Database for MySQL - 灵活服务器
description: 本文介绍如何在尽量减少停机时间的情况下将 Azure Database for MySQL - 单一服务器迁移到 Azure Database for MySQL - 灵活服务器。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: cbabc94f1aa8a2dcd8d8022ab81b7d772006b574
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203468"
---
# <a name="tutorial-migrate-azure-database-for-mysql--single-server-to-azure-database-for-mysql--flexible-server-with-minimal-downtime"></a>教程：在尽量减少停机时间的情况下将 Azure Database for MySQL - 单一服务器迁移到 Azure Database for MySQL - 灵活服务器

可以将 mydumper/myloader 等开源工具组合和数据传入复制一起使用，在尽量减少应用程序停机时间的情况下，将 Azure Database for MySQL – 单一服务器实例迁移到 Azure Database for MySQL – 灵活服务器。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。

数据传入复制是基于二进制日志文件定位方法将源服务器中的数据更改复制到目标服务器的技术。 在此方案中，作为源（数据库更改的来源位置）运行的 MySQL 实例将更新和更改作为“事件”写入二进制日志。 二进制日志中的信息根据所记录的数据库更改以不同的日志记录格式进行存储。 副本配置为从源读取二进制日志，并执行副本的本地数据库上的二进制日志中的事件。

如果设置了数据传入复制以将一个 Azure Database for MySQL 实例中的数据同步到另一个实例，则可以选择性地将应用程序从主服务器（或源数据库）切换到副本服务器（或目标数据库）。

在本教程中，你将使用 mydumper/myloader 和数据传入复制将示例数据库 ([classicmodels](https://www.mysqltutorial.org/mysql-sample-database.aspx)) 从 Azure Database for MySQL - 单一服务器实例迁移到 Azure Database for MySQL - 灵活服务器实例，然后同步数据。

在本教程中，你将了解：

* 如何为不同的方案配置用于数据传入复制的网络设置。
* 如何配置主服务器与副本服务器之间的数据传入复制。
* 如何测试复制。
* 如何进行切换以完成迁移。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 运行版本 5.7 或 8.0 的 Azure Database for MySQL 单一服务器实例。
    > [!Note]
    > 如果运行的是 Azure Database for MySQL 单一服务器版本 5.6，请将实例升级到 5.7，然后配置数据传入复制。 有关详细信息，请参阅 [Azure Database for MySQL - 单一服务器中的主版本升级](how-to-major-version-upgrade.md)。
* 一个 Azure Database for MySQL 灵活服务器实例。 有关详细信息，请参阅[在 Azure Database for MySQL 灵活服务器中创建实例](./flexible-server/quickstart-create-server-portal.md)一文。
    > [!Note]
    > 不支持为区域冗余的高可用性服务器配置数据传入复制。 若要为目标服务器实现区域冗余的高可用性，请执行以下步骤：
    >
    > 1. 创建已启用区域冗余高可用性的服务器
    > 2. 禁用高可用性
    > 3. 遵循本文设置数据传入复制
    > 4. 切换后删除数据传入复制配置
    > 5. 启用高可用性
    >
    > 确保源和目标服务器上的 [GTID_Mode](./concepts-read-replicas.md#global-transaction-identifier-gtid) 具有相同的设置。

* 使用 MySQL Workbench 连接和创建数据库。 有关详细信息，请参阅[使用 MySQL Workbench 连接和查询数据](./flexible-server/connect-workbench.md)一文。
* 确保在托管源和目标数据库的同一区域中（或者在可进行私密访问的同一 VNet 中）有一个运行 Linux 的 Azure VM。
* 在 Azure VM 上安装 mysql 客户端或 MySQL Workbench（客户端工具）。 确保可以连接到主服务器和副本服务器。 若要实现本文的目的，需要安装有 mysql 客户端。
* 在 Azure VM 上安装 mydumper/myloader。 有关详细信息，请参阅 [mydumper/myloader](concepts-migrate-mydumper-myloader.md) 一文。
* 为源服务器上的 [classicmodels](https://www.mysqltutorial.org/wp-content/uploads/2018/03/mysqlsampledatabase.zip) 数据库下载并运行示例数据库脚本。

## <a name="configure-networking-requirements"></a>配置网络要求

若要配置数据传入复制，需确保目标可以通过端口 3306 连接到源。 根据源上设置的终结点类型执行以下相应步骤。

* 如果在源上启用了公共终结点，请在防火墙规则中启用“允许访问 Azure 服务”，确保目标可以连接到源。 有关详细信息，请参阅[防火墙规则 - Azure Database for MySQL](./concepts-firewall-rules.md#connecting-from-azure)。 
* 如果在源上启用了专用终结点和“[拒绝公共访问](concepts-data-access-security-private-link.md#deny-public-access-for-azure-database-for-mysql)”，请在托管目标的同一 VNet 中安装专用链接。 有关详细信息，请参阅[专用链接 - Azure Database for MySQL](concepts-data-access-security-private-link.md)。

## <a name="configure-data-in-replication"></a>配置数据传入复制

若要配置数据传入复制，请执行以下步骤：

1. 登录到安装了 mysql 客户端工具的 Azure VM。

2. 使用 mysql 客户端工具连接到源和目标。

3. 运行以下命令，使用 mysql 客户端工具确定是否在源上启用了 log_bin：

    ```sql
    SHOW VARIABLES LIKE 'log_bin';
    ```

    > [!Note]
    > 在具有较大存储的 Azure Database for MySQL 单一服务器（最多支持 16TB）中，默认已启用 log_bin。

    > [!Tip]
    > 在 Azure Database for MySQL 单一服务器（最多支持 4TB）中，默认未启用 log_bin。 但是，如果提升源服务器的[只读副本](howto-read-replicas-portal.md)，然后删除只读副本，则该参数将设置为 ON。

4. 根据源服务器的 SSL 强制设置，通过运行相应的命令在源服务器中创建拥有复制权限的用户。

    如果使用 SSL，请运行以下命令：

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
    ```

    如果未使用 SSL，请运行以下命令：

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
    ```

5. 若要使用 mydumper 备份数据库，请在安装了 mydumper\myloader 的 Azure VM 上运行以下命令：

    ```bash
    $ mydumper --host=<primary_server>.mysql.database.azure.com --user=<username>@<primary_server> --password=<Password> --outputdir=./backup --rows=100 -G -E -R -z --trx-consistency-only --compress --build-empty-files --threads=16 --compress-protocol --ssl  --regex '^(classicmodels\.)' -L mydumper-logs.txt
    ```

    > [!Tip]
    > 若要在创建备份时保持事务一致性，必须指定选项 --trx-consistency-only。
    >
    > * 该 mydumper 选项等效于 mysqldump 的 --single-transaction。
    > * 如果所有表都是 InnoDB，则此选项十分有用。
    > * “main”线程只需将全局锁保留到“dump”线程可以启动事务为止。
    > * 提供全局锁定的最短持续时间

    “main”线程只需将全局锁保留到“dump”线程可以启动事务为止。

    下面解释了此命令中的变量：

    * --host：主服务器的名称
    * --user：用户名（由于主服务器正在运行 Azure Database for MySQL - 单一服务器，因此格式为 username@servername）。 可以使用服务器管理员或拥有 SELECT 和 RELOAD 权限的用户。
    * --Password：上述用户的密码

    有关使用 mydumper 的详细信息，请参阅 [mydumper/myloader](concepts-migrate-mydumper-myloader.md)

6. 通过运行以下命令，读取元数据文件以确定二进制日志文件名和偏移量：

    ```bash
    $ cat ./backup/metadata 
    ```

    在此命令中，./backup 引用在上一步骤所述的命令中使用的输出目录。

    结果应如下图所示：

    :::image type="content" source="./media/howto-migrate-single-flexible-minimum-downtime/metadata.png" alt-text="使用 Azure 数据库迁移服务进行连续同步":::

    请务必记下二进制文件名，以供在后续步骤中使用。

7. 通过运行以下命令，使用 myloader 还原数据库：

    ```bash
    $ myloader --host=<servername>.mysql.database.azure.com --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=100 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

    下面解释了此命令中的变量：

    * --host：副本服务器的名称
    * --user：用户名。 可以使用服务器管理员，或者拥有读/写权限的、能够将架构和数据还原到数据库的用户
    * --Password：上述用户的密码

8. 根据主服务器上的 SSL 强制设置，使用 mysql 客户端工具连接到副本服务器并执行以下步骤。

    * 如果已启用 SSL 强制，请执行以下操作：

        i. 从[此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)下载通过 SSL 与 Azure Database for MySQL 服务器通信所需的证书。

        ii. 在记事本中打开证书文件，将内容粘贴到“PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE”节中。

        ```sql
        SET @cert = '-----BEGIN CERTIFICATE-----
        PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE 
        -----END CERTIFICATE-----'
        ```

        iii. 若要配置数据传入复制，请运行以下命令：

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '=<username>@<primary_server>', '<Password>, 3306, '<File_Name>', <Position>, @cert);
        ```

        > [!Note]
        > 根据在步骤 6 中获取的信息确定位置和文件名。

    * 如果未启用 SSL 强制，请运行以下命令：

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '=<username>@<primary_server>', '<Password>, 3306, '<File_Name>', <Position>, ‘’);
        ```

9. 若要从副本服务器启动复制，请调用以下存储过程。

    ```sql
    call  mysql.az_replication_start;
    ```

10. 若要检查复制状态，请在副本服务器上运行以下命令：

     ```sql
     show slave status \G; 
     ```

    > [!Note]
    > 如果使用的是 MySQL Workbench，则 \G 修饰符不是必需的。

    如果 Slave_IO_Running 和 Slave_SQL_Running 的状态为 Yes，Seconds_Behind_Master 的值为 0，则表示复制工作正常  。 Seconds_Behind_Master 指示副本的滞后程度。 如果值不为 0，则表示副本正在处理更新。

## <a name="testing-the-replication-optional"></a>测试复制（可选）

若要确认数据传入复制是否正常工作，可以验证对主服务器中的表所做的更改是否已复制到副本。

1. 指定一个要用于测试的表（例如 Customers 表），然后在主服务器和副本服务器上运行以下命令，确认这两个服务器上该表包含的条目数是否相同：

    ```
    select count(*) from customers;
    ```

2. 记下条目计数以便稍后进行比较。

    若要测试复制，请尝试将一些数据添加到主服务器上的客户表，然后确认是否复制了新数据。 在本例中，你会将两行添加到主服务器上的表，然后确认这两行是否已复制到副本服务器。

3. 在主服务器上的 Customers 表中，运行以下命令以插入行：

    ```sql
    insert  into `customers`(`customerNumber`,`customerName`,`contactLastName`,`contactFirstName`,`phone`,`addressLine1`,`addressLine2`,`city`,`state`,`postalCode`,`country`,`salesRepEmployeeNumber`,`creditLimit`) values 
    (<ID>,'name1','name2','name3 ','11.22.5555','54, Add',NULL,'Add1',NULL,'44000','country',1370,'21000.00');
    ```

4. 若要检查复制状态，请调用 show slave status \G 以确认复制是否按预期方式进行。

5. 若要确认计数是否相同，请在副本服务器上运行以下命令：

    ```sql
    select count(*) from customers;
    ```

## <a name="ensure-a-successful-cutover"></a>确保切换成功

为确保切换成功，请执行以下任务：

1. 配置适当的服务器级防火墙和虚拟网络规则以连接到目标服务器。 可以在门户中比较[源](howto-manage-firewall-using-portal.md)和[目标](./flexible-server/how-to-manage-firewall-portal.md#create-a-firewall-rule-after-server-is-created)的防火墙规则。
2. 在目标服务器中配置适当的登录名和数据库级权限。 可以在源和目标服务器上运行 SELECT * FROM mysql.user; 进行比较。
3. 确保 Azure Database for MySQL 单一服务器的所有传入连接已停止。
    > [!Tip]
    > 可将 Azure Database for MySQL 单一服务器设置为只读。
4. 运行 show slave status \G 并确认 Seconds_Behind_Master 参数值为 0，确保副本服务器与主服务器已同步 。
5. 将客户端和客户端应用程序重定向到 Azure Database for MySQL 灵活服务器的目标实例。
6. 运行 mysql.az_replication_stop 存储过程（这会停止从副本服务器的复制）来执行最终切换。
7. 调用 mysql.az_replication_remove_master 以删除数据传入复制配置。

此时，应用程序已连接到新的 Azure Database for MySQL 灵活服务器，并且源中的更改将不再复制到目标。

## <a name="next-steps"></a>后续步骤

* 在[将数据复制到 Azure Database for MySQL 灵活服务器](flexible-server/concepts-data-in-replication.md)和[配置 Azure Database for MySQL 灵活服务器数据传入复制](./flexible-server/how-to-data-in-replication.md)中详细了解数据传入复制
* 详细了解如何[排查 Azure Database for MySQL 中的常见错误](howto-troubleshoot-common-errors.md)。
* 详细了解如何[使用 Azure 数据库迁移服务将 MySQL 脱机迁移到 Azure Database for MySQL](../dms/tutorial-mysql-azure-mysql-offline-portal.md)。
