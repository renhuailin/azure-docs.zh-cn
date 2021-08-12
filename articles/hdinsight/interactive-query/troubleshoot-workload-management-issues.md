---
title: 排查 Hive LLAP 工作负载管理问题
titleSuffix: Azure HDInsight
description: 排查 Hive LLAP 工作负载管理问题
ms.service: hdinsight
ms.topic: troubleshooting
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 6a06a67b5039bc3a7e25e8300128c85ee008be3a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482184"
---
# <a name="troubleshoot-hive-llap-workload-management-issues"></a>排查 Hive LLAP 工作负载管理问题

启动 HDInsight 4.0 群集的客户可以使用工作负载管理 (WLM)。 可使用以下资源来帮助调试 WLM 功能相关的问题。

## <a name="get-wlm-resource-plan-and-plan-entities"></a>获取 WLM 资源计划和计划实体
#### <a name="to-get-all-resource-plans-on-the-cluster"></a>若要获取群集上的所有资源计划，请运行以下代码：
```hql
SHOW RESOURCE PLANS;
```

#### <a name="to-get-definition-of-a-given-resource-plan"></a>若要获取给定资源计划的定义，请运行以下代码：
```hql
SHOW RESOURCE PLAN <plan_name>;
```

## <a name="get-wlm-entities-information-from-metastore-database"></a>从元存储数据库获取 WLM 实体信息
> [!NOTE]
> 仅适用于自定义 Hive 元存储数据库

还可以通过 Hive 元存储数据库中的以下表查看 WLM 实体信息 

* **WM_RESOURCEPLANS** (NAME string, STATUS string, QUERY_PARALLELISM int, DEFAULT_POOL_PATH string)
* **WM_POOLS** (RP_NAME string, PATH string, ALLOC_FRACTION double, QUERY_PARALLELISM int, SCHEDULING_POLICY string)
* **WM_MAPPINGS** (RP_NAME string, ENTITY_TYPE string, ENTITY_NAME string, POOL_PATH string, ORDERING int)
* **WM_TRIGGERS** (RP_NAME string, NAME string, TRIGGER_EXPRESSION string, ACTION_EXPRESSION string)
* **WM_POOLS_TO_TRIGGERS** (RP_NAME string, POOL_PATH string, TRIGGER_NAME string)

## <a name="wlm-metrics"></a>WLM 指标

可以直接通过“指标转储”选项卡下的 HS2Interactive UI 访问 WLM 指标。 <br>
:::image type="content" source="./media/hive-workload-management/hs2-interactive-wlm.jpg" alt-text="HS2 交互式 UI。" lightbox="./media/hive-workload-management/hs2-interactive-wlm.jpg":::

WLM 为资源计划中给定池发布的示例指标。
```
    "name" : "Hadoop:service=hiveserver2,name=WmPoolMetrics.etl",
    "modelerType" : "WmPoolMetrics.etl",
    "tag.Context" : "HS2",
    "tag.SessionId" : "etl",
    "tag.Hostname" : "hn0-c2b-ll.cu1cgjaim53urggr4psrgczloa.cx.internal.cloudapp.net",
    "NumExecutors" : 10,
    "NumRunningQueries" : 2,
    "NumParallelQueries" : 3,
    "NumQueuedQueries" : 0,
    "NumExecutorsMax" : 10
```

HS2Interactive UI 可能不适用于在 2021 年 4 月之前发布的、已启用 ESP（企业安全性套餐）的群集。 在这种情况下，可从自定义的 Grafana 仪表板获取 WLM 相关指标。
<br>
指标名称遵循以下模式：
```
default.General.WM_<pool>_numExecutors
default.General.WM_<pool>_numExecutorsMax
default.General.WM_<pool>_numRunningQueries
default.General.WM_<pool>_numParallelQueries
default.General.WM_<pool>_numQueuedQueries
```
将 `<pool>` 替换为相应的池名称即可获取 Grafana 中的指标。

:::image type="content" source="./media/hive-workload-management/grafana-wlm.jpg" alt-text="Grafana WLM 指标。" lightbox="./media/hive-workload-management/grafana-wlm.jpg":::

> 注意：确保在上述筛选器和组件名称中选择 hiveserver2 组件。

<br>

## <a name="wlm-feature-characteristics"></a>WLM 功能特征
### <a name="lifecycle-of-tez-ams-in-wlm-enabled-clusters"></a>**已启用 WLM 的群集中 Tez AM 的生命周期**
与默认的 LLAP 群集相比，已启用 WLM 的群集具有另一组 Tez AM。 如果在 Hive 配置中设置了 hive.server2.tez.interactive.queue=wm，则这些 Tez AM 将计划为在 `wm` 队列中运行。 <br>
基于资源计划中定义的所有池的 QUERY_PARALLELISM 之和激活 WLM 后，将会生成这些 Tez AM。 <br>
在群集中禁用工作负载管理时，会自动终止这些 Tez AM。
`{ DISABLE WORKLOAD MANAGEMENT; }`

