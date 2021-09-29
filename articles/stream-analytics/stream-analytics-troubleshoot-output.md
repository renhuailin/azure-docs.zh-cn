---
title: Azure 流分析输出的故障排除
description: 本文介绍了对 Azure 流分析作业中的输出连接进行故障排除的技巧。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 393d710ba411a6573a4cce155abd7c1aeae06aa8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754872"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure 流分析输出的故障排除

本文介绍了 Azure 流分析输出连接的常见问题，以及如何排查输出问题。 许多故障排除步骤都需要为流分析作业启用资源日志和其他诊断日志。 如果没有启用资源日志，请参阅[使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)。

## <a name="the-job-doesnt-produce-output"></a>作业不生成输出

1. 使用每项输出对应的“测试连接”按钮来验证与输出的连接。
1. 查看“监视器”选项卡上的[监视指标](stream-analytics-monitoring.md)。由于值将进行聚合，因此指标会延迟几分钟。

   * 如果“输入事件”的值大于零，则作业可以读取输入数据。 如果“输入事件”的值小于或等于零，表示作业的输入有问题。 有关详细信息，请参阅[对输入连接进行故障排除](stream-analytics-troubleshoot-input.md)。 如果作业具有参考数据输入，请在查看输入事件指标时按逻辑名称应用拆分。 如果仅参考数据中没有输入事件，则可能意味着此输入源未正确配置，无法获取正确的参考数据集。
   * 如果“数据转换错误”的值大于零并不断增大，请参阅 [Azure 流分析数据错误](data-errors.md)，详细了解数据转换错误。
   * 如果“运行时错误”的值大于零，表示作业可以接收数据，但在处理查询时生成错误。 若要查找错误，请转到[审核日志](../azure-monitor/essentials/activity-log.md)，然后筛选“失败”状态。
   * 如果“输入事件”的值大于零，而“输出事件”的值等于零，则下列陈述之一是正确的：
      * 查询处理导致了输出事件为零。
      * 事件或字段的格式可能不正确，导致在执行查询处理后的输出为零。
      * 由于连接或身份验证原因，作业无法将数据推送到输出接收器。

   操作日志消息解释了其他详细信息（包括具体发生了什么），但查询逻辑筛选掉所有事件的情况除外。 如果对多个事件的处理生成错误，则错误每 10 分钟聚合一次。

## <a name="the-first-output-is-delayed"></a>首个输出延迟

当流分析作业启动时，就会读取输入事件。 不过，在某些情况下，输出可能会有延迟。

在时态查询元素中使用较大的时间值可能会导致输出延迟。 为了在长时间段内生成正确的输出，流式处理作业会尽量读取最新时间的数据来填充时间段。 最长可以追溯到 7 天前的数据。 在读取未完成的输入事件之前，不会生成任何输出。 当系统升级流式处理作业时，可能会出现此问题。 当升级发生时，作业重启。 此类升级通常每隔几个月发生一次。

在设计流分析查询时，请保持审慎。 如果对作业查询语法中的时态元素使用长时间段，可能会导致首个输出在作业启动或重启时延迟。 超过几个小时（最长 7 天）就被视为长时间段。

缓解这种首个输出延迟的问题的一种方法是，使用查询并行化技术（如对数据进行分区）。 或者，可以添加更多的流单元来提高吞吐量，直到完成作业。  有关详细信息，请参阅[创建流分析作业时的注意事项](stream-analytics-concepts-checkpoint-replay.md)。

下面这些因素会影响首个输出的及时性：

* 使用窗口化聚合（如翻转窗口、跳跃窗口和滑动窗口的 GROUP BY 子句）：

  * 对于翻转窗口或跳跃窗口聚合，结果在窗口时间范围结束时生成。
  * 对于滑动窗口，结果在事件进入或退出滑动窗口时生成。
  * 如果计划使用大的窗口大小（如超过一小时），最好选择跳跃窗口或滑动窗口。 使用这些类型的窗口，可以更频繁地看到输出。

* 使用时态联接（如包含 DATEDIFF 的 JOIN）：
  * 只要到达匹配的事件的两侧，就会生成匹配。
  * 缺少匹配的数据（如 LEFT OUTER JOIN）在左侧每个事件的 DATEDIFF 窗口结束时生成。

* 使用时态分析函数（如包含 LIMIT DURATION 的 ISFIRST、LAST 和 LAG）：
  * 对于分析函数，输出是针对每个事件生成的。 不存在延迟。

## <a name="the-output-falls-behind"></a>输出落后于进度

在作业的正常操作期间，输出的延迟时间可能会越来越长。 如果输出像这样落后于进度，可以通过检查以下因素来确定根本原因：

