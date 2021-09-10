---
title: 比较 SQL 数据库和 SQL 托管实例的数据库引擎功能
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 本文比较了 Azure SQL 数据库和 Azure SQL 托管实例的数据库引擎功能
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: bonova, mathoma, danil
ms.date: 08/26/2021
ms.openlocfilehash: 451dd198b5507b99400d4e1c8e1670e596016af1
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123308286"
---
# <a name="features-comparison-azure-sql-database-and-azure-sql-managed-instance"></a>功能比较：Azure SQL 数据库和 Azure SQL 托管实例

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL 数据库服务和 SQL 托管实例均与最新稳定版本的 SQL Server 共用一个通用代码库。 大多数标准 SQL 语言、查询处理和数据库管理功能都是相同的。 SQL Server 与 SQL 数据库或 SQL 托管实例都有以下功能：

- 语言功能 - [流语言和关键字](/sql/t-sql/language-elements/control-of-flow)、[光标](/sql/t-sql/language-elements/cursors-transact-sql)、[数据类型](/sql/t-sql/data-types/data-types-transact-sql)、[DML 语句](/sql/t-sql/queries/queries)、[谓词](/sql/t-sql/queries/predicates)、[序号](/sql/relational-databases/sequence-numbers/sequence-numbers)、[存储过程](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)和[变量](/sql/t-sql/language-elements/variables-transact-sql)的控制。
- 数据库功能 - [自动优化（计划强制）](/sql/relational-databases/automatic-tuning/automatic-tuning)、[更改跟踪](/sql/relational-databases/track-changes/about-change-tracking-sql-server)、[数据库排序规则](/sql/relational-databases/collations/set-or-change-the-database-collation)、[包含的数据库](/sql/relational-databases/databases/contained-databases)、[包含的用户](/sql/relational-databases/security/contained-database-users-making-your-database-portable)、[数据压缩](/sql/relational-databases/data-compression/data-compression)、[数据库配置设置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)、[联机索引操作](/sql/relational-databases/indexes/perform-index-operations-online)、[分区](/sql/relational-databases/partitions/partitioned-tables-and-indexes)和[时态表](/sql/relational-databases/tables/temporal-tables)（[参阅入门指南](../temporal-tables.md)）。
- 安全功能 - [应用程序角色](/sql/relational-databases/security/authentication-access/application-roles)、[动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)（[参阅入门指南](dynamic-data-masking-overview.md)）、[行级别安全性](/sql/relational-databases/security/row-level-security)和威胁检测 - 请参阅适用于 [SQL 数据库](threat-detection-configure.md)和 [SQL 托管实例](../managed-instance/threat-detection-configure.md)的入门指南。
- 多模型功能 - [图形处理](/sql/relational-databases/graphs/sql-graph-overview)、[JSON 数据](/sql/relational-databases/json/json-data-sql-server)（[参阅入门指南](json-features.md)）、[OPENXML](/sql/t-sql/functions/openxml-transact-sql)、[空间](/sql/relational-databases/spatial/spatial-data-sql-server)、[OPENJSON](/sql/t-sql/functions/openjson-transact-sql) 和 [XML 索引](/sql/t-sql/statements/create-xml-index-transact-sql)。

Azure 管理数据库并保证其高可用性。 可能影响高可用性或无法在 PaaS 领域中使用的某些功能在 SQL 数据库和 SQL托管实例中会受到限制。 下表描述了这些功能。

如需有关差异的更多详细信息，请查看相关页面：
- [Azure SQL 数据库与SQL Server 之间的差异](transact-sql-tsql-differences-sql-server.md)
- [Azure SQL 托管实例与SQL Server 之间的差异](../managed-instance/transact-sql-tsql-differences-sql-server.md)


## <a name="features-of-sql-database-and-sql-managed-instance"></a>SQL 数据库和 SQL 托管实例的功能

下表列出了 SQL Server 的主要功能，并说明该功能在 Azure SQL 数据库或 Azure SQL 托管实例中是部分受支持还是完全受支持，同时提供了指向该功能的详细信息的链接。

