---
title: 在 Azure SQL 托管实例中使用 SQL 代理作业实现作业自动化
description: 用于在 Azure SQL 托管实例中运行 Transact-SQL (T-SQL) 脚本的自动化选项
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 326ac64d5ea8948b9f9990a76dbbcf76d9915d5a
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414900"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>在 Azure SQL 托管实例中使用 SQL 代理作业自动执行管理任务
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

使用 SQL Server 中的 [SQL Server 代理](/sql/ssms/agent/sql-server-agent)和 [SQL 托管实例](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)，可以创建和计划可针对一个或多个数据库定期执行的作业，以运行 Transact-SQL (T-SQL) 查询和执行维护任务。 本文介绍了 SQL 托管实例的 SQL 代理。

> [!Note]
> SQL 代理不适用于 Azure SQL 数据库或 Azure Synapse Analytics。 相反，建议[使用弹性作业实现作业自动化](job-automation-overview.md)。

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Azure SQL 托管实例中的 SQL 代理作业限制

值得注意的是，SQL Server 中提供的 SQL 代理与 SQL 托管实例中的 SQL 代理之间存在差异。 若要详细了解 SQL Server 和 SQL 托管实例支持的功能之间的差异，请参阅 [Azure SQL 托管实例与 SQL Server 的 T-SQL 差异](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)。 

在 SQL Server 中可用的某些 SQL 代理功能在 SQL 托管实例中不受支持：

- SQL 代理设置为只读。 
    - SQL 托管实例中不支持系统存储过程 `sp_set_agent_properties`。
- 目前，SQL 托管实例不支持启用/禁用 SQL 代理。 SQL 代理始终运行。
- 部分支持通知：
  - 不支持寻呼机。
  - 不支持 NetSend。
  - 不支持警报。
- 不支持代理。
- 不支持事件日志。
- 不支持基于空闲 CPU 的作业计划触发器。

## <a name="when-to-use-sql-agent-jobs"></a>何时使用 SQL 代理作业 

SQL 代理作业有多种使用场景：

- 自动完成管理任务，并将作业计划为在每个工作日、数小时之后或按其他频率运行。
  - 部署架构更改、凭据管理、性能数据收集或租户（客户）遥测数据收集。
  - 更新引用数据（所有数据库的公用信息）、从 Azure Blob 存储加载数据。
  - 常见维护任务，包括用于确保数据完整性的 DBCC CHECKDB，或用于提高查询性能的索引维护。 配置作业，以便定期（例如，在非高峰时段）对一系列数据库执行作业。
  - 持续将一组数据库中的查询结果收集到中央表中。 性能查询可以持续执行，并可配置为触发执行其他任务。
- 收集要报告的数据
  - 将数据库集合中的数据聚合到单个目标表中。
  - 对大量的数据库执行长时间运行的数据处理查询，例如，收集客户遥测数据。 结果将收集到单个目标表以供进一步分析。
- 数据移动
  - 创建作业，用于将数据库中所做的更新复制到其他数据库，或者收集远程数据库中所做的更新，并在数据库中应用更改。
  - 创建作业用于通过 SQL Server Integration Services (SSIS) 从/向数据库加载数据。

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL 托管实例中的 SQL 代理作业

SQL 代理作业由 SQL 代理服务执行，该服务继续用于 SQL Server 和 SQL 托管实例中的任务自动化。 

SQL 代理作业是针对数据库指定的 T-SQL 脚本系列。 使用作业能够定义可一次或多次运行的，并且可以监视其成功或失败状态的管理任务。 

一个作业可在一台本地服务器或者多台远程服务器上运行。 SQL 代理作业是内部的数据库引擎组件，在 SQL 托管实例服务中执行。

SQL 代理作业有几个关键概念：

- **作业步骤** 集是指应在作业中执行的一个或多个步骤。 对于每个作业步骤，可以定义重试策略，以及该作业步骤成功或失败时应执行的操作。
- **计划** 定义何时应执行该作业。
- 使用 **通知** 可以定义作业完成后，用于通过电子邮件通知操作员的规则。

### <a name="sql-agent-job-steps"></a>SQL 代理作业步骤

SQL 代理作业步骤是 SQL 代理应执行的操作序列。 每个步骤包含该步骤成功或失败时应执行的后续步骤，以及失败时的重试次数。

SQL 代理允许创建不同类型的作业步骤，例如，用于针对数据库执行单个 Transact-SQL 批处理的 Transact-SQL 作业步骤、可执行自定义 OS 脚本的 OS 命令/PowerShell 步骤、用于通过 SSIS 运行时加载数据的 [SSIS 作业步骤](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md)，或者可将数据库中的更改发布到其他数据库的[复制](../managed-instance/replication-transactional-overview.md)步骤。

