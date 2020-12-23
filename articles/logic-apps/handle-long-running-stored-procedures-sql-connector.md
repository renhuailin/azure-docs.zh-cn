---
title: 在 SQL 连接器中处理长时间运行的存储过程
description: 如何处理在 Azure 逻辑应用中使用 SQL 连接器时超时的存储过程
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102818"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>处理适用于 Azure 逻辑应用的 SQL 连接器中的存储过程超时

如果逻辑应用使用的结果集很大， [SQL 连接器](../connectors/connectors-create-api-sqlazure.md) 不会同时返回所有结果，或者如果想要更好地控制结果集的大小和结构，则可以创建一个 [存储过程](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) ，以便按所需的方式组织结果。 SQL 连接器提供多种后端功能，你可以使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 访问这些功能，以便你可以更轻松地自动执行与 SQL 数据库表一起使用的业务任务。

例如，在获取或插入多个行时，逻辑应用可以通过在这些 [限制](../logic-apps/logic-apps-limits-and-config.md)内使用 [ **Until** 循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)来循环访问这些行。 但是，当逻辑应用必须使用数千或数百万行时，需要将对数据库的调用降到最低。 有关详细信息，请参阅 [使用 SQL 连接器处理大容量数据](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)。

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>存储过程执行的超时限制

