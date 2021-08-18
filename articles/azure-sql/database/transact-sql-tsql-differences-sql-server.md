---
title: 解析 T-SQL 差异 - 迁移
description: 在 Azure SQL 数据库中不完全支持的 T-SQL 语句。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 06/17/2021
ms.openlocfilehash: 479800be0301d40fb1f20127f3d35fd11e7ba69e
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004585"
---
# <a name="t-sql-differences-between-sql-server-and-azure-sql-database"></a>SQL Server 与 Azure SQL 数据库之间的 T-SQL 差异

从 SQL Server [将数据库迁移](migrate-to-database-from-sql-server.md)到 Azure SQL 数据库时，你可能会发现需要先对 SQL Server 数据库进行一些重新设计，然后才能迁移它们。 本文提供相关指南来帮助你执行此重新设计和了解重新设计是必需的基本原因。 若要检测不兼容性并将数据库迁移到 Azure SQL 数据库，请使用[数据迁移助手 (DMA)](/sql/dma/dma-overview)。

## <a name="overview"></a>概述

Microsoft SQL Server 和 Azure SQL 数据库都完全支持应用程序使用的大多数 T-SQL 功能。 例如，核心 SQL 组件（如数据类型、运算符、字符串、算术、逻辑和游标函数等）在 SQL Server 和 SQL 数据库中的工作方式相同。 但是，DDL（数据定义语言）和 DML（数据操作语言）元素中的一些 T-SQL 差异导致存在仅部分受支持的 T-SQL 语句和查询（我们会在本文后面的内容中介绍）。

此外，还有一些功能和语法根本不受支持，因为 Azure SQL 数据库的设计使其隔离功能与系统数据库和操作系统的依赖项。 因此，SQL 数据库不支持大多数实例级功能。 T-SQL 语句和选项在配置实例级选项、操作系统组件或指定文件系统配置时不可用。 需要此类功能时，通常是以某种其他方式从 SQL 数据库或从其他 Azure 功能或服务获取相应的替代项。

例如，高可用性已内置于 Azure SQL 数据库中。 SQL 数据库不支持与可用性组相关的 T-SQL 语句，也不支持与 Always On 可用性组相关的动态管理视图。

有关 SQL 数据库支持和不支持的功能的列表，请参阅 [Azure SQL 数据库功能比较](features-comparison.md)。 此页对该篇文章进行了补充，并重点介绍了 T-SQL 语句。

## <a name="t-sql-syntax-statements-with-partial-differences"></a>具有部分差异的 T-SQL 语法语句

核心 DDL 语句可用，但与不受支持功能相关的 DDL 语句扩展（如磁盘上的文件放置）不受支持。

- 在 SQL Server 中，`CREATE DATABASE` 和 `ALTER DATABASE` 语句具有超过 36 个的选项。 这些语句包括文件定位、FILESTREAM 以及仅适用于 SQL Server 的服务中转站选项。 如果在迁移前在 SQL 数据库中创建数据库，这可能不是问题，但如果要迁移用于创建数据库的 T-SQL 代码，应将 [CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)与 [CREATE DATABASE (SQL Server T-SQL)](/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver15&preserve-view=true) 中的 SQL Server 语法进行比较，以确保所用的所有选项都受支持。 Azure SQL 数据库的 `CREATE DATABASE` 还具有服务目标和仅适用于 SQL 数据库的弹性池选项。
- `CREATE TABLE` 和 `ALTER TABLE` 语句具有不能在 SQL 数据库上使用的 `FILETABLE` 和 `FILESTREAM` 选项，因为这些功能不受支持。
- `CREATE LOGIN` 和 `ALTER LOGIN` 语句受支持，但不提供 SQL Server 中可用的所有选项。 要使数据库更易于移植，SQL 数据库建议尽可能使用包含的数据库用户，而不是使用登录名。 有关详细信息，请参阅 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true)、[ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql?view=azuresqldb-current&preserve-view=true) 和[管理登录名和用户](logins-create-manage.md)。

## <a name="t-sql-syntax-not-supported-in-azure-sql-database"></a>Azure SQL 数据库不支持的 T-SQL 语法

除了与 [Azure SQL 数据库功能比较](features-comparison.md)中所述的不受支持的功能相关的 T-SQL 语句外，也不支持以下语句和语句组。 因此，如果要迁移的数据库使用以下任一功能，请重新设计应用程序以消除这些 T-SQL 功能和语句。

