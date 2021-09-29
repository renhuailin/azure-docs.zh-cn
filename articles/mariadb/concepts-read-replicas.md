---
title: 只读副本 - Azure Database for MariaDB
description: 了解 Azure Database for MariaDB 中的只读副本：选择区域、创建副本、连接到副本、监视复制和停止复制。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 3b7306555e59759f79ffad8e984f0688820247de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641287"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的只读副本

使用只读副本功能可将数据从 Azure Database for MariaDB 服务器复制到只读服务器。 可将源服务器中的数据复制到最多 5 个副本。 使用全局事务 ID (GTID) 通过 MariaDB 引擎的基于二进制日志 (binlog) 文件位置的复制技术以异步方式更新副本。 要详细了解 binlog 复制，请参阅 [binlog 复制概述](https://mariadb.com/kb/en/library/replication-overview/)。

副本是新的服务器，可以像管理普通的 Azure Database for MariaDB 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

若要详细了解 GTID 复制，请参阅 [MariaDB 复制文档](https://mariadb.com/kb/en/library/gtid/)。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本

只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 “读取”工作负载可以隔离到副本，而“写入”工作负载可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，因此它们不能直接减少主服务器上的写入容量负担。 此功能并非面向写入密集型工作负荷。

只读副本功能使用异步复制技术。 该功能不适用于同步复制方案。 源与副本之间将会存在明显的延迟。 副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

## <a name="cross-region-replication"></a>跨区域复制

可以在与源服务器不同的区域中创建只读副本。 跨区域复制对于灾难恢复规划或使数据更接近用户等方案非常有用。

可以在任何 [Azure Database for MariaDB 区域](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)中拥有源服务器。  源服务器可以在其配对区域或通用副本区域中拥有副本。 下图显示了哪些副本区域可用，具体取决于你的源区域。

[![只读副本区域](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>通用副本区域

无论源服务器位于何处，你都可以在以下任何区域中创建只读副本。 支持的通用副本区域包括：

澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、东亚、美国东部、美国东部 2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、西欧、美国西部、美国西部 2、美国中西部。

### <a name="paired-regions"></a>配对区域

除通用副本区域外，还可以在源服务器的 Azure 配对区域中创建只读副本。 如果你不知道所在区域的配对，可以从 [Azure 配对区域](../best-practices-availability-paired-regions.md)一文中了解更多信息。

如果你使用跨区域副本进行灾难恢复规划，建议你在配对区域而不是其他某个区域中创建副本。 配对区域可避免同时更新，并优先考虑物理隔离和数据驻留。  

但是，需要考虑以下限制： 

* 区域可用性：Azure Database for MariaDB 可在法国中部、阿拉伯联合酋长国北部和德国中部使用。 但是，它们的配对区域不可用。

* 单向对：某些 Azure 区域仅在一个方向上配对。 这些区域包括印度西部、巴西南部和 US Gov 弗吉尼亚州。 
   这意味着，印度西部的源服务器可以在印度南部创建副本。 但是，印度南部的源服务器无法在印度西部创建副本。 这是因为印度西部的次要区域是印度南部，但印度南部的次要区域不是印度西部。

## <a name="create-a-replica"></a>创建副本

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MariaDB 服务器。 请确保源服务器位于其中一个定价层中。

如果源服务器没有现有的副本服务器，该源会先重启，以自行准备复制。

启动“创建副本”工作流时，将创建空白的 Azure Database for MariaDB 服务器。 新服务器中会填充源服务器上的数据。 创建时间取决于源服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

> [!NOTE]
> 如果尚未在服务器上设置存储警报，我们建议进行设置。 当服务器即将达到其存储限制（这会影响复制）时，警报可以向你发出通知。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本

创建时，副本会继承源服务器的防火墙规则。 之后，这些规则将独立于源服务器。

副本会从源服务器继承管理员帐户。 源服务器上的所有用户帐户都会复制到只读副本。 只能通过使用源服务器上可用的用户帐户来连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for MariaDB 服务器上连接一样。 对于名称为 **myreplica**、管理员用户名为 **myadmin** 的服务器，可以使用 mysql CLI 连接到副本：

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制

Azure Database for MariaDB 在 Azure Monitor 中提供“复制滞后时间(秒)”指标。 此指标仅适用于副本。

此指标是使用 MariaDB 的 `SHOW SLAVE STATUS` 命令中提供的 `seconds_behind_master` 指标计算得出的。

请设置警报，以便在复制滞后时间达到工作负荷不可接受的值时收到通知。

## <a name="stop-replication"></a>停止复制

可以停止源与副本之间的复制。 在源服务器与只读副本之间停止复制后，该副本会成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本上可用的数据。 独立服务器不会与源服务器保持同步。

选择停止复制到副本时，它会丢失指向其以前的源和其他副本的所有链接。 在源与其副本之间无法自动进行故障转移。

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

了解如何[停止复制到副本](howto-read-replicas-portal.md)。

## <a name="failover"></a>故障转移

在源服务器和副本服务器之间无法自动进行故障转移。

由于复制是异步的，因此在源和副本之间存在滞后时间。 滞后时间量会受到多种因素影响，例如，在源服务器上运行的工作负荷有多大，以及数据中心之间的延迟有多严重。 大多数情况下，副本验证在几秒钟到几分钟之间。 可以使用“副本延迟”指标来跟踪实际的副本延迟，该指标适用于每个副本。 该指标显示的是自上次重播事务以来所经历的时间。 建议观察一段时间的副本延迟，以便确定平均延迟。 可以针对副本延迟设置警报，这样，当它超出预期范围时，你就可以采取行动。

> [!Tip]
> 如果你故障转移到副本，在取消副本与源的链接时的滞后时间将表明会丢失多少数据。

决定要故障转移到某个副本后，

1. 请停止将数据复制到副本。

   此步骤是使副本服务器能够接受写入所必需的。 在此过程中，副本服务器与主服务器之间的链接会取消。 在启动了停止复制操作之后，后端进程通常需要大约 2 分钟才能完成。 请参阅本文的[停止复制](#stop-replication)部分，了解此操作的潜在影响。

2. 将应用程序指向（以前的）副本。

   每个服务器都有唯一的连接字符串。 更新应用程序，使之指向（以前的）副本而不是主服务器。

在应用程序成功处理了读取和写入操作之后，故障转移即已完成。 应用程序经历的停机时间取决于何时检测到问题并完成上面的步骤 1 和 2。

## <a name="considerations-and-limitations"></a>注意事项和限制

### <a name="pricing-tiers"></a>定价层

只读副本当前仅适用于“常规用途”和“内存优化”的定价层。

> [!NOTE]
> 运行副本服务器的成本取决于副本服务器的运行区域。

### <a name="source-server-restart"></a>源服务器重启

在为没有现有副本的源创建副本时，该源服务器会先重启，以便为复制做好自身准备。 请考虑这一点并在非高峰期执行这些操作。

### <a name="new-replicas"></a>新副本

只读副本将创建为新的 Azure Database for MariaDB 服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置

副本是使用与主服务器相同的服务器配置创建的。 创建副本后，可以独立于源服务器更改多项设置：计算代系、vCore 数、存储、备份保留期和 MariaDB 引擎版本。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将源服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本可以跟得上主服务器的变化。

在创建副本时，防火墙规则和参数设置会从源服务器继承到副本。 之后，副本的规则便会独立。

### <a name="stopped-replicas"></a>停止的副本

如果停止源服务器与只读副本之间的复制，已停止的副本会成为接受读取和写入操作的独立服务器。 独立服务器不能再次成为副本。

### <a name="deleted-source-and-standalone-servers"></a>已删除的源服务器和独立服务器

在删除源服务器时，会对所有只读副本都停止复制。 这些副本会自动成为独立服务器，并且可以接受读取和写入。 源服务器本身会被删除。

### <a name="user-accounts"></a>用户帐户

源服务器上的用户会复制到只读副本。 只能使用源服务器上可用的用户帐户来连接到只读副本。

### <a name="server-parameters"></a>服务器参数

为了防止数据不同步并避免潜在的数据丢失或损坏，使用读取副本时，会锁定某些服务器参数以防止其更新。

源服务器和副本服务器上都会锁定以下服务器参数：

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

副本服务器上的 [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) 参数处于锁定状态。

若要在源服务器上更新上述某个参数，请删除副本服务器，更新主服务器上的参数值，然后重新创建副本。

### <a name="other"></a>其他

* 不支持创建副本服务器的副本。
* 内存中的表可能会导致副本服务器变得不同步。这是 MariaDB 复制技术的限制。
* 确保源服务器表具有主键。 缺少主键可能会导致源服务器与副本服务器之间的复制延迟。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 门户创建和管理只读副本](howto-read-replicas-portal.md)
* 了解如何[通过 Azure CLI 和 REST API 创建和管理只读副本](howto-read-replicas-cli.md)
