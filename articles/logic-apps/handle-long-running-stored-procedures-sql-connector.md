---
title: 处理 SQL 连接器中长时间运行的存储过程
description: 在 Azure 逻辑应用中使用 SQL 连接器时如何处理超时的存储过程
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102818"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>处理适用于 Azure 逻辑应用的 SQL 连接器中的存储过程超时

如果逻辑应用处理的结果集很大，导致 [SQL 连接器](../connectors/connectors-create-api-sqlazure.md)不会同时返回所有结果，或者如果你希望更好地控制结果集的大小和结构，则可以创建一个[存储过程](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)，按照想要的方式组织结果。 SQL 连接器提供了许多可以使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)访问的后端功能，以便你更轻松地自动执行处理 SQL 数据库表的业务任务。

例如，获取或插入多个行时，逻辑应用可以在这些[限制](../logic-apps/logic-apps-limits-and-config.md)中使用 [Until loop](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 来循环访问这些行。 但是，当逻辑应用必须处理数千或数百万行内容时，你希望将调用数据库的成本降到最低。 有关详细信息，请参阅[使用 SQL 连接器处理批量数据](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)。

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>存储过程执行的超时限制

SQL 连接器的存储过程超时限制是[小于 2 分钟](/connectors/sql/#known-issues-and-limitations)。 某些存储过程可能需要超过此限制才能完成，从而导致 `504 Timeout` 错误。 有时，出于此目的，这些长时间运行的进程被显式编码为存储过程。 由于超时限制，从 Azure 逻辑应用调用这些过程可能会出现问题。 尽管 SQL 连接器不本机支持异步模式，但你可以使用 SQL 完成触发器、本机 SQL 传递查询、状态表和服务器端作业来绕过此问题并模拟此模式。 对于此任务，可使用适用于 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)的 [Azure 弹性作业代理](../azure-sql/database/elastic-jobs-overview.md)。 对于[本地 SQL Server](/sql/sql-server/sql-server-technical-documentation) 和 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)，可使用 [SQL Server 代理](/sql/ssms/agent/sql-server-agent)。

例如，假设你有以下长时间运行的存储过程，完成运行需要的时间比超时限制长。 如果使用 SQL 连接器从逻辑应用运行此存储过程，则结果中会出现 `HTTP 504 Gateway Timeout` 错误。

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

可以使用作业代理在后台异步运行该过程，而不是直接调用存储过程。 可以将输入和输出存储在状态表中，然后可以通过逻辑应用与之交互。 如果不需要输入和输出，或者已经将结果写入存储过程中的表，则可以简化此方法。

> [!IMPORTANT]
> 请确保存储过程和所有作业都是幂等的，这意味着它们可以运行多次而不影响结果。 如果异步处理失败或超时，作业代理可能会多次重试该步骤，进而重试存储过程。 若要避免重复输出，请在创建任何对象之前，查看这些[最佳做法和方法](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)。

下一部分介绍如何使用 Azure SQL 数据库的 Azure 弹性作业代理。 对于 SQL Server 和 Azure SQL 托管实例，可以使用 SQL Server 代理。 某些管理详细信息会有所不同，但基本步骤与为 Azure SQL 数据库设置作业代理相同。

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>适用于 Azure SQL 数据库的作业代理

若要创建为 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)运行存储过程的作业，请使用 [Azure 弹性作业代理](../azure-sql/database/elastic-jobs-overview.md)。 在 Azure 门户中创建作业代理。 此方法将若干存储过程添加到代理使用的数据库（也称为代理数据库）。 然后，可以创建在目标数据库中运行存储过程并在完成后捕获输出的作业。

在创建作业之前，需要设置权限、组和目标，如 [Azure 弹性作业代理的完整文档](../azure-sql/database/elastic-jobs-overview.md)所述。 还需要在目标数据库中创建支持表，如以下部分所述。

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>创建用于注册参数和存储输入的状态表

SQL 代理作业不接受输入参数。 在目标数据库中，创建一个状态表，可以在其中注册参数并存储用于调用存储过程的输入。 所有代理作业步骤都针对目标数据库运行，但作业的存储过程针对代理数据库运行。 

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

[SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms) 中的生成表如下所示：

![屏幕截图显示存储存储过程输入的已创建的状态表。](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

为了确保良好的性能并确保代理作业可以找到关联的记录，该表使用作业执行 ID (`jobid`) 作为主键。 如果需要，还可以为输入参数添加单个列。 前面描述的架构可以更普遍地处理多个参数，但仅限于由 `NVARCHAR(MAX)` 计算的大小。

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>创建用于运行存储过程的顶级作业

若要执行长时间运行的存储过程，请在代理数据库中创建此顶级作业代理：

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

现在，向参数化、运行和完成存储过程的作业添加步骤。 默认情况下，作业步骤将在 12 小时后超时。 如果存储过程需要更多时间，或者你希望该过程更早超时，可以将 `step_timeout_seconds` 参数更改为以秒为单位指定的另一个值。 默认情况下，一个步骤具有 10 个内置重试，每次重试之间有回退超时，你可以加以利用。

以下是要添加的步骤：

1. 等待参数出现在 `LongRunningState` 表中。

   第一步等待参数添加到 `LongRunningState` 表中，这会在作业开始后不久发生。 如果未将作业执行 ID (`jobid`) 添加到 `LongRunningState` 表，则步骤将仅失败，并且默认重试或回退超时将等待：

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

1. 从状态表中查询参数，然后将它们传递给存储过程。 此步骤还会在后台运行该过程。 

   如果存储过程不需要参数，只需直接调用存储过程。 否则，若要传递 `@timespan` 参数，请使用 `@callparams`，也可将其扩展以传递其他参数。

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

若要启动作业，请将传递本机查询和 [ **“执行 SQL 查询**”操作](/connectors/sql/#execute-a-sql-query-(v2))结合使用，并立即将作业的参数推送到状态表中。 为了向目标表中的 `jobid` 属性提供输入，逻辑应用添加了一个 For each 循环，该循环从前面的操作中循环访问表输出。 对于每个作业执行 ID，运行一个“插入行”操作，该操作使用动态数据输出 `ResultSets JobExecutionId` 添加作业的参数以解包并传递给目标存储过程。

![屏幕截图显示用于启动作业和将参数传递到存储过程的操作。](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

当作业完成时，作业将更新 `LongRunningState` 表，以便你可以使用[“当修改项时”触发器](/connectors/sql/#when-an-item-is-modified-(v2))来轻松对结果进行触发。 如果不需要输出，或者已有监视输出表的触发器，则可以跳过此部分。

![屏幕截图显示“当修改项时”SQL 触发器。](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server 或 Azure SQL 托管实例的作业代理

对于同一场景，可以将 [SQL Server 代理](/sql/ssms/agent/sql-server-agent)用于[本地 SQL Server](/sql/sql-server/sql-server-technical-documentation) 和 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。 尽管某些管理详细信息会有所不同，但基本步骤与为 Azure SQL 数据库设置作业代理相同。

## <a name="next-steps"></a>后续步骤

[连接到 SQL Server、Azure SQL 数据库或 Azure SQL 托管实例](../connectors/connectors-create-api-sqlazure.md)