- 系统对象的排序规则。
- 相关连接：终结点语句。 SQL 数据库不支持 Windows 身份验证，但支持 Azure Active Directory 身份验证。 这包括对与 Azure Active Directory 联合的 Active Directory 主体的身份验证。 有关详细信息，请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库或 Azure Synapse Analytics](authentication-aad-overview.md)。
- 使用由三部分或四部分构成的名称的跨数据库和跨实例查询。 支持引用 `tempdb` 数据库和当前数据库的由三部分构成的名称。 [弹性查询](elastic-query-overview.md)支持对其他 MSSQL 数据库中的表进行只读引用。
- 跨数据库所有权链接和 `TRUSTWORTHY` 数据库属性。
- `EXECUTE AS LOGIN`. 请改用 `EXECUTE AS USER`。
- 加密密钥的可扩展密钥管理 (EKM)。 透明数据加密 (TDE) [客户管理的密钥](transparent-data-encryption-byok-overview.md)和 Always Encrypted [列主密钥](always-encrypted-azure-key-vault-configure.md)可能存储在 Azure Key Vault 中。
- 事件处理：事件通知、查询通知。
- 文件属性：与数据库文件名、位置、大小和其他由 SQL 数据库自动管理的文件属性相关的语法。
- 高可用性：与高可用性和数据库恢复相关的语法，由 SQL 数据库管理。 这包括备份、还原、Always On、数据库镜像、日志传送、恢复模型的语法。
- 与快照、事务和合并复制相关的语法，在 SQL 数据库中不可用。 支持[复制订阅](replication-to-sql-database.md)。
- 函数：`fn_get_sql`、`fn_virtualfilestats`、`fn_virtualservernodes`。
- 实例配置：与服务器内存、工作线程、CPU 相关性、跟踪标志相关的语法。 请改用服务层级和计算大小。
- `KILL STATS JOB`.
- `OPENQUERY`、`OPENDATASOURCE` 和由四部分构成的名称。
- .NET Framework：CLR 集成
- 语义搜索
- 服务器凭据：改用[数据库范围的凭据](/sql/t-sql/statements/create-database-scoped-credential-transact-SQL)。
- 服务器级权限：服务器级权限 `GRANT`、`REVOKE` 和 `DENY` 不受支持。 某些服务器级权限已替换为数据库级权限，或由内置服务器角色隐式授予。 一些服务器级 DMV 和目录视图具有类似的数据库级视图。
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` 和 `RECONFIGURE`。 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) 受支持。
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server 代理：依赖于 SQL Server 代理或 MSDB 数据库的语法：警报、运算符、中央管理服务器。 改用脚本，如 PowerShell。
- SQL Server 审核：改用 SQL 数据库[审核](auditing-overview.md)。
- SQL Server 跟踪。
- 跟踪标志。
- T-SQL 调试。
- 服务器作用域或登录触发器。
- `USE` 语句：要将数据库上下文更改为不同的数据库，必须与该数据库建立新连接。

## <a name="full-t-sql-reference"></a>完整的 T-SQL 参考

有关 T-SQL 语法、用法和示例的详细信息，请参阅 [T-SQL 参考（数据库引擎）](/sql/t-sql/language-reference)。

### <a name="about-the-applies-to-tags"></a>有关“适用于”标记

T-SQL 参考包括与所有最新 SQL Server 版本相关的文章。 文章标题下面有一个图标栏，其中列出了 MSSQL 平台，并指明了适用性。 例如，SQL Server 2012 中引入了可用性组。 [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) 一文指明该语句适用于 SQL Server（从版本 2012 开始）。 该语句不适用于 SQL Server 2008、SQL Server 2008 R2、Azure SQL 数据库、Azure Synapse Analytics 或并行数据仓库。

在某些情况下，产品中可能使用了某篇文章的常规主旨，但产品之间存在细微差异。 在适当的情况下，我们会在文章的中间位置指出该差异。 例如，`CREATE TRIGGER` 文章在 SQL 数据库中可用。 但服务器级触发器的 `ALL SERVER` 选项指示不能在 SQL 数据库中使用服务器级触发器。 请改用数据库级触发器。

## <a name="next-steps"></a>后续步骤

有关 SQL 数据库支持和不支持的功能的列表，请参阅 [Azure SQL 数据库功能比较](features-comparison.md)。

要在迁移到 Azure SQL 数据库之前检测 SQL Server 数据库中的兼容性问题并迁移数据库，请使用[数据迁移助手 (DMA)](/sql/dma/dma-overview)。