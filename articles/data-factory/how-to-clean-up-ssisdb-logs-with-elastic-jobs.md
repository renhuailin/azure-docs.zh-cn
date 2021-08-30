---
title: 如何自动清理 SSISDB 日志
description: 本文介绍如何通过 Azure 数据工厂、Azure SQL 托管实例代理或弹性数据库作业自动调用相关的 SSISDB 存储过程来清理存储在 SSISDB 中的 SSIS 项目部署和包执行日志。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/28/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6df8c978957bfcbeef87d36d14647ad1caeeb699
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726870"
---
# <a name="how-to-clean-up-ssisdb-logs-automatically"></a>如何自动清理 SSISDB 日志

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 数据工厂 (ADF) 中预配 Azure-SQL Server Integration Services (SSIS) 集成运行时 (IR) 后，可以使用它来运行部署到以下位置的 SSIS 包：

- 由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB)（项目部署模型）
- 由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB)（包部署模型）

在项目部署模型中，Azure-SSIS IR 会将 SSIS 项目部署到 SSISDB 中，从 SSISDB 中提取要运行的 SSIS 包，并将包执行日志写回 SSISDB。 SSISDB 还用于存储 SSIS 作业和 IR 操作日志。 为了管理累积的日志，我们提供了相关的 SSISDB 属性和存储过程，可以通过 ADF、Azure SQL 托管实例代理或弹性数据库作业按计划自动调用这些属性和存储过程。