SQL 连接器的存储过程超时限制 [小于2分钟](/connectors/sql/#known-issues-and-limitations)。 某些存储过程可能需要比此限制更长的时间才能完成，从而导致 `504 Timeout` 错误。 有时，这些长时间运行的进程将显式编码为此目的的存储过程。 由于超时限制，在 Azure 逻辑应用中调用这些过程可能会产生问题。 尽管 SQL 连接器不以本机方式支持异步模式，但你可以通过使用 SQL 完成触发器、本机 SQL 传递查询、状态表和服务器端作业来解决此问题并模拟此模式。 对于此任务，你可以使用适用于[AZURE SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)的[Azure 弹性作业代理](../azure-sql/database/elastic-jobs-overview.md)。 对于 [本地](/sql/sql-server/sql-server-technical-documentation) 和 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)SQL Server，可以使用 [SQL Server 代理](/sql/ssms/agent/sql-server-agent)。

例如，假设您具有以下长时间运行的存储过程，该存储过程所用的时间超过超时限制，无法完成运行。 如果使用 SQL 连接器从逻辑应用运行此存储过程，将收到一个 `HTTP 504 Gateway Timeout` 错误作为结果。

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

无需直接调用存储过程，只需使用 *作业代理* 即可在后台中异步运行该过程。 你可以将输入和输出存储在状态表中，然后你可以通过逻辑应用与之交互。 如果不需要输入和输出，或者您已将结果写入到存储过程中的表中，则可以简化此方法。

> [!IMPORTANT]
> 请确保存储过程和所有作业都是 *幂等* 的，这意味着它们可以运行多次而不影响结果。 如果异步处理失败或超时，则作业代理可能会多次重试该步骤，进而重试你的存储过程。 若要避免重复输出，请在创建任何对象之前查看这些 [最佳实践和方法](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)。

下一节将介绍如何使用适用于 Azure SQL 数据库的 Azure 弹性作业代理。 对于 SQL Server 和 Azure SQL 托管实例，你可以使用 SQL Server 代理。 某些管理详细信息将有所不同，但基本步骤与为 Azure SQL 数据库设置作业代理的步骤相同。

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>适用于 Azure SQL 数据库的作业代理

若要创建可运行 [AZURE SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)存储过程的作业，请使用 [Azure 弹性作业代理](../azure-sql/database/elastic-jobs-overview.md)。 在 Azure 门户中创建作业代理。 此方法会将多个存储过程添加到代理使用的数据库中，也称为 " *代理数据库* "。 然后，你可以创建一个作业，在目标数据库中运行存储过程，并在完成后捕获输出。

你需要设置权限、组和目标，如 [Azure 弹性作业代理的完整文档](../azure-sql/database/elastic-jobs-overview.md)中所述。 还需要在目标数据库中创建支持表，如以下各节所述。

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>创建用于注册参数和存储输入的状态表

SQL 代理作业不接受输入参数。 相反，在目标数据库中创建一个状态表，你可以在其中注册参数并存储用于调用存储过程的输入。 所有代理作业步骤都针对目标数据库运行，但作业的存储过程是针对代理数据库运行的。 

若要创建状态表，请使用此架构：

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

下面是生成的表在 [SQL Server Management Studio (SMSS) ](/sql/ssms/download-sql-server-management-studio-ssms)的外观：

![显示存储过程输入的已创建状态表的屏幕截图。](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

为了确保良好的性能并确保代理作业可以找到关联的记录，此表使用作业执行 ID (`jobid`) 作为主键。 如果需要，还可以为输入参数添加单独的列。 前面所述的架构可以更常见地处理多个参数，但限制为计算所得的大小 `NVARCHAR(MAX)` 。

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>创建用于运行存储过程的顶级作业

若要执行长时间运行的存储过程，请在代理数据库中创建此顶级作业代理：

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

现在，向作业添加参数化、运行和完成存储过程的步骤。 默认情况下，作业步骤将在12小时后超时。 如果你的存储过程需要更多时间，或如果你想要在前面的过程中超时，则可以将 `step_timeout_seconds` 参数更改为以秒为单位指定的其他值。 默认情况下，步骤具有10个内置重试，每次重试之间的回退超时，可用于你的优势。

下面是要添加的步骤：

1. 等待参数显示在 `LongRunningState` 表中。

   此第一步等待在表中添加参数 `LongRunningState` ，这在作业启动后很快就会发生。 如果作业执行 ID (`jobid`) 不会添加到 `LongRunningState` 表中，该步骤只会失败，并且默认的重试或回退超时会等待：

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 查询状态表中的参数并将其传递给存储过程。 此步骤还会在后台运行该过程。 

   如果存储过程不需要参数，只需直接调用存储过程。 否则，若要传递 `@timespan` 参数，请使用 `@callparams` ，还可以扩展以传递其他参数。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 完成该作业并记录结果。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>启动作业并传递参数

若要启动作业，请将传递本机查询与 [**执行 SQL 查询** 操作](/connectors/sql/#execute-a-sql-query-(v2)) 一起使用，并将作业的参数立即推送到状态表中。 为了向 `jobid` 目标表中的属性提供输入，逻辑应用会添加一个 **for each** 循环，用于遍历上一个操作的表输出。 对于每个作业执行 ID，运行一个使用动态数据输出的 " **插入行** " 操作， `ResultSets JobExecutionId` 以添加作业的参数以解包和传递到目标存储过程。

![显示用于启动作业并将参数传递给存储过程的操作的屏幕截图。](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

当作业完成时，作业将更新 `LongRunningState` 表，以便您可以使用 " [**修改项时** " 触发器](/connectors/sql/#when-an-item-is-modified-(v2))来轻松地对结果进行触发。 如果不需要输出，或者已经有监视输出表的触发器，则可以跳过此部分。

![显示项目修改时间的 SQL 触发器的屏幕截图。](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server 或 Azure SQL 托管实例的作业代理

对于同一方案，可以使用 [SQL Server 代理](/sql/ssms/agent/sql-server-agent) [SQL Server 本地](/sql/sql-server/sql-server-technical-documentation) 和 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。 尽管某些管理细节有所不同，但基本步骤仍与为 Azure SQL Database 设置作业代理的步骤相同。

## <a name="next-steps"></a>后续步骤

[连接到 SQL Server、Azure SQL 数据库或 Azure SQL 托管实例](../connectors/connectors-create-api-sqlazure.md)

