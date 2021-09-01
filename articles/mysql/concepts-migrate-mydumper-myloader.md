---
title: 使用 mydumper/myloader 将大型数据库迁移到 Azure Database for MySQL
description: 本文介绍使用 mydumper/myloader 工具在 Azure Database for MySQL 中备份和还原数据库的两种常用方法
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e295e967b9164e9ab4744d18f407a18feb32481e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323169"
---
# <a name="migrate-large-databases-to-azure-database-for-mysql-using-mydumpermyloader"></a>使用 mydumper/myloader 将大型数据库迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL 是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 MySQL 数据库。 若要将大于 1 TB 的 MySQL 数据库迁移到 Azure Database for MySQL，请考虑使用 [mydumper/myloader](https://centminmod.com/mydumper.html) 之类的社区工具，这些工具可提供以下优势：

* 提供并行度来帮助缩短迁移时间。
* 通过避免高开销的字符集转换例程来提高性能。
* 输出格式支持为表、元数据等内容提供单独的文件，以便于查看/分析数据。 通过维护所有线程的快照来保持一致性。
* 准确的主日志和副本日志位置。
* 易于管理，因为它们支持 Perl 兼容的正则表达式 (PCRE)，允许指定数据库和表包含项与排除项。
* 架构与数据共置在一起。 不需要像在其他逻辑迁移工具中一样单独对其进行处理。

本快速入门将介绍如何使用 mydumper/myloader 来安装、备份和还原 MySQL 数据库。

## <a name="prerequisites"></a>先决条件

在开始迁移 MySQL 数据库之前，需要：

1. 使用 [Azure 门户](./flexible-server/quickstart-create-server-portal.md)创建一个 Azure Database for MySQL 服务器。

2. 使用 [Azure 门户](../virtual-machines/linux/quick-create-portal.md)创建一个运行 Linux 的 Azure VM（最好是 Ubuntu）。
    > [!Note]
    > 在安装这些工具之前，请注意以下几点：
    >
    > * 如果源位于本地并且与 Azure 建立了高带宽连接（使用 ExpressRoute），请考虑在 Azure VM 上安装该工具。<br> 
    > * 如果源与目标之间的带宽是个问题，请考虑将 mydumper 安装在靠近源的位置，将 myloader 安装在靠近目标服务器的位置。 可以使用 [Azcopy](../storage/common/storage-use-azcopy-v10.md) 工具将数据从本地或其他云解决方案移到 Azure。

3. 执行以下步骤安装 mysql 客户端：

4. 

    * 运行以下命令，更新运行 Linux 的 Azure VM 上的包索引：
        ```bash
        $ sudo apt update
        ```
    * 运行以下命令来安装 mysql 客户端包：
        ```bash
        $ sudo apt install mysql-client
        ```

## <a name="install-mydumpermyloader"></a>安装 mydumper/myloader

若要安装 mydumper/myloader，请执行以下步骤。

1. 根据你的 OS 发行版，运行以下命令下载 mydumper/myloader 的相应包：
2. 
    ```bash
    $ wget https://github.com/maxbube/mydumper/releases/download/v0.10.1/mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Note]
    > $(lsb_release -cs) 可帮助识别你的发行版。

3. 若要安装 mydumper 的 .deb 包，请运行以下命令：

    ```bash
    $ dpkg -i mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Tip]
    > 用于安装该包的命令根据所用的 Linux 发行版而异，因为安装程序因发行版的不同而不同。 mydumper/myloader 适用于以下发行版：Fedora、RedHat、Ubuntu、Debian、CentOS、openSUSE 和 MacOSX。 有关详细信息，请参阅[如何安装 mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)

## <a name="create-a-backup-using-mydumper"></a>使用 mydumper 创建备份

* 若要使用 mydumper 创建备份，请运行以下命令：

    ```bash
    $ mydumper --host=<servername> --user=<username> --password=<Password> --outputdir=./backup --rows=100000 --compress --build-empty-files --threads=16 --compress-protocol --trx-consistency-only --ssl  --regex '^(<Db_name>\.)' -L mydumper-logs.txt
    ```

此示例使用以下变量：

* --host：要连接到的主机
* --user：拥有必要特权的用户名 
* --password：用户密码
* --rows：尝试将表拆分为包含这么多行的区块
* --outputdir：将输出文件转储到的目录
* --regex：用于进行数据库匹配的正则表达式。
* --trx-consistency-only：仅保持事务一致性
* --threads：要使用的线程数，默认值为 4。 建议使用等于计算机 vCore 数 2 倍的值。

    >[!Note] 
    >有关可在 mydumper 中使用的其他选项的详细信息，请运行以下命令：mydumper --help。 有关详细信息，请参阅 [mydumper\myloader 文档](https://centminmod.com/mydumper.html)<br>
    若要同时转储多个数据库，可按以下示例中所示修改正则表达式变量：regex ’^(DbName1\.|DbName2\.)

## <a name="restore-your-database-using-myloader"></a>使用 myloader 还原数据库

* 若要还原使用 mydumper 备份的数据库，请运行以下命令：

    ```bash
    $ myloader --host=<servername> --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=500 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

此示例使用以下变量：

* --host：要连接到的主机
* --user：拥有必要特权的用户名 
* --password：用户密码
* --directory：存储备份到的位置。 
* --queries-per-transaction：建议设置为不大于 500 的值
* --threads：要使用的线程数，默认值为 4。 建议使用等于计算机 vCore 数 2 倍的值

> [!Tip]
> 有关可在 myloader 中使用的其他选项的详细信息，请运行以下命令：myloader --help

还原数据库后，始终建议验证源数据库与目标数据库之间的数据一致性。

> [!Note]
> 可在[此处](https://github.com/maxbube/mydumper/issues)提交有关 mydumper/myloader 工具的任何问题或反馈。

## <a name="next-steps"></a>后续步骤

* 详细了解 [GitHub 中的 mydumper/myloader 项目](https://github.com/maxbube/mydumper)。
* 了解[如何迁移大型 MySQL 数据库](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)。
* [教程：在尽量减少停机时间的情况下将 Azure Database for MySQL - 单一服务器迁移到 Azure Database for MySQL - 灵活服务器](howto-migrate-single-flexible-minimum-downtime.md)
* 在[将数据复制到 Azure Database for MySQL 灵活服务器](flexible-server/concepts-data-in-replication.md)和[配置 Azure Database for MySQL 灵活服务器数据传入复制](./flexible-server/how-to-data-in-replication.md)中详细了解数据传入复制
* 经常遇到的[迁移错误](./howto-troubleshoot-common-errors.md)