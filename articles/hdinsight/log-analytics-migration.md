---
title: 为 Azure HDInsight 迁移 Log Analytics 数据
description: 了解对 Azure Monitor 集成的更改以及有关使用新表的最佳做法。
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 823fb43afbd7ac51cafeff7e9e18472fe32c9aec
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742266"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>适用于 Azure HDInsight 群集的 Log Analytics 迁移指南

 Azure HDInsight 是一种面向企业的托管群集服务。 此服务在 Azure 上运行开放源代码分析框架，如 Apache Spark、Hadoop、HBase 和 Kafka。 Azure HDInsight 已与其他 Azure 服务集成，使客户可以更好地管理其大数据分析应用程序。

Log Analytics 在 Azure 门户中提供了一个工具，用于编辑和运行日志查询。 查询来自 Azure Monitor 日志收集的数据，以交互方式分析其结果。 客户可以使用 Log Analytics 查询检索符合特定条件的记录。 他们还可以使用查询识别趋势、分析模式以及提供对数据的见解。

Azure HDInsight 在 2017 年实现了与 Log Analytics 的集成。 HDInsight 客户很快便采用此功能来监视其 HDInsight 群集并在群集中查询日志。 尽管此功能的采用已增加，不过客户提供了有关集成的反馈：

- 客户无法确定要存储的日志，而存储所有日志可能会成本高昂。
- 当前 HDInsight 架构日志未遵循一致的命名约定，某些表是重复的。
- 客户希望使用现成的仪表板轻松监视 HDInsight 群集的 KPI。
- 客户必须跳到 Log Analytics 才能运行简单查询。

## <a name="solution-overview"></a>解决方案概述

考虑到客户反馈，Azure HDInsight 团队投入开发了与 Azure Monitor 的集成。 此集成实现了：

- 客户 Log Analytics 工作区中的一组新表。 新表通过新 Log Analytics 管道提供。
- 更高的可靠性
- 更快的日志传送
- 基于资源的表分组和默认查询



> [!NOTE]  
> 新的 Azure Montitor 集成是公开预览版。 仅在美国东部和西欧区域可用。


## <a name="benefits-of-the-new-azure-monitor-integration"></a>新 Azure Monitor 集成的优点

本文档概述了对 Azure Monitor 集成的更改，并提供了有关使用新表的最佳做法。

重新设计的架构：新 Azure Monitor 集成的架构格式进行了更好的组织，并且易于理解。 减少了三分之二的架构，以尽可能消除旧架构中的多义性。

选择性日志记录（即将发布）：通过 Log Analytics 提供了日志和指标。 为了帮助节省监视成本，我们将发布新的选择性日志记录功能。 使用此功能可打开和关闭不同的日志和指标源。 借助此功能，你只需为使用的内容付费。

日志群集门户集成：“日志”窗格是 HDInsight 群集门户的新功能。 有权访问群集的任何人都可以访问此窗格，以查询群集资源向其发送记录的任何表。 用户无需再访问 Log Analytics 工作区即可查看特定群集资源的记录。

见解群集门户集成：“见解”窗格也是 HDInsight 群集门户的新功能。 启用新 Azure Monitor 集成后，可以选择“见解”窗格，系统会自动为你填充特定于群集类型的现成日志和指标仪表板。 这些仪表板已相对于以前的 Azure 解决方案进行了改进。 它们可让你深入了解群集的性能和运行状况。

大规模见解：可以使用 Azure Monitor 门户中新的“大规模见解”工作簿跨不同订阅监视群集运行状况和性能 。

## <a name="customer-scenarios"></a>客户场景

