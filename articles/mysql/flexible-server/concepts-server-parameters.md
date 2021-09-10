---
title: 服务器参数 - Azure Database for MySQL 灵活服务器
description: 本主题提供了在 Azure Database for MySQL 灵活服务器中配置服务器参数的准则。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 43f544cb2782fc80dd574a1d8c425283c51a0ed3
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256470"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 灵活服务器中的服务器参数

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文提供了在 Azure Database for MySQL 灵活服务器中配置服务器参数的注意事项和准则。

## <a name="what-are-server-variables"></a>什么是服务器变量？ 

MySQL 引擎提供了可以用来配置和优化引擎行为的许多不同的[服务器变量/参数](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html)。 某些参数可在运行时动态设置，另外一些参数则为“静态”参数，需要重启服务器才能应用。

Azure Database for MySQL 灵活服务器公开了通过 [Azure 门户](./how-to-configure-server-parameters-portal.md) 和 [Azure CLI](./how-to-configure-server-parameters-cli.md) 根据工作负载的需求更改各种 MySQL 服务器参数值的功能。

## <a name="configurable-server-parameters"></a>可配置的服务器参数

你可以使用服务器参数管理 Azure Database for MySQL 灵活服务器配置。 创建服务器时，将使用默认值和推荐值配置服务器参数。 Azure 门户上的“服务器参数”边栏选项卡显示可修改和不可修改的服务器参数。 不可修改的服务器参数为灰显。

受支持服务器参数的列表还在不断增加。 在 Azure 门户中使用服务器参数选项卡可查看完整列表并配置服务器参数值。

请参阅以下各部分，详细了解多个经常更新的服务器参数的限制。 这些限制取决于服务器的计算层和大小 (vCore)。

> [!NOTE]
> 如果想要修改无法修改的服务器参数，但想要将其视为适用于你的环境的可修改项，请打开 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 项或投票是否反馈已存在，这有助于我们设置优先级。

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

在 Azure Database for MySQL 灵活服务器中，始终启用二进制日志（即 `log_bin` 设置为“开启”）。 在灵活服务器中，log_bin_trust_function_creators 默认设置为“开启”。 

二进制日志记录格式始终是“行”，所有与服务器的连接始终使用基于行的二进制日志记录。 使用基于行的二进制日志记录时，不存在安全问题并且二进制日志记录无法中断，因此可以安全地使 [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) 保留为“开启”。

如果 [`log_bin_trust_function_creators`] 设置为“关闭”，如果你尝试创建触发器，可能会收到如下错误消息：你没有“超级”权限且二进制日志记录已启用(你可能需要使用安全性更低的 `log_bin_trust_function_creators` 变量)”。 

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

查看 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)详细了解此参数。

|**定价层**|**vCore(s)**|**内存大小 (GiB)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|---|
|可突发 (B1s)|1|1|134217728|33554432|134217728|
|可突发 (B1ms)|1|2|536870912|134217728|536870912|
|可突发|2|4|2147483648|134217728|2147483648|
|常规用途|2|8|6442450944|134217728|6442450944|
|常规用途|4|16|12884901888|134217728|12884901888|
|常规用途|8|32|25769803776|134217728|25769803776|
|常规用途|16|64|51539607552|134217728|51539607552|
|常规用途|32|128|103079215104|134217728|103079215104|
|常规用途|48|192|154618822656|134217728|154618822656|
|常规用途|64|256|206158430208|134217728|206158430208|
|内存优化|2|16|12884901888|134217728|12884901888|
|内存优化|4|32|25769803776|134217728|25769803776|
|内存优化|8|64|51539607552|134217728|51539607552|
|内存优化|16|128|103079215104|134217728|103079215104|
|内存优化|32|256|206158430208|134217728|206158430208|
|内存优化|48|384|309237645312|134217728|309237645312|
|内存优化|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL 根据你在表创建期间提供的配置，将 InnoDB 表存储在不同的表空间中。 [系统表空间](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)是 InnoDB 数据字典的存储区域。 [file-per-table 表空间](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)包含单个 InnoDB 表的数据和索引，并存储在文件系统内它自己的数据文件中。 此行为由 `innodb_file_per_table` 服务器参数控制。 将 `innodb_file_per_table` 设置为 `OFF` 会导致 InnoDB 在系统表空间中创建表。 否则，InnoDB 在 file-per-table 表空间中创建表。

在单个数据文件中，Azure Database for MySQL 灵活服务器支持的最大大小为 4 TB。 如果数据库大小超过 4 TB，应在 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 表空间中创建表。 如果单个表的大小超过 4 TB，应使用分区表。

### <a name="max_connections"></a>max_connections

max_connection 的值取决于服务器的内存大小。 