> [!Note]
> 若要详细了解如何结合使用 Azure SSIS Integration Runtime 与 Azure SQL 托管实例托管的 SSISDB，请参阅[在 Azure 数据工厂中结合使用 Azure SQL 托管实例和 SQL Server Integration Services (SSIS)](../../data-factory/how-to-use-sql-managed-instance-with-ir.md)。

[事务复制](../managed-instance/replication-transactional-overview.md)可将表中的更改复制到 Azure SQL 托管实例、Azure SQL 数据库或 SQL Server 中的其他数据库。 若要了解信息，请参阅[在 Azure SQL 托管实例中配置复制](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md)。 

SQL 托管实例中目前不支持其他类型的工作步骤，包括：

- 不支持合并复制作业步骤。
- 不支持队列读取器。
- 不支持 Analysis Services
 
### <a name="sql-agent-job-schedules"></a>SQL 代理作业计划

计划指定运行作业的时间。 多个作业可按同一计划运行，可将多个计划应用到同一作业。

计划可为作业运行时间定义以下条件：

- 每当 SQL Server 代理启动时。 每次故障转移后会激活作业。
- 在特定的日期和时间运行一次，这对于延迟执行某些作业非常有用。
- 按重复的计划运行。

> [!Note]
> SQL 托管实例目前不允许在 CPU 空闲时启动作业。

### <a name="sql-agent-job-notifications"></a>SQL 代理作业通知

当作业成功完成或失败时，SQL 代理作业可让你接收通知。 可以通过电子邮件接收通知。

如果未启用，首先需要在 Azure SQL 托管实例上配置[数据库邮件功能](/sql/relational-databases/database-mail/database-mail)：

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

例如，设置将用于发送电子邮件通知的电子邮件帐户。 将帐户分配给名为 `AzureManagedInstance_dbmail_profile` 的电子邮件配置文件。 若要在 SQL 托管实例中使用 SQL 代理作业发送电子邮件，应创建一个配置文件且名称必须为 `AzureManagedInstance_dbmail_profile`。 否则，SQL 托管实例将无法通过 SQL 代理发送电子邮件。 请查看以下示例：

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

使用 [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 系统存储过程通过 T-SQL 测试数据库邮件配置：

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

可以通知操作员 SQL 代理作业发生了问题。 操作员为一个或多个 SQL 托管实例中的实例的维护负责人定义联系信息。 有时，操作员职责会分配给一个人。

在包含多个 SQL 托管实例中的实例或 SQL Server 的系统中，可由多个人分担操作员的职责。 操作员不涉及安全信息，因此不会定义安全主体。 理想情况下，操作员不应是其责任可能发生变化的个人，而应是电子邮件通讯组。   

可以使用 SQL Server Management Studio (SSMS) 或以下示例中所示的 Transact-SQL 脚本[创建操作员](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql)：

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

通过 SSMS 中的[数据库邮件日志](/sql/relational-databases/database-mail/database-mail-log-and-audits)确认电子邮件是成功还是失败。

然后可以使用 SSMS 或以下 Transact-SQL 脚本[修改任何 SQL 代理作业](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql)并分配操作员，以便在作业完成、失败或成功时向其发送电子邮件通知：

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>SQL 代理作业历史记录

Azure SQL 托管实例当前不允许更改任何 SQL 代理属性，因为这些属性存储在基础注册表值中。 这意味着，用于调整作业历史记录的代理保留策略的选项固定为默认的 1000 条总记录，每个作业至多 100 条历史记录。

### <a name="sql-agent-fixed-database-role-membership"></a>SQL 代理固定数据库角色成员身份

如果将链接到非 sysadmin 登录名的用户添加到 msdb 系统数据库中三个 SQL 代理固定数据库角色中的任何一个角色，则会出现以下问题：需要向主存储过程授予显式 EXECUTE 权限才能使这些登录名有效。 如果遇到此问题，将显示错误消息“在对象 <object_name> 中拒绝了 EXECUTE 权限(Microsoft SQL Server，错误:229)”。 

将用户添加到 msdb 中的 SQL 代理固定数据库角色（SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgentOperatorRole）后，对于添加到这些角色的每个用户登录名，请执行以下 T-SQL 脚本，向列出的系统存储过程显式授予 EXECUTE 权限。 此示例假定用户名与登录名相同。 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>了解详细信息

- [什么是 Azure SQL 托管实例？](../managed-instance/sql-managed-instance-paas-overview.md)
- [Azure SQL 数据库和 SQL 托管实例中的新增功能有哪些？](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Azure SQL 托管实例与 SQL Server 的 T-SQL 差异](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [功能比较：Azure SQL 数据库和 Azure SQL 托管实例](../../azure-sql/database/features-comparison.md)