以下部分介绍客户如何在不同场景中使用新 Azure Monitor 集成。 [激活新 Azure Monitor 集成](#activate-a-new-azure-monitor-integration)部分概述了如何激活和使用新 Azure Monitor 集成。 [从 Azure Monitor 经典迁移到新 Azure Monitor 集成](#migrate-to-the-new-azure-monitor-integration)部分包含了面向依赖于旧 Azure Monitor 集成的用户的其他信息。

> [!NOTE]
> 只有在 2020 年 9 月下旬及之后创建的集群才有资格使用新 Azure Monitor 集成。

## <a name="activate-a-new-azure-monitor-integration"></a>激活新 Azure Monitor 集成 

> [!NOTE]
> 启用新集成之前，你必须在有权访问的订阅中创建 Log Analytics 工作区。 有关如何创建 Log Analytics 工作区的详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。

转到群集的门户页面并向下滚动左侧菜单，直到达到“监视”部分，以激活新集成。 在“监视”部分中，选择“监视集成” 。 然后，选择“启用”，可以选择要将日志发送到的 Log Analytics 工作区。 选择了工作区后，选择“保存”。 

### <a name="access-the-new-tables"></a>查询新表

可以通过两种方式访问新表。 

### <a name="approach-1"></a>方法 1：
访问新表的第一种方式是通过 Log Analytics 工作区。 

1. 转到在启用集成时选择的 Log Analytics 工作区。 
2. 在屏幕左侧的菜单中向下滚动，然后选择“日志”。 日志查询编辑器会弹出，其中包含工作区中所有表的列表。 
3. 如果表按“解决方案”进行分组，则新 HDI 表位于“日志管理”部分下 。 
4. 如果按“资源类型”对表进行分组，则表位于“HDInsight 群集”部分下，如下图所示 。 

> [!NOTE]
> 此过程描述如何在旧集成中访问日志。 这要求用户有权访问工作区。

### <a name="approach-2"></a>方法 2：

访问新表的第二种方式是通过群集门户访问。
 
1. 导航到群集的门户页面，向下滚动左侧菜单，直到看到“监视”部分。 在此部分中，你会看到“日志”窗格。 
2. 选择“日志”，日志查询编辑器随即出现。 该编辑器包含与群集资源关联的所有日志。 启用集成后，会将日志发送到 Log Analytics 工作区。 这些日志提供基于资源的访问 (RBAC)。 借助 RBAC，有权访问群集但不能访问工作区的用户可以查看与群集关联的日志。

为进行比较，以下屏幕截图显示了旧集成工作区视图和新集成工作区视图：

**旧集成工作区视图**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="显示旧集成工作区视图的屏幕截图。" border="false":::

**新集成工作区视图**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="显示新集成工作区视图的屏幕截图。" border="false":::

### <a name="use-the-new-tables"></a>使用新表

这些集成可帮助你使用新表：

#### <a name="default-queries-to-use-with-new-tables"></a>用于新表的默认查询

在日志查询编辑器中，在表列表上方将开关设置为“查询”。 请确保按“资源类型”对查询进行分组，并且没有为“HDInsight 群集”以外的资源类型设置筛选器 。 下图显示了按“资源类型”进行分组并针对“HDInsight 群集”进行筛选时结果的显示方式 。 只选择一个查询，它会出现在日志查询编辑器中。 请务必阅读查询中包含的注释，因为某些查询要求输入一些信息（如群集名称）才能成功运行查询。

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="显示按资源类型进行分组的默认查询结果的屏幕截图。" border="true":::


#### <a name="create-your-own-queries"></a>创建自己的查询

可以在日志查询编辑器中输入自己的查询。 对旧表使用的查询会对新表无效，因为许多新表具有新的优化架构。 默认查询是有关对新表塑造查询的良好参考。

#### <a name="insights"></a>洞察力

见解是使用 [Azure 工作簿](../azure-monitor/visualize/workbooks-overview.md)创建的特定于群集的可视化效果仪表板。 这些仪表板可提供有关群集运行情况的详细图和可视化效果。 仪表板包含用于每种群集类型、YARN、系统指标和组件日志的部分。 可以在门户中访问群集页面，向下滚动到“监视”部分，然后选择“见解”窗格，从而访问群集的仪表板 。 如果已启用新集成，则仪表板会自动加载。 在查询日志时，留出几秒钟让图加载。

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="显示可视化效果仪表板的屏幕截图。":::

#### <a name="custom-azure-workbooks"></a>自定义 Azure 工作簿

可以创建自己的 Azure 工作簿，其中包含自定义图和可视化效果。 在群集的门户页面中，向下滚动到“监视”部分，然后在左侧菜单中选择“工作簿”窗格 。 可以开始使用空白模板，也可以使用“HDInsight 群集”部分下的模板之一。 每种群集类型都有一个模板。 如果要保存默认 HDInsight 见解未提供的特定自定义项，则模板非常有用。 如果觉得 HDInsight 见解缺少某些内容，请随时在其中发送针对新功能的请求。

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>新 Azure Monitor 集成的大规模工作簿

使用我们新的大规模工作簿可获取针对群集的多群集监视体验。 我们的大规模工作簿会显示哪些群集启用了监视管道。 工作簿还提供一种简单方式来同时检查多个群集的运行状况。 若要查看此工作簿，请执行以下操作：

1. 从 Azure 门户主页转到“Azure Monitor”页面
2. 处于“Azure Monitor”页面上后，选择“见解”部分下面的“见解中心”  。
3. 选择“分析”部分下的“HDInsight 群集” 。

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="显示大规模工作簿的屏幕截图。" border="false":::

#### <a name="alerts"></a>警报

可以在日志查询编辑器中向群集和工作区添加自定义警报。 通过从群集或工作区门户选择“日志”窗格，转到日志查询编辑器。 运行查询，然后选择“新建警报规则”，如以下屏幕截图所示。 有关详细信息，请参阅[配置警报](../azure-monitor/alerts/alerts-log.md)。

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="显示新建警报规则的屏幕截图。" border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>迁移到新 Azure Monitor 集成

如果在使用经典 Azure Monitor 集成，则在切换到新 Azure Monitor 集成后，需要对新的表格式进行一些调整。

若要启用新 Azure Monitor 集成，请按照[激活新 Azure Monitor 集成](#activate-a-new-azure-monitor-integration)部分中概述的步骤进行操作。

### <a name="run-queries-in-log-analytics"></a>在 Log Analytics 中运行查询

由于新的表格式与迁移的格式不同，因此查询需要进行改编，以便可以使用我们的新表。 启用新 Azure Monitor 集成后，可以浏览表和架构，以确定旧查询中使用的字段。

我们在旧表与新表之间提供了一个[映射表](#appendix-table-mapping)，以帮助快速查找需要用于迁移仪表板和查询的新字段。

默认查询：我们创建了演示如何在常见情况下使用新表的默认查询。 默认查询还显示每个表中提供的信息。 可以按照本文[用于新表的默认查询](#default-queries-to-use-with-new-tables)部分中的说明访问默认查询。

### <a name="update-dashboards-for-hdinsight-clusters"></a>为 HDInsight 群集更新仪表板

如果生成了多个仪表板来监视 HDInsight 群集，则在启用新 Azure Monitor 集成后，需要调整表幕后的查询。 表名或字段名称可能会在新集成中发生更改，但包含在旧集成中的所有信息都会包含在内。

请参阅旧表/架构与新表/架构之间的[映射表](#appendix-table-mapping)，来更新仪表板幕后的查询。

#### <a name="out-of-box-dashboards"></a>现成仪表板 

我们还改进了群集级别的现成仪表板。 每个图右上方都有一个按钮，可用于查看生成信息的基础查询。 图是一种很好的方法，可让你熟悉如何有效地查询新表。 可以按照在[见解](#insights)和[新 Azure Monitor 集成的大规模工作簿](#at-scale-workbooks-for-new-azure-monitor-integrations)部分中提供的说明来访问现成仪表板。

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>使用 HDInsight 大规模监视仪表板

如果你将现成监视仪表板用于 HDInsight 群集（如 HDInsight Spark 监视和 HDInsight 交互式监视），我们会致力于在 Azure Monitor 门户上为你提供相同的功能。

你会看到 Azure Monitor 中有一个 HDInsight 群集选项。

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="显示 Azure Monitor 中的 HDInsight 选项的屏幕截图。" border="false":::

Azure Monitor 门户的见解中心使你能够在一个位置监视多个 HDInsight 群集。 我们根据工作负载类型来组织群集，因此你会看到 Spark、HBase 和 Hive 等类型。 现在可以在此视图中监视所有 HDInsight 群集，而不是转到多个仪表板。

> [!NOTE]
> 有关详细信息，请参阅本文中的[见解](#insights)和[新 Azure Monitor 集成的大规模工作簿](#at-scale-workbooks-for-new-azure-monitor-integrations)部分。

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>同时启用两种集成以加速迁移

可以在有资格实现两种集成的群集上同时激活经典和新 Azure Monitor 集成，以便快速迁移到新 Azure Monitor 集成。 新集成适用于 2020 年 9 月中旬之后创建的所有群集。

通过这种方式，可以轻松地对所使用的查询进行并排比较。

### <a name="enabling-the-classic-integration"></a>启用经典集成

如果在使用 2020 年 9 月中旬之后创建的群集，则会在群集的门户中看到新门户体验。 若要启用新管道，可以按照[激活新 Azure Monitor 集成](#activate-a-new-azure-monitor-integration)部分中概述的步骤进行操作。 若要对此群集激活经典集成，请转到群集的门户页面。 在群集门户页面左侧菜单的“监视”部分中，选择“监视集成”窗格 。 选择“为 HDInsight 群集集成配置 Azure Monitor (经典)”。 一个侧边上下文随即出现，其中包含可用于启用和禁用经典 Azure 监视集成的开关。 

> [!NOTE]
> 通过群集门户的日志和见解页面，不会看到来自经典集成的任何日志或指标。 只有新集成日志和指标才会出现在这些位置。

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="显示用于访问经典集成的链接的屏幕截图。" border="false":::

2021 年 7 月 1 日之后，无法使用经典 Azure Monitor 集成创建新群集。

## <a name="release-and-support-timeline"></a>发布和支持时间线

- 经典 Azure Monitor 集成会在 2021 年 10 月 15 日后不可用。 无法在该日期后启用经典 Azure Monitor 集成。
- 现有经典 Azure Monitor 集成将继续正常工作。 对于经典 Azure Monitor 集成，将提供有限的支持。 
  - 客户提交支持票证后，将调查问题。
  - 如果解决方案需要更改映像，客户应迁移到新集成。
  - 除了严重安全性问题之外，我们不会修补经典 Azure Monitor 集成群集。

## <a name="appendix-table-mapping"></a>附录：表映射

下图显示了从经典 Azure Monitor 集成到新集成的表映射。 “工作负载”列描述每个表关联的工作负载。 “新表”行显示新表的名称。 “说明”行描述将在此表中提供的日志/指标的类型。 “旧表”行是经典 Azure Monitor 集成中的所有表的列表，其数据现在会出现在“新表”行中列出的表中 。

> [!NOTE]
> 某些表是新表，但不基于旧表。

## <a name="general-workload-tables"></a>常规工作负载表

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>说明：此表包含从 Ambari 收集的系统指标。 指标现在来自群集中的每个节点（边缘节点除外），而不只是两个头节点。 每个指标现在都是一列，并且每个指标对每个记录报告一次。</li><li>旧表：metrics\_cpu\_nice\_cl、metrics\_cpu\_system\_cl、metrics\_cpu\_user\_cl、metrics\_memory\_cache\_CL、metrics\_memory\_swap\_CL、metrics\_memory\_total\_CLmetrics\_memory\_buffer\_CL、metrics\_load\_1min\_CL、metrics\_load\_cpu\_CL、metrics\_load\_nodes\_CL、metrics\_load\_procs\_CL、metrics\_network\_in\_CL、metrics\_network\_out\_CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>说明：此表包含来自群集中每个节点（边缘节点除外）的 Ambari 群集警报。 每个警报都是此表中的一个记录。</li><li>旧表：metrics\_cluster\_alerts\_CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>说明：此表包含来自 Ambari 审核和身份验证日志的记录。</li><li>旧表：log\_ambari\_audit\_CL、log\_auth\_CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>说明：此表包含来自 ESP 群集的 Ranger 审核日志的所有记录。</li><li>旧表：ranger\_audit\_logs\_CL</li></ul>|
| HDInsightGatewayAuditLogs\_CL | <ul><li>说明：此表包含网关节点审核信息。 它是与“旧表”列中的表相同的格式。 它仍位于“自定义日志”部分中。</li><li>旧表：log\_gateway\_Audit\_CL</li></ul>|

## <a name="spark-workload"></a>Spark 工作负载

> [!NOTE]
> Spark 应用程序相关表已替换为 11 个新 Spark 表（从 HDInsightSpark* 开始），将提供有关 Spark 工作负载的更多深入信息。


| 新建表 | 详细信息 |
| --- | --- |
| HDInsightSparkLogs | <ul><li>说明：此表包含与 Spark 及其相关组件（Livy 和 Jupyter）相关的所有日志。</li><li>旧表：log\_livy,\_CL、log\_jupyter\_CL、log\_spark\_CL、log\_sparkappsexecutors\_CL、log\_sparkappsdrivers\_CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>说明：此表包含 Spark 应用程序的事件信息，包括提交和完成时间、应用 ID 和 AppName。 它可用于跟踪应用程序的启动和完成时间。 </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>说明：此表包含与 Spark 的块管理器相关的事件信息。 它包括执行程序内存使用情况等信息。</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>说明：此表包含与应用程序执行环境相关的事件信息，包括 Spark 部署模式、主设备以及有关执行程序的信息。</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>说明：此表包含有关应用程序的 Spark 执行程序使用情况的事件信息。</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>说明：此表包含不符合任何其他 Spark 表的事件信息。 </li></ul>|
| HDInsightSparkJobEvents | <ul><li>说明：此表包含有关 Spark 作业的信息，其中包括其开始和结束时间、结果和关联阶段。</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>说明：此表包含有关 Spark SQL 查询的事件信息，包括其计划信息和说明以及开始和结束时间。</li></ul>|
| HDInsightSparkStageEvents | <ul><li>说明：此表包含 Spark 阶段的事件信息，包括其开始时间和完成时间、失败状态和详细执行信息。</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>说明：此表包含阶段和任务的性能指标。</li></ul>|
| HDInsightTaskEvents | <ul><li>说明：此表包含 Spark 任务的事件信息，包括开始和完成时间、关联阶段、执行状态和任务类型。</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>说明：此表包含 Jupyter Notebook 的事件信息。</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop/YARN 工作负载

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>说明：此表包含 Hadoop 和 YARN 框架中的 JMX 指标。 它包含与旧自定义日志表相同的所有 JMX 指标，以及我们认为重要的更多指标。 我们添加了时间线服务器、节点管理器和作业历史记录服务器指标。 它对每个记录包含一个指标。</li><li>旧表：metrics\_resourcemanager\_clustermetrics\_CL、metrics\_resourcemanager\_jvm\_CL、metrics\_resourcemanager\_queue\_root\_CL、metrics\_resourcemanager\_queue\_root\_joblauncher\_CL、metrics\_resourcemanager\_queue\_root\_default\_CL、metrics\_resourcemanager\_queue\_root\_thriftsvr\_CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>说明：此表包含从 Hadoop 和 YARN 框架生成的所有日志。</li><li>旧表：log\_mrjobsummary\_CL、log\_resourcemanager\_CL、log\_timelineserver\_CL、log\_nodemanager\_CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive/LLAP 工作负载 

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>说明：此表包含 Hive 和 LLAP 框架中的 JMX 指标。 它包含与旧自定义日志表相同的所有 JMX 指标。 它对每个记录包含一个指标。</li><li>旧表：llap\_metrics\_hiveserver2\_CL、llap\_metrics\_hs2\_metrics\_subsystemllap\_metrics\_jvm\_CL、llap\_metrics\_llap\_daemon\_info\_CL、llap\_metrics\_buddy\_allocator\_info\_CL、llap\_metrics\_deamon\_jvm\_CL、llap\_metrics\_io\_CL、llap\_metrics\_executor\_metrics\_CL、llap\_metrics\_metricssystem\_stats\_CL、llap\_metrics\_cache\_CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>说明：此表包含从 Hive、LLAP 及其相关组件（WebHCat 和 Zeppelin）生成的日志。</li><li>旧表：log\_hivemetastore\_CL log\_hiveserver2\_CL、log\_hiveserve2interactive\_CL、log\_webhcat\_CL、log\_zeppelin\_zeppelin\_CL</li></ul>|


## <a name="kafka-workload"></a>Kafka 工作负载

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>说明：此表包含 Kafka 中的 JMX 指标。 它包含与旧自定义日志表相同的所有 JMX 指标，以及我们认为重要的更多指标。 它对每个记录包含一个指标。</li><li>旧表：metrics\_kafka\_CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>说明：此表包含从 Kafka 代理生成的所有日志。</li><li>旧表：log\_kafkaserver\_CL、log\_kafkacontroller\_CL</li></ul>|

## <a name="hbase-workload"></a>HBase 工作负载

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>说明：此表包含 HBase 中的 JMX 指标。 它包含“旧架构”列中所列表中的所有相同 JMX 指标。 与旧表不同，每行都包含一个指标。</li><li>旧表：metrics\_regionserver\_CL、metrics\_regionserver\_wal\_CL、metrics\_regionserver\_ipc\_CL、metrics\_regionserver\_os\_CL、metrics\_regionserver\_replication\_CL、metrics\_restserver\_CL、metrics\_restserver\_jvm\_CL、metrics\_hmaster\_assignmentmanager\_CL、metrics\_hmaster\_ipc\_CL、metrics\_hmaser\_os\_CL、metrics\_hmaster\_balancer\_CL、metrics\_hmaster\_jvm\_CL、metrics\_hmaster\_CL、metrics\_hmaster\_fs\_CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>说明：此表包含 HBase 及其相关组件（Phoenix 和 HDFS）中的日志。</li><li>旧表：log\_regionserver\_CL、log\_restserver\_CL、log\_phoenixserver\_CL、log\_hmaster\_CL、log\_hdfsnamenode\_CL、log\_garbage\_collector\_CL</li></ul>|

## <a name="storm-workload"></a>Storm 工作负载

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightStormMetrics | <ul><li>说明：此表包含与“旧表”部分中的表相同的 JMX 指标。 其行对每个记录包含一个指标。</li><li>旧表：metrics\_stormnimbus\_CL、metrics\_stormsupervisor\_CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>说明：此表包含 Storm 中的拓扑级别指标。 其外观与“旧表”部分中列出的表相同。</li><li>旧表：metrics\_stormrest\_CL</li></ul>|
| HDInsightStormLogs | <ul><li>说明：此表包含从 Storm 生成的所有日志。</li><li>旧表：log\_supervisor\_CL、log\_nimbus\_CL</li></ul>|

## <a name="oozie-workload"></a>Oozie 工作负载

| 新建表 | 详细信息 |
| --- | --- |
| HDInsightOozieLogs | <ul><li>说明：此表包含从 Oozie 框架生成的所有日志。</li><li>旧表：Log\_oozie\_CL</li></ul>|

## <a name="next-steps"></a>后续步骤
[ Azure Monitor 日志以监视 HDInsight 群集](hdinsight-hadoop-oms-log-analytics-use-queries.md)