|**定价层**|**vCore(s)**|**内存大小 (GiB)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|---|
|可突发 (B1s)|1|1|85|10|171|
|可突发 (B1ms)|1|2|171|10|341|
|可突发|2|4|341|10|683|
|常规用途|2|8|683|10|1365|
|常规用途|4|16|1365|10|2731|
|常规用途|8|32|2731|10|5461|
|常规用途|16|64|5461|10|10923|
|常规用途|32|128|10923|10|21845|
|常规用途|48|192|16384|10|32768|
|常规用途|64|256|21845|10|43691|
|内存优化|2|16|1365|10|2731|
|内存优化|4|32|2731|10|5461|
|内存优化|8|64|5461|10|10923|
|内存优化|16|128|10923|10|21845|
|内存优化|32|256|21845|10|43691|
|内存优化|48|384|32768|10|65536|
|内存优化|64|504|43008|10|86016|

当连接数超出限制时，可能会收到以下错误：
> 错误 1040 (08004)：连接过多

> [!IMPORTANT]
>为了获得最佳体验，建议使用 ProxySQL 等连接池程序来高效地管理连接。

创建与 MySQL 的新客户端连接需要时间，一旦建立，这些连接就会占用数据库资源，即使在空闲时也是如此。 大多数应用程序请求许多生存期短的连接，这加剧了这种情况。 其结果是可用于实际工作负荷的资源减少，从而导致性能下降。 连接池程序不仅会减少空闲连接，还会重用现有连接，因而有助于避免这种情况。 若要了解如何设置 ProxySQL，请访问我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

>[!Note]
>ProxySQL 是一个开源社区工具。 Microsoft 尽最大努力为它提供支持。 若要获得包含权威指导的生产支持，可以评估并联系 [ProxySQL 产品支持](https://proxysql.com/services/support/)。

### <a name="innodb_strict_mode"></a>innodb_strict_mode

如果收到类似于“行大小太大(> 8126)”的错误，则可能需要禁用 innodb_strict_mode 参数。 不允许在服务器级别全局修改服务器参数 innodb_strict_mode，因为如果行数据大小大于 8k，该数据将会被截断，且不显示错误，这可能会导致数据丢失。 建议修改架构以适应页面大小限制。 

可以使用 `init_connect` 在会话级别设置此参数。 若要在会话级别设置 innodb_strict_mode，请参阅[设置未列出的参数](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)。

> [!NOTE]
> 如果有只读副本服务器，在源服务器上的会话级别将 innodb_strict_mode 设置为 OFF 会中断复制。 如果有只读副本，建议将该参数始终设置为 OFF。

### <a name="time_zone"></a>time_zone

初始部署后，Azure for MySQL 灵活服务器包含用于时区信息的系统表，但这些表没有填充数据。 可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程来填充时区表。 若要了解如何调用存储过程并设置全局时区或会话级时区，请参阅 [Azure 门户](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter)或 [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 一文。

### <a name="binlog_expire_logs_seconds"></a>binlog_expire_logs_seconds 

在 Azure Database for MySQL 中，此参数指定在清除二进制日志文件之前，服务等待的秒数。

二进制日志包含描述数据库更改的“事件”，例如表创建操作或对表数据所做的更改。 它还包含可能已进行了更改的语句的事件。 二进制日志主要用于两个目的：复制和数据恢复操作。  通常，当句柄从服务、备份或副本集中释放后，二进制日志就会立即被清除。 如果有多个副本，则会等待最慢的副本读取更改，然后再被清除。 若要将二进制日志保留更长的时间，可以配置参数 binlog_expire_logs_seconds。 如果将 binlog_expire_logs_seconds 设置为 0（默认值），则在释放二进制日志的句柄后，会立即将其清除。 如果 binlog_expire_logs_seconds 大于 0，则会等待已配置的秒数，然后再将其清除。 对于 Azure database for MySQL，二进制文件的备份和只读副本清除等托管功能都是在内部处理的。 复制从 Azure Database for MySQL 服务中输出的数据时，需要在主副本中设置此参数，以避免在副本从主副本中读取更改之前清除二进制日志。 如果将 binlog_expire_logs_seconds 设置为一个较大的值，则不会立即清除二进制日志，这可能会导致存储计费的增加。 

## <a name="non-modifiable-server-parameters"></a>不可修改的服务器参数

Azure 门户上的“服务器参数”边栏选项卡显示可修改和不可修改的服务器参数。 不可修改的服务器参数为灰显。如果要在会话级别配置不可修改的服务器参数，请参阅 [Azure 门户](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) 或 [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) 文章，了解如何使用 `init_connect` 在连接级别设置参数。

## <a name="next-steps"></a>后续步骤

- 如何配置 [Azure 门户中的服务器参数](./how-to-configure-server-parameters-portal.md)
- 如何配置 [Azure CLI 中的服务器参数](./how-to-configure-server-parameters-cli.md)