## <a name="ssisdb-log-clean-up-properties-and-stored-procedures"></a>SSISDB 日志清理属性和存储过程
若要管理 SSIS 包执行日志，可以使用 SQL Server Management Studio (SSMS) 连接到由 Azure SQL 数据库服务器/托管实例托管的 SSISDB 来配置 SSISDB 日志清理属性，请参阅[连接到 SSISDB](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial?view=sql-server-ver15&preserve-view=true#connect-to-the-ssisdb-database)。 连接后，在 SSMS 的“对象资源管理器”窗口中，可以展开“Integration Services 目录”节点，右键单击“SSISDB”子节点，然后选择“属性”菜单项以打开“目录属性”对话框    。 在“目录属性”对话框中，可以找到以下 SSISDB 日志清理属性：

- **定期清理日志**：启用包执行日志的清理，默认情况下设置为“True”。
- **保持期（天数）** ：指定保留日志的最长时间（以天为单位），默认情况下设置为 365，而较旧的日志会在调用相关的 SSISDB 存储过程时被删除。
- **定期删除旧版本**：启用存储项目版本的清理，默认情况下设置为“True”。
- **每个项目的最大版本数量**：指定存储项目版本的最大数量，默认情况下设置为 10，而较旧的日志会在调用相关的 SSISDB 存储过程时被删除。

![SSISDB 日志清理属性](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-ssisdb-properties.png)

配置 SSISDB 日志清理属性后，可以调用相关的 SSISDB 存储过程 `[internal].[cleanup_server_retention_window_exclusive]` 来清理 SSIS 包执行日志。

若要清理 SSIS 作业日志，可以调用相关的 SSISDB 存储过程 `[internal].[cleanup_completed_jobs_exclusive]`。 默认情况下，保持期设置为 60 分钟，并且较旧的日志会在调用存储过程时被删除。

若要清理 SSIS IR 操作日志，可以调用相关的 SSISDB 存储过程 `[internal].[cleanup_expired_worker]`。 默认情况下，保持期设置为 168 小时，并且较旧的日志会在调用存储过程时被删除。

这些 SSISDB 存储过程会清理不同的 SSISDB 表：

| SSISDB 存储过程 | 要清理的 SSISDB 表 |
|--------------------------|---------------------------|
| `[internal].[cleanup_server_retention_window_exclusive]` | `[internal].[operations]`<br/><br/>`[internal].[operation_messages_scaleout]`<br/><br/>`[internal].[event_messages_scaleout]`<br/><br/>`[internal].[event_message_context_scaleout]` |
| `[internal].[cleanup_completed_jobs_exclusive]` | `[internal].[jobs]`<br/><br/>`[internal].[tasks]`<br/><br/>`[internal].[job_worker_agents]` |
| `[internal].[cleanup_expired_worker]` | `[internal].[worker_agents]` |
 
还可以通过 ADF、Azure SQL 托管实例代理或弹性数据库作业按计划自动调用这些 SSISDB 存储过程。

## <a name="clean-up-ssisdb-logs-automatically-via-adf"></a>通过 ADF 自动清理 SSISDB 日志
无论是否使用 Azure SQL 数据库服务器/托管实例来托管 SSISDB，都可以始终按计划使用 ADF 来自动清理 SSISDB 日志。 为此，可以在 ADF 管道中准备一个具有嵌入包的“执行 SSIS 包”活动，其中包含调用相关 SSISDB 存储过程的单个执行 SQL 任务。 请参阅博客：[在 Azure 数据工厂中使用 SSIS 通过 3 个简单步骤运行任意 SQL](https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244) 中的示例 4。

![通过 ADF 清理 SSISDB 日志](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/run-sql-ssis-activity-ssis-parameters-ssisdb-clean-up.png)

对于 SQLStatementSource 参数，可以输入 `EXEC internal.cleanup_server_retention_window_exclusive` 来清理 SSIS 包执行日志。 

若要清理 SSIS 作业日志，可以添加 `EXEC internal.cleanup_completed_jobs_exclusive [@minutesToKeep=’Number of minutes to set as retention period’]`。 

若要清理 SSIS IR 操作日志，可以添加 `EXEC internal.cleanup_expired_worker [@hoursToKeep=’Number of hours to set as retention period’] `。

准备好 ADF 管道后，可以附加计划触发器来定期运行它，请参阅[如何按计划触发 ADF 管道](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="clean-up-ssisdb-logs-automatically-via-azure-sql-managed-instance-agent"></a>通过 Azure SQL 托管实例代理自动清理 SSISDB 日志
如果使用 Azure SQL 托管实例来托管 SSISDB，还可以使用其内置作业协调程序/计划程序、Azure SQL 托管实例代理来按计划自动清理 SSISDB 日志。 如果最近在 Azure SQL 托管实例中创建了 SSISDB，则还在 Azure SQL 托管实例代理中创建了一个名为“SSIS 服务器维护作业”的 T SQL 作业，用于专门清理 SSIS 包执行日志。 默认情况下，它处于禁用状态并配置为每天运行一次。  若要启用它或重新配置其计划，可以通过使用 SSMS 连接到 Azure SQL 托管实例来实现此目的。 连接后，在 SSMS 的“对象资源管理器”窗口中，可以依次展开“SQL Server 代理”节点、“作业”子节点，然后双击“SSIS 服务器维护作业”来启用/重新配置它   。

![通过 Azure SQL 托管实例代理进行 SSISDB 日志清理](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-maintenance-job.png)

如果 Azure SQL 托管实例代理尚未在其下创建“SSIS 服务器维护作业”，则可以通过在 Azure SQL 托管实例上运行以下 T-SQL 脚本来手动添加它。

```sql
USE msdb
IF EXISTS(SELECT * FROM sys.server_principals where name = '##MS_SSISServerCleanupJobLogin##')
   DROP LOGIN ##MS_SSISServerCleanupJobLogin##

DECLARE @loginPassword nvarchar(256)
SELECT @loginPassword = REPLACE (CONVERT( nvarchar(256), CRYPT_GEN_RANDOM( 64 )), N'''', N'''''')
EXEC ('CREATE LOGIN ##MS_SSISServerCleanupJobLogin## WITH PASSWORD =''' +@loginPassword + ''', CHECK_POLICY = OFF')
ALTER LOGIN ##MS_SSISServerCleanupJobLogin## DISABLE

USE master
GRANT VIEW SERVER STATE TO ##MS_SSISServerCleanupJobLogin##

USE SSISDB
IF EXISTS (SELECT name FROM sys.database_principals WHERE name = '##MS_SSISServerCleanupJobUser##')
    DROP USER ##MS_SSISServerCleanupJobUser##
CREATE USER ##MS_SSISServerCleanupJobUser## FOR LOGIN ##MS_SSISServerCleanupJobLogin##
GRANT EXECUTE ON [internal].[cleanup_server_retention_window_exclusive] TO ##MS_SSISServerCleanupJobUser##
GRANT EXECUTE ON [internal].[cleanup_server_project_version] TO ##MS_SSISServerCleanupJobUser##

USE msdb
EXEC dbo.sp_add_job
    @job_name = N'SSIS Server Maintenance Job', 
    @enabled = 0,
    @owner_login_name = '##MS_SSISServerCleanupJobLogin##',
    @description = N'Runs every day. The job removes operation records from the database that are outside the retention period and maintains a maximum number of versions per project.'

DECLARE @IS_server_name NVARCHAR(30)
SELECT @IS_server_name = CONVERT(NVARCHAR, SERVERPROPERTY('ServerName'))
EXEC sp_add_jobserver  @job_name = N'SSIS Server Maintenance Job',
                       @server_name = @IS_server_name

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Operation Records Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_retention_window_exclusive]',
    @database_name = N'msdb',
    @on_success_action = 3,
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Max Version Per Project Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_project_version]',
    @database_name = N'msdb',
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobschedule
    @job_name = N'SSIS Server Maintenance Job',
    @name = 'SSISDB Scheduler',
    @enabled = 1,
    @freq_type = 4, /*daily*/
    @freq_interval = 1,/*every day*/
    @freq_subday_type = 0x1,
    @active_start_date = 20001231,
    @active_end_date = 99991231,
    @active_start_time = 0,
    @active_end_time = 120000
```

还可以配置现有“SSIS 服务器维护作业”或修改上述 T-SQL 脚本，以通过调用相关的 SSISDB 存储过程来清理 SSIS 作业/IR 操作日志。

## <a name="clean-up-ssisdb-logs-automatically-via-elastic-database-jobs"></a>通过弹性数据库作业自动清理 SSISDB 日志
如果使用 Azure SQL 数据库服务器来托管 SSISDB，则它没有内置作业协调程序/计划程序，因此必须使用外部组件（例如 ADF (参见上文) 或弹性数据库作业 (参见本章节的剩余部分) 才能按计划自动清理 SSISDB 日志。

弹性数据库作业是一项 Azure 服务，它可以针对一个或一组数据库自动执行和运行作业。 可以使用 Azure 门户、Azure PowerShell、T-SQL 或 REST API 来计划、运行和监视这些作业。 使用弹性数据库作业调用相关 SSISDB 存储过程，从而一次性或按计划清理日志。 可以根据 SSISDB 资源使用情况来选择计划时间间隔，避免数据库负载过重。

有关详细信息，请参阅[通过弹性数据库作业管理数据库组](../azure-sql/database/elastic-jobs-overview.md)。

以下各节介绍如何调用相关的 SSISDB 存储过程 `[internal].[cleanup_server_retention_window_exclusive]`/`[internal].[cleanup_completed_jobs_exclusive]`/`[internal].[cleanup_expired_worker]`，这些存储过程会删除在其特定保持期之外的 SSISDB 日志。

### <a name="configure-elastic-database-jobs-using-azure-powershell"></a>使用 Azure PowerShell 配置弹性数据库作业

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

以下 Azure PowerShell 脚本将创建一个新的弹性作业，该作业将调用所选的 SSISDB 日志清理存储过程。 有关详细信息，请参阅[使用 PowerShell 创建弹性作业代理](../azure-sql/database/elastic-jobs-powershell-create.md)。

#### <a name="create-parameters"></a>创建参数

``` powershell
# Parameters needed to create your job database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL Database server, for example myjobserver, to hold your job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for your job database to be created in the given Azure SQL Database server"),
$StoredProcName = $(Read-Host "Please enter the name of SSISDB log clean-up stored procedure to be invoked by your job (internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker)"), 

# Your job database should be a clean, empty S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create your Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your Elastic Job agent"),

# Parameters needed to create credentials in your job database for connecting to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for the log clean-up job user to connect to SSISDB"),

# Parameters needed to create the login and user for SSISDB
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of target Azure SQL Database server that contains SSISDB, for example myssisdbserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set the job schedule for invoking SSISDB log clean-up stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run your job that cleans up SSISDB logs outside their retention period immediately (Y/N). Make sure the specific retention period is set properly before running the following scripts as deleted logs cannot be recovered."),
$IntervalType = $(Read-Host "Please enter the interval type for SSISDB log clean-up schedule: Year, Month, Day, Hour, Minute, Second are supported."),
$IntervalCount = $(Read-Host "Please enter the count of interval type for SSISDB log clean-up schedule."),

# The start time for SSISDB log clean-up schedule is set to current time by default. 
$StartTime = (Get-Date)
```

#### <a name="invoke-ssisdb-log-clean-up-stored-procedure"></a>调用 SSISDB 日志清理存储过程

```powershell
# Install the latest PowerShell PackageManagement module that PowerShellGet v1.6.5 depends on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart your PowerShell session
# Install the latest PowerShellGet module that adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Install AzureRM.Sql preview cmdlets side by side with the existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create your job database for defining SSISDB log clean-up job and tracking the job history
Write-Output "Creating a blank SQL database to be used as your job database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable Elastic Database Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create your Elastic Job agent
Write-Output "Creating your Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create job credentials in your job database for connecting to SSISDB in target server
Write-Output "Creating job credentials for connecting to SSISDB..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# Create the job user login in master database of target server
Write-Output "Grant permissions on the master database of target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# Create SSISDB log clean-up user from login in SSISDB and grant it permissions to invoke SSISDB log clean-up stored procedure
Write-Output "Grant appropriate permissions on SSISDB..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON " + $StoredProcName + " TO SSISDBLogCleanupUser" 

$TargetDatabase | ForEach-Object -Process {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create your target group that includes only SSISDB to clean up
Write-Output "Creating your target group that includes only SSISDB to clean up..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create your job to invoke SSISDB log clean-up stored procedure
Write-Output "Creating your job to invoke SSISDB log clean-up stored procedure..."
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add your job step to invoke SSISDB log clean-up stored procedure
Write-Output "Adding your job step to invoke SSISDB log clean-up stored procedure..."
$SqlText = "EXEC " + $StoredProcName 
$Job | Add-AzureRmSqlElasticJobStep -Name "Step to invoke SSISDB log clean-up stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run your job to immediately invoke SSISDB log clean-up stored procedure once
if ($RunJobOrNot -eq 'Y')
{
Write-Output "Invoking SSISDB log clean-up stored procedure immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule your job to invoke SSISDB log clean-up stored procedure periodically, deleting SSISDB logs outside their retention period
Write-Output "Starting your schedule to invoke SSISDB log clean-up stored procedure periodically..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

### <a name="configure-elastic-database-jobs-using-t-sql"></a>使用 T-SQL 配置弹性数据库作业

以下 T-SQL 脚本将创建一个新的弹性作业，该作业将调用所选的 SSISDB 日志清理存储过程。 有关详细信息，请参阅[使用 T-SQL 创建和管理弹性数据库作业](../azure-sql/database/elastic-jobs-tsql-create-manage.md)。

1. 为作业数据库确定一个 Azure SQL 数据库的空 S0/更高服务层，或创建一个新的新服务层。 然后在 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent)中创建弹性作业代理。

2. 在作业数据库中，创建用于连接到目标服务器中 SSISDB 的凭据。

   ```sql
   -- Connect to the job database specified when creating your job agent.
   -- Create a database master key if one doesn't already exist, using your own password.
   CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';

   -- Create credentials for SSISDB log clean-up.
   CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
   ```

3. 定义仅包含要清理的 SSISDB 的目标组。

   ```sql
   -- Connect to your job database.
   -- Add your target group.
   EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

   -- Add SSISDB to your target group
   EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
   @target_type = 'SqlDatabase',
   @server_name = '<EnterSSISDBTargetServerName>',
   @database_name = 'SSISDB'

   -- View your recently created target group and its members.
   SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
   SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
   ```

4. 根据 SSISDB 中的登录名创建 SSISDB 日志清理用户，并向其授予调用 SSISDB 日志清理存储过程的权限。 如需详细指导，请参阅[管理登录名](../azure-sql/database/logins-create-manage.md)。

   ```sql
   -- Connect to the master database of target server that hosts SSISDB 
   CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

   -- Connect to SSISDB
   CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
   GRANT EXECUTE ON '<internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker>' TO SSISDBLogCleanupUser 
   ```

5. 创建作业并添加作业步骤来调用 SSISDB 日志清理存储过程。

   ```sql
   -- Connect to your job database.
   -- Add your job to invoke the relevant SSISDB log clean-up stored procedure.
   EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs outside their specific retention period'

   -- Add your job step to invoke the relevant SSISDB log clean-up stored procedure
   EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
   @command=N'<EXEC internal.cleanup_server_retention_window_exclusive/EXEC internal.cleanup_completed_jobs_exclusive/EXEC internal.cleanup_expired_worker>',
   @credential_name='SSISDBLogCleanupCred',
   @target_group_name='SSISDBTargetGroup'
   ```

6. 在继续操作之前，请确保正确设置了特定的保持期。 此时间段之外的 SSISDB 日志会被删除，并且无法恢复。 然后，你可以立即运行作业来启动 SSISDB 日志清理。

   ```sql
   -- Connect to your job database.
   -- Run your job immediately to invoke SSISDB log clean-up stored procedure.
   declare @je uniqueidentifier
   exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

   -- Watch SSISDB log clean-up results 
   select @je
   select * from jobs.job_executions where job_execution_id = @je
   ```

7. 还可以选择根据计划删除保持期之外的 SSISDB 日志。 按如下所示配置作业参数。

   ```sql
   -- Connect to your job database.
   EXEC jobs.sp_update_job
   @job_name='CleanupSSISDBLog',
   @enabled=1,
   @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
   @schedule_interval_count='<EnterDetailedIntervalValue>',
   @schedule_start_time='<EnterProperStartTimeForSchedule>',
   @schedule_end_time='<EnterProperEndTimeForSchedule>'
   ```

### <a name="monitor-ssisdb-log-clean-up-job-using-azure-portal"></a>使用 Azure 门户监视 SSISDB 日志清理作业

在 Azure 门户中监视 SSISDB 日志清理作业。 对于每次执行，都可以查看作业的状态、开始时间和结束时间。

![在 Azure 门户中监视 SSISDB 日志清理作业](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

### <a name="monitor-ssisdb-log-clean-up-job-using-t-sql"></a>使用 T-SQL 监视 SSISDB 日志清理作业

还可使用 T-SQL 来查看 SSISDB 日志清理作业的执行历史记录。

```sql
-- Connect to your job database.
-- View all SSISDB log clean-up job executions.
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions.
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>后续步骤

若要管理和监视 Azure-SSIS IR，请参阅以下文章。

- [重新配置 Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)

- [监视 Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