### <a name="resource-contention"></a>**资源争用**
在已启用 WLM 的 LLAP 群集中，将会基于资源计划配置在查询之间共享资源。 资源共享有时会导致查询速度缓慢。
可对资源计划进行一些优化，以减少池中发生的资源争用。 例如，可将 `scheduling_policy` 定义为 `fair`，以保证分配到池的每个查询在群集上获得均等份额的资源；或者将它定义为 `fifo`，以保证将所有资源分配给第一个进入池中的查询。<br>
以下示例演示如何为资源计划 `wlm_basic` 中名为 `etl` 的池设置计划策略：
```hql
ALTER POOL wlm_basic.etl SET SCHEDULING_POLICY = fair;
```
也可以在创建池时设置计划策略：
```hql
CREATE POOL wlm_basic.default WITH ALLOC_FRACTION = 0.5, QUERY_PARALLELISM = 2, SCHEDULING_POLICY = fifo;
```

### <a name="query-failures-for-some-specific-use-cases"></a>**在某些特定用例中发生的查询失败**
对于以下情况，WLM 中运行的查询可以自动终止：
1. 将“移动触发器”应用到没有任何可用 Tez AM 的查询和目标池后，查询将改为终止。 <br>
上述问题是 WLM 功能的一种设计限制。 要解决此问题，可以增大目标池的 `QUERY_PARALLELISM` 属性，这样，即使达到了最大负载，提交到群集的查询也能获得此池的支持。 此外，请优化 `wm` 队列大小以适应此更改。 <br>
2. 禁用 WLM 后，所有进行中的查询都会失败并出现以下异常模式：
   ```
   FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.tez.TezTask. Dag received [DAG_TERMINATE, DAG_KILL] in RUNNING state.
   ```
3. 手动终止 WLM Tez AM 后，某些查询可能会失败并出现以下模式。 <br/>重新提交后，这些查询应该可以正常运行。
```
java.util.concurrent.CancellationException: Task was cancelled.
    at com.google.common.util.concurrent.AbstractFuture.cancellationExceptionWithCause(AbstractFuture.java:1349) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.getDoneValue(AbstractFuture.java:550) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.get(AbstractFuture.java:513) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.get(AbstractFuture.java:90) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Uninterruptibles.getUninterruptibly(Uninterruptibles.java:237) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures.getDone(Futures.java:1064) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures$CallbackListener.run(Futures.java:1013) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.DirectExecutor.execute(DirectExecutor.java:30) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.executeListener(AbstractFuture.java:1137) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.complete(AbstractFuture.java:957) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.cancel(AbstractFuture.java:611) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.cancel(AbstractFuture.java:118) ~[guava-28.0-jre.jar:?]
    at org.apache.hadoop.hive.ql.exec.tez.WmTezSession$TimeoutRunnable.run(WmTezSession.java:264) ~[hive-exec-3.1.3.4.1.3.6.jar:3.1.3.4.1.3.6]
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) [?:1.8.0_275]
    at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:180) ~[?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293) ~[?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_275]
    at java.lang.Thread.run(Thread.java:748) [?:1.8.0_275]
```

## <a name="known-issues"></a>已知问题
* 如果为目标 LLAP 群集启用了 WLM 功能，则通过 [Hive Warehouse Connector (HWC)](apache-hive-warehouse-connector.md) 提交的 Spark 作业可能会间歇性失败。 <br>
  若要避免上述问题，客户可以使用两个 LLAP 群集，并在其中一个群集中启用 WLM，在另一个群集中不启用 WLM。
  然后，客户可以使用 HWC 将其 Spark 群集连接到未启用 WLM 的 LLAP 群集。

* `DISABLE WORKLOAD MANAGEMENT;` 命令有时会长时间挂起。 <br>
取消该命令，并使用以下命令检查资源计划的状态：`SHOW RESOURCE PLANS;`
在再次运行 `DISABLE WORKLOAD MANAGEMENT` 命令之前，检查是否提供了有效的资源计划； <br>

* 某些 Tez AM 可能会一直保持运行，使用 `DISABLE WORKLOAD MANAGEMENT` 命令或重启 HS2 无法将其终止。 <br>
可在禁用工作负载管理后，通过 `yarn UI` 或 `yarn console application` 终止这些 Tez AM。

## <a name="related-articles"></a>相关文章
* [Hive LLAP 工作负载管理](hive-workload-management.md)
* [Hive LLAP 工作负载管理命令摘要](workload-management-commands.md)

