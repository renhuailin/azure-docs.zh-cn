---
title: 只读副本 - Azure Database for MySQL
description: 了解 Azure Database for MySQL 中的只读副本：选择区域、创建副本、连接副本、监视复制和停止复制。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 06/17/2021
ms.custom: references_regions
ms.openlocfilehash: bb061fb11fbc770d751f60e15c81ce31c6a07440
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666319"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的只读副本

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

使用只读副本功能可将数据从 Azure Database for MySQL 服务器复制到只读服务器。 可将源服务器中的数据复制到最多 5 个副本。 使用 MySQL 引擎的基于本机二进制日志 (binlog) 文件位置的复制技术以异步方式更新副本。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

副本是新的服务器，可以像管理普通的 Azure Database for MySQL 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

如需了解有关 MySQL 复制功能和问题的详细信息，请参阅 [MySQL 复制文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
>

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本

只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 读取工作负载可与副本隔离，而写入工作负载可定向到源。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

副本是只读的，因此不能直接缓解源上的写入容量负担。 此功能并非面向写入密集型工作负荷。

只读副本功能使用 MySQL 本机异步复制。 该功能不适用于同步复制方案。 源与副本之间将会存在明显的延迟。 副本上的数据最终将与源上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

## <a name="cross-region-replication"></a>跨区域复制

可以在与源服务器不同的区域中创建只读副本。 跨区域复制对于灾难恢复规划或使数据更接近用户等方案非常有用。

任何 [Azure Database for MySQL 区域](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)中都可以有源服务器。  源服务器可以在其配对区域或通用副本区域中拥有副本。 下图显示了哪些副本区域可用，具体取决于源区域。

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="只读副本区域":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>通用副本区域

无论源服务器位于何处，你都可以在以下任何区域中创建只读副本。 支持的通用副本区域包括：

| 区域 | 副本可用性 | 
| --- | --- | 
| 澳大利亚东部 | :heavy_check_mark: | 
| 澳大利亚东南部 | :heavy_check_mark: | 
| 巴西南部 | :heavy_check_mark: | 
| 加拿大中部 | :heavy_check_mark: |
| 加拿大东部 | :heavy_check_mark: |
| 美国中部 | :heavy_check_mark: | 
| 美国东部 | :heavy_check_mark: | 
| 美国东部 2 | :heavy_check_mark: |
| 东亚 | :heavy_check_mark: | 
| Japan East | :heavy_check_mark: | 
| 日本西部 | :heavy_check_mark: | 
| 韩国中部 | :heavy_check_mark: |
| 韩国南部 | :heavy_check_mark: |
| 北欧 | :heavy_check_mark: | 
| 美国中北部 | :heavy_check_mark: | 
| 美国中南部 | :heavy_check_mark: | 
| Southeast Asia | :heavy_check_mark: | 
| 英国南部 | :heavy_check_mark: | 
| 英国西部 | :heavy_check_mark: | 
| 美国中西部 | :heavy_check_mark: | 
| 美国西部 | :heavy_check_mark: | 
| 美国西部 2 | :heavy_check_mark: | 
| 西欧 | :heavy_check_mark: | 
| 印度中部* | :heavy_check_mark: | 
| 法国中部* | :heavy_check_mark: | 
| 阿拉伯联合酋长国北部* | :heavy_check_mark: | 
| 南非北部* | :heavy_check_mark: |

> [!Note] 
> *在这些区域中，Azure Database for MySQL 公共预览版中拥有常规用途存储 v2  <br /> *对于这些 Azure 区域，可选择在常规用途存储 v1 和 v2 中创建服务器。 对于使用公共预览版的常规用途存储 v2 创建的服务器，只能在支持常规用途存储 v2 的 Azure 区域中创建副本服务器。

### <a name="paired-regions"></a>配对区域

除通用副本区域外，还可以在源服务器的 Azure 配对区域中创建只读副本。 如果你不知道所在区域的配对，可以从 [Azure 配对区域](../best-practices-availability-paired-regions.md)一文中了解更多信息。

如果你使用跨区域副本进行灾难恢复规划，建议你在配对区域中而不是其他某个区域中创建副本。 配对区域可避免同时更新，并优先考虑物理隔离和数据驻留。  

但是，需要考虑以下限制： 

* 区域可用性：Azure Database for MySQL 可在法国中部、阿拉伯联合酋长国北部和德国中部使用。 但是，它们的配对区域不可用。

* 单向对：某些 Azure 区域仅在一个方向上配对。 这些区域包括印度西部、巴西南部和 US Gov 弗吉尼亚州。
   这意味着印度西部的源服务器可以在印度南部创建副本。 但是，印度南部的源服务器无法在印度西部创建副本。 这是因为印度西部的次要区域是印度南部，但印度南部的次要区域不是印度西部。

## <a name="create-a-replica"></a>创建副本

> [!IMPORTANT]
> * 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保源服务器位于其中一个定价层中。
> * 如果源服务器没有现有的副本服务器，则源服务器可能需要重启才能为复制做好自身准备，具体取决于使用的存储 (v1/v2)。 请考虑服务器重启，并在非高峰时段执行此操作。 有关更多详细信息，请参阅[源服务器重启](./concepts-read-replicas.md#source-server-restart)。 


启动“创建副本”工作流时，将创建空白的 Azure Database for MySQL 服务器。 新服务器中会填充源服务器上的数据。 创建时间取决于源服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。 始终会在与源服务器相同的资源组和订阅中创建副本服务器。 如果要将副本服务器创建到不同的资源组或不同的订阅，可以在创建后[移动副本服务器](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

每个副本都启用了存储[自动增长](concepts-pricing-tiers.md#storage-auto-grow)。 自动增长功能允许副本与复制到它的数据保持同步，并防止由于存储空间不足错误而导致的复制中断。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本

创建时，副本会继承源服务器的防火墙规则。 之后，这些规则将独立于源服务器。

副本会从源服务器继承管理员帐户。 源服务器上的所有用户帐户都会复制到只读副本。 只能通过使用源服务器上可用的用户帐户来连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for MySQL 服务器上连接一样。 对于名称为 **myreplica**、管理员用户名为 **myadmin** 的服务器，可以使用 mysql CLI 连接到副本：

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制

Azure Database for MySQL 在 Azure Monitor 中提供“复制滞后时间(秒)”指标。 此指标仅适用于副本。 此指标是使用 MySQL 的 `SHOW SLAVE STATUS` 命令中提供的 `seconds_behind_master` 指标计算的。 请设置警报，以便在复制滞后时间达到工作负荷不可接受的值时收到通知。

如果发现复制滞后时间增加，请参阅[排查复制延迟问题](howto-troubleshoot-replication-latency.md)，以便排除故障并了解可能的原因。

## <a name="stop-replication"></a>停止复制

可以停止源与副本之间的复制。 在源服务器与只读副本之间停止复制后，该副本会成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本上可用的数据。 独立服务器不会与源服务器保持同步。

选择停止复制到副本时，它会丢失指向其以前的源和其他副本的所有链接。 在源与其副本之间无法自动进行故障转移。

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

了解如何[停止复制到副本](howto-read-replicas-portal.md)。

## <a name="failover"></a>故障转移

在源服务器和副本服务器之间无法自动进行故障转移。

由于复制是异步的，因此在源和副本之间存在滞后时间。 滞后程度会受许多因素影响，例如，在源服务器上运行的工作负载有多大，以及数据中心之间的延迟有多严重。 大多数情况下，副本验证在几秒钟到几分钟之间。 可以使用“副本延迟”指标来跟踪实际的副本延迟，该指标适用于每个副本。 该指标显示的是自上次重播事务以来所经历的时间。 建议观察一段时间的副本延迟，以便确定平均延迟。 可以针对副本延迟设置警报，这样，当它超出预期范围时，你就可以采取行动。

> [!Tip]
> 如果你故障转移到副本，在取消副本与源的链接时的滞后时间将表明会丢失多少数据。

在已决定要故障转移到某个副本之后，请执行以下操作：

1. 请停止将数据复制到副本<br/>
   此步骤是使副本服务器能够接受写入所必需的。 在此过程中，副本服务器会取消与源的链接。 在启动了停止复制操作之后，后端进程通常需要大约 2 分钟才能完成。 请参阅本文的[停止复制](#stop-replication)部分，了解此操作的潜在影响。

2. 将应用程序指向（以前的）副本<br/>
   每个服务器都有唯一的连接字符串。 更新应用程序，使之指向（以前的）副本而不是源。

应用程序成功处理读取和写入操作即表明故障转移已完成。 应用程序经历停机时间的长短将取决于你何时检测到问题并完成前面列出的步骤 1 和 2。

## <a name="global-transaction-identifier-gtid"></a>全局事务标识符 (GTID)

全局事务标识符 (GTID) 是使用源服务器上的每个提交的事务创建的唯一标识符，在 Azure Database for MySQL 中默认处于关闭状态。 GTID 在版本 5.7 和 8.0 中受支持，且仅在所支持的存储不超过 16 TB 的服务器上受支持。 若要详细了解 GTID 及其在复制中的使用方式，请参阅 MySQL 的[使用 GTID 进行复制](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html)文档。

MySQL 支持两种类型的事务：GTID 事务（使用 GTID 标识）和匿名事务（未分配 GTID）

以下服务器参数可用于配置 GTID： 

|**服务器参数**|**说明**|**默认值**|**值**|
|--|--|--|--|
|`gtid_mode`|指示是否使用 GTID 标识事务。 若要在两个模式之间进行更改，只能按升序顺序一次完成一个步骤（例如 `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`：新事务和复制事务都必须是匿名事务 <br> `OFF_PERMISSIVE`：新事务是匿名事务。 复制的事务可以是匿名事务，也可以是 GTID 事务。 <br> `ON_PERMISSIVE`：新事务是 GTID 事务。 复制的事务可以是匿名事务，也可以是 GTID 事务。 <br> `ON`：新事务和复制事务都必须是 GTID 事务。|
|`enforce_gtid_consistency`|通过仅允许执行能够以事务性安全方式记录的语句来强制实施 GTID 一致性。 在启用 GTID 复制之前，必须将此值设置为 `ON`。 |`OFF`|`OFF`：允许所有事务违反 GTID 一致性。  <br> `ON`：不允许任何事务违反 GTID 一致性。 <br> `WARN`：允许所有事务违反 GTID 一致性，但系统会生成警告。 | 

> [!NOTE]
> * 在启用 GTID 后，无法再将其关闭。 如果需要关闭 GTID，请与支持人员联系。 
>
> * 若要更改 GTID 的值，只能按模式的升序一次完成一个步骤。 例如，如果 gtid_mode 设置为 OFF_PERMISSIVE，则可以更改为 ON_PERMISSIVE，但不能更改为 ON。
>
> * 为了保持复制一致性，无法更新主服务器/副本服务器的此设置。
>
> * 建议先将 enforce_gtid_consistency 设置为 ON，然后再设置 gtid_mode=ON


若要启用 GTID 并配置一致性行为，请使用 [Azure 门户](howto-server-parameters.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md) 或 [PowerShell](howto-configure-server-parameters-using-powershell.md)更新 `gtid_mode` 和 `enforce_gtid_consistency` 服务器参数。

如果在源服务器上启用了 GTID（`gtid_mode` = 开），则新创建的副本也将启用 GTID 并使用 GTID 复制。 为了确保复制一致性，在启用 GTID 的情况下创建主服务器或副本服务器后，无法更改 `gtid_mode`。 

## <a name="considerations-and-limitations"></a>注意事项和限制

### <a name="pricing-tiers"></a>定价层

只读副本当前仅适用于“常规用途”和“内存优化”的定价层。

> [!NOTE]
> 运行副本服务器的成本取决于副本服务器的运行区域。

### <a name="source-server-restart"></a>源服务器重启

具有常规用途存储 v1 的服务器，`log_bin` 参数在默认情况下将处于关闭状态。 该值将会在你创建第一个只读副本时打开。 如果源服务器没有现有的只读副本，该源服务器会先重启，以便为复制做好自身准备。 请考虑服务器重启，并在非高峰时段执行此操作。

具有常规用途存储 v2 的源服务器，`log_bin` 参数在默认情况下将处于打开状态，并且在你添加只读副本时不需要重启。 

### <a name="new-replicas"></a>新副本

只读副本创建为新的 Azure Database for MySQL 服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置

副本是使用与源相同的服务器配置创建的。 在创建副本后，可以独立于源服务器更改多项设置：计算代系、vCore 数、存储，以及备份保持期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将源服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本与源服务器发生的任何更改保持同步。

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

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

副本服务器上的 [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 参数处于锁定状态。

若要在源服务器上更新上述参数之一，请删除副本服务器，更新源上的参数值，然后重新创建副本。

### <a name="gtid"></a>GTID

GTID 在以下版本上受支持：

* MySQL 版本 5.7 和 8.0。
* 支持不超过 16 TB 的存储的服务器。 若要获取支持 16 TB 存储的区域的完整列表，请参阅[定价层](concepts-pricing-tiers.md#storage)一文。

GTID 默认处于关闭状态。 在启用 GTID 后，无法再将其关闭。 如果需要关闭 GTID，请与支持人员联系。

如果在源服务器上启用了 GTID，则新创建的副本也将启用 GTID 并使用 GTID 复制。 无法在源或副本服务器上更新 `gtid_mode`，这是为了保持复制一致性。

### <a name="other"></a>其他

* 不支持创建副本服务器的副本。
* 内存中的表可能会导致副本服务器变得不同步。这是 MySQL 复制技术的限制。 有关详细信息，请阅读 [MySQL 参考文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)中的更多信息。
* 请确保源服务器表具有主键。 缺少主键可能会导致源和副本之间出现复制延迟。
* 查看 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)中 MySQL 复制限制的完整列表

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 门户创建和管理只读副本](howto-read-replicas-portal.md)
* 了解如何[通过 Azure CLI 和 REST API 创建和管理只读副本](howto-read-replicas-cli.md)