| **功能** | **Azure SQL 数据库** | **Azure SQL 托管实例** |
| --- | --- | --- |
| [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 请参阅[证书存储](always-encrypted-certificate-store-configure.md)和[密钥保管库](always-encrypted-azure-key-vault-configure.md) | 是 - 请参阅[证书存储](always-encrypted-certificate-store-configure.md)和[密钥保管库](always-encrypted-azure-key-vault-configure.md) |
| [Always On 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每个数据库都保证 [99.99-99.995% 可用性](high-availability-sla.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)中对灾难恢复进行了讨论 | 每个数据库都保证 [99.99.% 可用性](high-availability-sla.md)，并且[不能由用户管理](../managed-instance/transact-sql-tsql-differences-sql-server.md#availability)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)中对灾难恢复进行了讨论。 使用[自动故障转移组](auto-failover-group-overview.md)在另一个区域中配置辅助 SQL 托管实例。 不能将 SQL Server 实例和 SQL 数据库用作 SQL 托管实例的辅助数据库。 |
| [附加数据库](/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [审核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](auditing-overview.md)| [是](../managed-instance/auditing-configure.md)，但有一些[差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) |
| [Azure Active Directory (Azure AD) 身份验证](authentication-aad-overview.md) | 是的。 仅限 Azure AD 用户。 | 是的。 包括服务器级 Azure AD 登录名。 |
| [备份命令](/sql/t-sql/statements/backup-transact-sql) | 否，仅系统启动的自动备份 - 请参阅[自动备份](automated-backups-overview.md) | 是，用户发起的仅将备份复制到 Azure Blob 存储的操作（用户无法发起自动系统备份）- 请参阅[备份差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) |
| [内置函数](/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [BULK INSERT 语句](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 是，但是只能从充当源的 Azure Blob 存储进行。 | 是，但是只能从充当源的 Azure Blob 存储进行 - 请参阅[差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)。 |
| [证书和非对称密钥](/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 是，无法访问文件系统完成 `BACKUP` 和 `CREATE` 操作。 | 是，无法访问文件系统完成 `BACKUP` 和 `CREATE` 操作 - 请参阅[证书差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#certificates)。 |
| [更改数据捕获 - CDC](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | 是 |
| [排序规则 - 服务器/实例](/sql/relational-databases/collations/set-or-change-the-server-collation) | 否，始终使用默认的服务器排序规则 `SQL_Latin1_General_CP1_CI_AS`。 | 是，可以在[创建实例](../managed-instance/create-template-quickstart.md)时设置，以后不可更新。 |
| [列存储索引](/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [高级层、标准层 - S3 及以上层、常规用途层、业务关键层和超大规模层](/sql/relational-databases/indexes/columnstore-indexes-overview) |是 |
| [公共语言运行时 - CLR](/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是，但无权在 `CREATE ASSEMBLY` 语句中访问文件系统 - 请参阅 [CLR 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#clr) |
| [凭据](/sql/relational-databases/security/authentication-access/credentials-database-engine) | 是，但是仅限[数据库范围的凭据](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 | 是，但仅支持 **Azure Key Vault** 和 `SHARED ACCESS SIGNATURE` - 请参阅 [详细信息](../managed-instance/transact-sql-tsql-differences-sql-server.md#credential) |
| [跨数据库/三部分名称查询](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](elastic-query-overview.md) | 是，外加[弹性查询](elastic-query-overview.md) |
| [跨数据库事务](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | 是，在实例中。 对于跨实例查询，请参阅[链接服务器的差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)。 |
| [数据库邮件 - DbMail](/sql/relational-databases/database-mail/database-mail) | 否 | 是 |
| [数据库镜像](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | [否](../managed-instance/transact-sql-tsql-differences-sql-server.md#database-mirroring) |
| [数据库快照](/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [DBCC 语句](/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [DBCC 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) |
| [DDL 语句](/sql/t-sql/statements/statements) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [DDL 触发器](/sql/relational-databases/triggers/ddl-triggers) | 仅数据库 |  是 |
| [分布式分区视图](/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | 是 |
| [分布式事务 - MS DTC](/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 请参阅[弹性事务](elastic-transactions-overview.md) | 否 - 请参阅[弹性事务](elastic-transactions-overview.md) |
| [DML 触发器](/sql/relational-databases/triggers/create-dml-triggers) | 大多数 - 请参阅单个语句 |  是 |
| [DMV](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大多数 - 请参阅单个 DMV |  是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [弹性查询](elastic-query-overview.md)（公共预览版） | 是，使用所需的 RDBMS 类型。 | 否 |
| [事件通知](/sql/relational-databases/service-broker/event-notifications) | 否 - 请参阅[警报](alerts-insights-configure-portal.md) | 否 |
| [表达式](/sql/t-sql/language-elements/expressions-transact-sql) |是 | 是 |
| [扩展事件 (XEvent)](/sql/relational-databases/extended-events/extended-events) | 部分 - 请参阅 [SQL 数据库中的扩展事件](xevent-db-diff-from-svr.md) | 是 - 请参阅[扩展事件的差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#extended-events) |
| [扩展的存储过程](/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
| [文件和文件组](/sql/relational-databases/databases/database-files-and-filegroups) | 仅限主文件组 | 是的。 文件路径是自动分配的，不能在 `ALTER DATABASE ADD FILE` [语句](../managed-instance/transact-sql-tsql-differences-sql-server.md#alter-database-statement)中指定文件位置。  |
| [文件流](/sql/relational-databases/blob/filestream-sql-server) | 否 | [否](../managed-instance/transact-sql-tsql-differences-sql-server.md#filestream-and-filetable) |
| [全文搜索 (FTS)](/sql/relational-databases/search/full-text-search) |  是，但不支持第三方断字符 | 是，但[不支持第三方断字符](../managed-instance/transact-sql-tsql-differences-sql-server.md#full-text-semantic-search) |
| [函数](/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [内存中优化](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 在[“高级”和“业务关键”服务层级](../in-memory-oltp-overview.md)中为是。</br> 在[超大规模服务层级](service-tier-hyperscale.md)中为非持久性内存中 OLTP 对象（如内存优化表变量）提供有限支持。| 在[“业务关键”服务层级](../managed-instance/sql-managed-instance-paas-overview.md)中为是 |
| [语言元素](/sql/t-sql/language-elements/language-elements-transact-sql) | 大多数 - 请参阅单个元素 |  是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [账本](ledger-overview.md) | 是 | 否 |
| [链接服务器](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](elastic-query-horizontal-partitioning.md) | 是的。 仅适用于没有分布式事务的 [SQL Server 和 SQL 数据库](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)。 |
| 从文件（CSV、Excel）中读取数据的[链接服务器](/sql/relational-databases/linked-servers/linked-servers-database-engine)| 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 来替代 CSV 格式。 | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 来替代 CSV 格式。 在 [SQL 托管实例反馈项](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)中跟踪这些请求|
| [日志传送](/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每个数据库均包含[高可用性](high-availability-sla.md)。 [业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)中对灾难恢复进行了探讨。 | 以本机内置方式成为 [Azure 数据迁移服务 (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md) 迁移过程的一部分。 以本机方式专为自定义数据迁移项目而构建成外部[日志重播服务 (LRS)](../managed-instance/log-replay-service-migrate.md)。<br /> 不可用作高可用性解决方案，因为每个数据库都附带其他[高可用性](high-availability-sla.md)方法，并且我们不建议使用日志传送作为高可用性替代方案。 [业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)中对灾难恢复进行了探讨。 不可用作数据库之间的复制机制 - 请使用[业务关键层](service-tier-business-critical.md)、[自动故障转移组](auto-failover-group-overview.md)或[事务复制](../managed-instance/replication-transactional-overview.md)中的辅助副本作为替代方案。 |
| [登录名和用户](/sql/relational-databases/security/authentication-access/principals-database-engine) | 是，但是 `CREATE` 和 `ALTER` 登录语句不提供所有选项（没有 Windows 和服务器级别 Azure Active Directory 登录名）。 不支持 `EXECUTE AS LOGIN` - 请改用 `EXECUTE AS USER`。  | 是，但有一些[差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)。 不支持 Windows 登录名，应将其替换为 Azure Active Directory 登录名。 |
| [批量导入中的最小日志记录](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否，仅支持完整恢复模式。 | 否，仅支持完整恢复模式。 |
| [修改系统数据](/sql/relational-databases/databases/system-databases) | 否 | 是 |
| [OLE 自动化](/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 否 | 否 |
| [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)|否|是，仅适用于 SQL 数据库、SQL 托管实例和 SQL Server。 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql)|否|是，仅适用于 SQL 数据库、SQL 托管实例和 SQL Server。 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)|是，只是为了从 Azure Blob 存储导入。 |是，仅适用于 SQL 数据库、SQL 托管实例和 SQL Server，以及从 Azure Blob 存储进行导入的操作。 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [运算符](/sql/t-sql/language-elements/operators-transact-sql) | 大多数 - 请参阅单个运算符 |是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [Polybase](/sql/relational-databases/polybase/polybase-guide) | 否。 可以使用 `OPENROWSET` 函数或使用[引用 Synapse Analytics 中的无服务器 SQL 池的外部表](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)查询放置在 Azure Blob 存储上的文件中的数据。 | 不是。 可以使用 `OPENROWSET` 函数、[引用 Synapse Analytics 中的无服务器 SQL 池的链接服务器](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)或引用 [Synapse Analytics 中的无服务器 SQL 池](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)或 SQL Server 的外部表查询放置在 Azure Blob 存储上的文件中的数据。 |
| [查询通知](/sql/relational-databases/native-client/features/working-with-query-notifications) | 否 | 是 |
| [机器学习服务](/sql/advanced-analytics/what-is-sql-server-machine-learning)（_以前称为 R Services_）| 否 | 是，请参阅 [Azure SQL 托管实例中的机器学习服务](../managed-instance/machine-learning-services-overview.md) |
| [恢复模型](/sql/relational-databases/backup-restore/recovery-models-sql-server) | 仅支持保证高可用性的完整恢复。 “简单”和“批量日志记录”恢复模式不可用。 | 仅支持保证高可用性的完整恢复。 “简单”和“批量日志记录”恢复模式不可用。 |
| [资源调控器](/sql/relational-databases/resource-governor/resource-governor) | 否 | 是 |
| [RESTORE 语句](/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是，对 Azure Blob 存储上的备份文件使用必需的 `FROM URL` 选项。 请参阅[还原差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#restore-statement) |
| [从备份还原数据库](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 仅从自动备份 - 请参阅 [SQL 数据库恢复](recovery-using-backups.md) | 从自动备份 - 请参阅 [SQL 数据库恢复](recovery-using-backups.md)；从 Azure Blob 存储中的完整备份 - 请参阅[备份差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) |
| [将数据库还原到 SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 否。 使用 BACPAC 或 BCP 而不是本机还原。 | 否，因为 SQL 托管实例中使用的 SQL Server 数据库引擎的版本高于本地使用的 SQL Server 的任何 RTM 版本。 改用 BACPAC、BCP 或事务复制。 |
| [语义搜索](/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是，但仅限在实例中。 如果使用远程 Service Broker 路由，请在迁移期间尝试将多个分布式 SQL Server 实例中的数据库整合到一个 SQL 托管实例中，并只使用本地路由。 请参阅 [Service Broker 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker) |
| [服务器配置设置](/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [SET 语句](/sql/t-sql/statements/set-statements-transact-sql) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [SQL Server 代理](/sql/ssms/agent/sql-server-agent) | 否 - 请参阅[弹性作业（预览版）](elastic-jobs-overview.md) | 是 - 请参阅 [SQL Server 代理差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent) |
| [SQL Server 审核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 请参阅 [SQL 数据库审核](auditing-overview.md) | 是 - 请参阅[审核差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) |
| [系统存储函数](/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [系统存储过程](/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 请参阅单个存储过程 | 是 - 请参阅[存储过程、函数和触发器差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [系统表](/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 请参阅单个表 | 是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [系统目录视图](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 请参阅单个视图 | 是 - 请参阅 [T-SQL 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [TempDB](/sql/relational-databases/databases/tempdb-database) | 是的。 [每个数据库的每个核心为 32GB 大小](resource-limits-vcore-single-databases.md)。 | 是的。 [整个 GP 层的每个 vCore 为 24GB 大小，受 BC 层上的实例大小限制](../managed-instance/resource-limits.md#service-tier-characteristics)  |
| [临时表](/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本地和数据库范围的全局临时表 | 本地和实例范围的全局临时表 |
| 时区选择 | 否 | [是](../managed-instance/timezones-overview.md)，必须在创建 SQL 托管实例时配置。 |
| [跟踪标志](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 否 | 是，但仅支持有限的全局跟踪标志集。 请参阅 [DBCC 差异](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) |
| [事务复制](../managed-instance/replication-transactional-overview.md) | 是，[仅限事务性和快照复制订户](migrate-to-database-from-sql-server.md) | 是，目前为[公共预览版](/sql/relational-databases/replication/replication-with-sql-database-managed-instance)。 请参阅[此处](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)的约束。 |
| [透明数据加密 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 - 仅限“常规用途”、“业务关键”和“超大规模”（预览版）服务层级| [是](transparent-data-encryption-tde-overview.md) |
| Windows 身份验证 | 否 | 否 |
| [Windows Server 故障转移群集](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 否。 每个数据库附带提供[高可用性](high-availability-sla.md)的其他技术。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)中对灾难恢复进行了讨论。 | 否。 每个数据库附带提供[高可用性](high-availability-sla.md)的其他技术。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)中对灾难恢复进行了讨论。 |

## <a name="platform-capabilities"></a>平台功能

Azure 平台提供许多 PaaS 功能，可以增大标准数据库功能的价值。 可将许多外部服务与 Azure SQL 数据库配合使用。

| **平台功能** | **Azure SQL 数据库** | **Azure SQL 托管实例** |
| --- | --- | --- |
| [活动异地复制](active-geo-replication-overview.md) | 是 - 除超大规模之外的所有服务层级 | 否，请参阅用作替代方案的[自动故障转移组](auto-failover-group-overview.md) |
| [自动故障转移组](auto-failover-group-overview.md) | 是 - 除超大规模之外的所有服务层级 | 是，请参阅[自动故障转移组](auto-failover-group-overview.md)|
| 自动缩放 | 是，但仅限[无服务器模型](serverless-tier-overview.md)。 在非服务器模式下，可以快速联机更改服务层级（更改 vCore、存储或 DTU）。 更改服务层级只会造成极短时间的停机，甚至不会导致任何停机。 | 否，需要选择预留的计算和存储。 可联机更改服务层级（vCore 或最大存储），只会造成极短时间的停机，甚至不会导致任何停机。 |
| [自动备份](automated-backups-overview.md) | 是的。 完整备份每 7 天一次，差异备份每 12 小时一次，日志备份每 5-10 分钟一次。 | 是的。 完整备份每 7 天一次，差异备份每 12 小时一次，日志备份每 5-10 分钟一次。 |
| [自动优化（索引）](/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](automatic-tuning-overview.md)| 否 |
| [可用性区域](../../availability-zones/az-overview.md) | 是 | 否 |
| [Azure 资源运行状况](../../service-health/resource-health-overview.md) | 是 | 否 |
| 备份保留 | 是的。 默认为 7 天，最长 35 天。 | 是的。 默认为 7 天，最长 35 天。 |
| [数据迁移服务 (DMS)](/sql/dma/dma-overview) | 是 | 是 |
| [弹性作业](elastic-jobs-overview.md) | 是 - 请参阅[弹性作业（预览版）](elastic-jobs-overview.md) | 否（可以改用 [SQL 代理](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)）。 |
| 文件系统访问 | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) 作为替代方法来访问和加载 Azure Blob 存储中的数据。 | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) 作为替代方法来访问和加载 Azure Blob 存储中的数据。 |
| [异地还原](recovery-using-backups.md#geo-restore) | 是 | 是 |
| [超大规模体系结构](service-tier-hyperscale.md) | 是 | 否 |
| [长期备份保留 - LTR](long-term-retention-overview.md) | 是，将自动创建的备份最长保留 10 年。 | 是，将自动创建的备份最长保留 10 年。 |
| 暂停/恢复 | 是，在[无服务器模型](serverless-tier-overview.md)中 | 否 |
| [基于策略的管理](/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| 公共 IP 地址 | 是的。 访问权限可以使用防火墙或服务终结点来限制。  | 是的。 需要显式启用，且必须在 NSG 规则中启用端口 3342。 可根据需要禁用公共 IP。 有关更多详细信息，请参阅[公共终结点](../managed-instance/public-endpoint-overview.md)。 |
| [数据库时间点还原](/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是 - 除超大规模之外的所有服务层级 - 请参阅 [SQL 数据库恢复](recovery-using-backups.md#point-in-time-restore) | 是 - 请参阅 [SQL 数据库恢复](recovery-using-backups.md#point-in-time-restore) |
| 资源池 | 是，用作[弹性池](elastic-pool-overview.md) | 是的。 SQL 托管实例的单个实例可以包含多个共享同一资源池的数据库。 此外，还可以在可共享资源的[实例池（预览版）](../managed-instance/instance-pools-overview.md)中部署 SQL 托管实例的多个实例。 |
| 纵向扩展或缩减（联机） | 是，可以更改 DTU、预留的 vCore 数或最大存储，这只会造成极短时间的停机。 | 是，可以更改预留的 vCore 数或最大存储，这只会造成极短时间的停机。 |
| [SQL 别名](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | 否，使用 [DNS 别名](dns-alias-overview.md) | 否，使用 [Cliconfg](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022) 在客户端计算机上设置别名。 |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md) | 是 | 是 |
| [SQL 数据同步](sql-data-sync-sql-server-configure.md) | 是 | 否 |
| [SQL Server Analysis Services (SSAS)](/sql/analysis-services/analysis-services) | 否，[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 是一项单独的 Azure 云服务。 | 否，[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 是一项单独的 Azure 云服务。 |
| [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services) | 是，使用 Azure 数据工厂 (ADF) 环境中的托管 SSIS ，其中程序包存储在由 Azure SQL 数据库承载的 SSISDB 中并在 Azure SSIS 集成运行时 (IR) 上执行，请参阅[在 ADF 中创建 Azure-SSIS IR](../../data-factory/create-azure-ssis-integration-runtime.md)。 <br/><br/>若要比较 SQL 数据库和 SQL 托管实例中的 SSIS 功能，请参阅[比较 SQL 数据库和 SQL 托管实例](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。 | 是，使用 Azure 数据工厂 (ADF) 环境中的托管 SSIS，其中包存储在由 SQL 托管实例托管的 SSISDB 中并在 Azure SSIS Integration Runtime (IR) 上执行，请参阅[在 ADF 中创建 Azure-SSIS IR](../../data-factory/create-azure-ssis-integration-runtime.md)。 <br/><br/>若要比较 SQL 数据库和 SQL 托管实例中的 SSIS 功能，请参阅[比较 SQL 数据库和 SQL 托管实例](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。 |
| [SQL Server Reporting Services (SSRS)](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 请参阅 [Power BI](/power-bi/) | 否 - 请改用 [Power BI 分页报表](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)或在 Azure VM 上托管 SSRS。 虽然 SQL 托管实例不能将 SSRS 作为服务运行，但它可以使用 SQL Server 身份验证为安装在 Azure 虚拟机上的报表服务器托管 [SSRS 目录数据库](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)。 |
| [Query Performance Insights (QPI)](query-performance-insight-use.md) | 是 | 否。 使用 SQL Server Management Studio 和 Azure Data Studio 中的内置报告。 |
| [VNet](../../virtual-network/virtual-networks-overview.md) | 部分支持，可以使用 [VNet 终结点](vnet-service-endpoint-rule-overview.md)进行受限访问 | 是，SQL 托管实例注入到客户的 VNet 中。 请参阅[子网](../managed-instance/transact-sql-tsql-differences-sql-server.md#subnet)和 [VNet](../managed-instance/transact-sql-tsql-differences-sql-server.md#vnet) |
| VNet 服务终结点 | [是](vnet-service-endpoint-rule-overview.md) | 否 |
| VNet 全球对等互连 | 是，使用[专用 IP 和服务终结点](vnet-service-endpoint-rule-overview.md) | 是，使用[虚拟网络对等互连](https://techcommunity.microsoft.com/t5/azure-sql/new-feature-global-vnet-peering-support-for-azure-sql-managed/ba-p/1746913)。 |
| [专用连接](../../private-link/private-link-overview.md) | 是，使用[专用链接](../../private-link/private-endpoint-overview.md) | 是，使用 VNet。 | 

## <a name="tools"></a>工具

Azure SQL 数据库和 Azure SQL 托管实例支持各种可帮助管理数据的数据工具。

| **工具** | **Azure SQL 数据库** | **Azure SQL 托管实例** |
| --- | --- | --- |
| Azure 门户 | 是 | 是 |
| Azure CLI | 是 | 是|
| [Azure Data Studio](/sql/azure-data-studio/what-is) | 是 | 是 |
| Azure PowerShell | 是 | 是 |
| [BACPAC 文件（导出）](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 请参阅 [SQL 数据库导出](database-export.md) | 是 - 请参阅 [SQL 托管实例导出](database-export.md) |
| [BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 请参阅 [SQL 数据库导入](database-import.md) | 是 - 请参阅 [SQL 托管实例导入](database-import.md) |
| [Data Quality Services (DQS)](/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [Master Data Services (MDS)](/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [是](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 是，[版本 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 | 是 |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | 是 | 是，[18.0 和更高版本](/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | 是 | 是 |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 请参阅[扩展事件](xevent-db-diff-from-svr.md) | 是 |
| [System Center Operations Manager](/system-center/scom/welcome) | [是](https://www.microsoft.com/download/details.aspx?id=38829) | [是](https://www.microsoft.com/en-us/download/details.aspx?id=101203) |

## <a name="migration-methods"></a>迁移方法

可以使用不同的迁移方法在 SQL Server、Azure SQL 数据库和 Azure SQL 托管实例之间移动数据。 某些方法是 **联机** 的，在运行迁移时，它们会拾取对源所做的全部更改；在 **脱机** 方法中，当迁移正在进行时，你需要停止正在修改源中数据的工作负荷。

| **Source** | **Azure SQL 数据库** | **Azure SQL 托管实例** |
| --- | --- | --- |
| SQL Server（本地、Azure VM、Amazon RDS） | 联机：[事务复制](../managed-instance/replication-transactional-overview.md) <br/> 脱机：[数据迁移服务 (DMS)](/sql/dma/dma-overview)、[BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP | **联机：** [数据迁移服务 (DMS)](/sql/dma/dma-overview)、[事务复制](../managed-instance/replication-transactional-overview.md) <br/> **脱机：** 本机备份/还原、[BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照复制](../managed-instance/replication-transactional-overview.md) |
| 单一数据库 | **脱机：** [BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP | **脱机：** [BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP |
| SQL 托管实例 | **联机：** [事务复制](../managed-instance/replication-transactional-overview.md) <br/> **脱机：** [BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照复制](../managed-instance/replication-transactional-overview.md) | **联机：** [事务复制](../managed-instance/replication-transactional-overview.md) <br/> **脱机：** 跨实例时间点还原（[Azure PowerShell](/powershell/module/az.sql/restore-azsqlinstancedatabase#examples) 或 [Azure CLI](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208)）、[本机备份/还原](../managed-instance/restore-sample-database-quickstart.md)、[BACPAC 文件（导入）](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照复制](../managed-instance/replication-transactional-overview.md) |

## <a name="next-steps"></a>后续步骤

Microsoft 会继续向 Azure SQL 数据库添加功能。 访问针对 Azure 的服务更新网页，并使用以下筛选器获取最新更新：

- 筛选为 [Azure SQL 数据库](https://azure.microsoft.com/updates/?service=sql-database)。
- 筛选为针对 SQL 数据库功能的[正式发布版本 \(GA\) 公告](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

若要详细了解 Azure SQL 数据库和 Azure SQL 托管实例，请参阅：

- [什么是 Azure SQL 数据库？](sql-database-paas-overview.md)
- [什么是 Azure SQL 托管实例？](../managed-instance/sql-managed-instance-paas-overview.md)
- [什么是 Azure SQL 托管实例池？](../managed-instance/instance-pools-overview.md)