* 下游接收器是否受限制
* 上游源是否受限制
* 查询中的处理逻辑是否是计算密集型的

若要查看输出详细信息，请在 Azure 门户中依次选择流式处理作业和“作业关系图”。 对于每个输入，每个分区都有一个积压工作 (backlog) 事件指标。 如果指标持续增长，则表明系统资源受到约束。 指标增长可能是由于输出接收器限制或 CPU 利用率高引起的。 有关详细信息，请参阅[使用作业关系图进行数据驱动调试](stream-analytics-job-diagram-with-metrics.md)。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL 数据库输出键冲突警告

如果你将 Azure SQL 数据库配置为流分析作业的输出，它会将记录批量插入到目标表中。 一般来说，Azure 流分析保证[至少一次传递](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)到输出接收器。 当 SQL 表定义了唯一约束时，你仍可以实现[确切地一次传递]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)到 SQL 输出。

如果你在 SQL 表上设置唯一键约束，Azure 流分析会删除重复记录。 它将数据拆分为几个批，并以递归方式插入这些批，直到找到一个重复的记录。 拆分和插入过程一次忽略一个重复项。 对于包含多个重复行的流式处理作业，此过程效率低下且非常耗时。 如果你在前一个小时的活动日志中看到多个键冲突警告消息，则很可能是 SQL 输出拖慢了整个作业的速度。

若要解决此问题，请通过启用 IGNORE_DUP_KEY 选项来[配置导致键冲突的索引](/sql/t-sql/statements/create-index-transact-sql)。 使用此选项，SQL 可以在大容量插入期间忽略重复值。 Azure SQL 数据库只是生成警告消息，而不是错误。 因此，Azure 流分析不再生成主键冲突错误。

为多种类型的索引配置 IGNORE_DUP_KEY 时，应注意以下几点：

* 无法对主键或使用 ALTER INDEX 的唯一约束设置 IGNORE_DUP_KEY。 必须删除并重新创建索引。  
* 可以通过对唯一索引使用 ALTER INDEX 来设置 IGNORE_DUP_KEY。 此实例与 PRIMARY KEY/UNIQUE 约束不同，它是使用 CREATE INDEX 或 INDEX 定义创建的。  
* IGNORE_DUP_KEY 选项不应用于列存储索引，因为无法对此类索引强制实现唯一性。

## <a name="sql-output-retry-logic"></a>SQL 输出重试逻辑

当带有 SQL 输出的流分析作业收到第一批事件时，将执行以下步骤：

1. 作业尝试连接到 SQL。
2. 作业提取目标表的架构。
3. 作业针对目标表架构验证列名称和类型。
4. 作业根据批处理中的输出记录准备内存中数据表。
5. 作业使用 BulkCopy [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver) 将数据表写入 SQL。

在这些步骤中，SQL 输出可能会遇到以下类型的错误：

* 使用指数退避重试策略重试的暂时性[错误](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others)。 最小重试间隔取决于各个错误代码，但是间隔通常小于 60 秒。 上限最多可为五分钟。 

   [登录失败](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531)和[防火墙问题](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues)会在上次尝试后至少 5 分钟重试一次，直到成功为止。

* 数据错误（如强制转换错误和架构约束冲突）将通过输出错误策略进行处理。 通过重试二进制拆分批处理来处理这些错误，直到导致错误的单个记录通过跳过或重试得到处理。 主唯一键约束冲突[始终需得到处理](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)。

* 存在 SQL 服务问题或内部代码缺陷时，可能会出现非暂时性错误。 例如，对于弹性池达到其存储限制之类的错误（代码 1132），重试无法解决。 在这些情况下，流分析作业将遭遇[降级](job-states.md)。
* 在步骤 5 的 `BulkCopy` 期间，可能出现 `BulkCopy` 超时。 `BulkCopy` 偶尔会出现操作超时。 默认配置的最小超时为五分钟，当连续达到此值时，它将增加一倍。
一旦超时超过 15 分钟，`BulkCopy` 的最大批处理大小提示将减少到一半，直到每批剩下 100 个事件为止。

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Azure 流分析 (1.0) 中的列名称是小写的

如果使用的是原始兼容性级别 (1.0)，Azure 流分析会将列名称更改为小写。 此行为已在以后的兼容性级别中修复。 若要保留大小写，请迁移到兼容性级别 1.1 或更高版本。 有关详细信息，请参阅[流分析作业的兼容性级别](./stream-analytics-compatibility-level.md)。

## <a name="get-help"></a>获取帮助

若要获得进一步的帮助，可前往 [Azure 流分析的 Microsoft 问答问题页面](/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](/rest/api/streamanalytics/)