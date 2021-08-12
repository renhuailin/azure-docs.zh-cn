---
title: Hive LLAP 工作负载管理功能
titleSuffix: Azure HDInsight
description: Hive LLAP 工作负载管理功能
ms.service: hdinsight
ms.topic: how-to
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 8dde65d0547552af90caddd214dd1ceda5a754ee
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481976"
---
# <a name="hive-llap-workload-management-wlm-feature"></a>Hive LLAP 工作负载管理 (WLM) 功能
在交互式查询群集中，资源管理是必不可少的，尤其是在多租户环境中。 Hive LLAP（低延迟分析处理）使用工作负载管理，使用户能够满足特定工作负载需求，并防止争用这些资源。 <br> 工作负载管理实现了资源池（也称为查询池），这样就可以将用于 Hive/LLAP 的资源划分到池中以用于特定工作负载。
通过它，还可以为每个单独的资源池配置资源百分比和查询并行度。

![“LLAP 体系结构。”](./media/hive-workload-management/llap-architecture.png)

## <a name="enable-hive-llap-workload-management-feature-for-hdinsight-clusters"></a>为 HDInsight 群集启用 Hive LLAP 工作负载管理功能

按照下列步骤在 HDInsight 交互式查询群集中启用工作负载管理功能：
1. 创建新的 yarn 队列，用于启动工作负载管理 Tez AM。
2. 通过 Ambari 更改群集配置，以在 Hive 中启用该功能。
3. 创建并激活资源计划。

### <a name="create-a-new-yarn-queue-suitable-for-workload-management-feature"></a>创建适用于工作负载管理功能的新 yarn 队列
在以下[指南](../hdinsight-troubleshoot-yarn.md)的帮助下，创建一个名为 `wm` 的新的 yarn 队列。
基于以下配置在群集上配置 `wm` 队列：

| QueueName   | 容量 | 最大容量 | 优先级 | 最大 AM 资源 |
|------------|---------|--------------|----------|---------------------|
| `default`   | 5%       | 5%           | 0        | 33%                 |
| `llap`      | 85%      | 100%         | 10       | 33%                 |
| `wm`        | 10%      | 15%          | 9        | 100%                |

确认 `wm` 队列配置是否如下所示。
:::image type="content" source="./media/hive-workload-management/wm-yarn-queue.png" alt-text="wm 队列配置。":::

### <a name="enable-workload-management-feature-in-hive-configs"></a>启用 Hive 配置中的工作负载管理功能
将以下属性添加到自定义 hiveserver2-interactive-site，将其值设置为新创建的 yarn 队列的名称，即 `wm` 。 重启交互式 HiveServer，以更改配置。
```
hive.server2.tez.interactive.queue=wm
```

### <a name="create-resource-plan"></a>创建资源计划
以下是介绍如何创建基本资源计划的示例。
![“基本资源计划。”](./media/hive-workload-management/wlm-resourceplan.jpg)

通过 beeline 执行以下命令以创建上述资源计划。

#### <a name="commands-to-create-view-and-drop-the-resource-plan"></a>用于创建、查看和删除资源计划的命令
```hql
# CREATE RESOURCE PLAN
CREATE RESOURCE PLAN demo_plan;

# CREATE POOLS
ALTER POOL demo_plan.default SET ALLOC_FRACTION = 0.65, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.sys_accounts WITH ALLOC_FRACTION = 0.15, QUERY_PARALLELISM = 1;

# CREATE MAPPING
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
 
# CREATE TRIGGERS
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
ALTER TRIGGER demo_plan.defaultToETL ADD TO POOL default;
CREATE TRIGGER demo_plan.ETLKill  WHEN ELAPSED_TIME > 40000 DO KILL;
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# DISABLE PLAN
-- In case plan is in active state first run:
-- DISABLE WORKLOAD MANAGEMENT;
ALTER RESOURCE PLAN demo_plan DISABLE;

# DROP RESOURCE PLAN
DROP RESOURCE PLAN demo_plan;
```

## <a name="understanding-resource-plan"></a>了解资源计划
若要获得最佳资源计划，需要全面了解工作负载要求。

### <a name="number-of-pools"></a>池数
池数受总查询并行度的限制（每个池至少有一个查询）。
大多数工作负载很少需要超过三个池。 
- 默认值，用于交互式查询 
- etl/batch，用于长时间运行的查询
- sys，用于系统管理员

### <a name="total-query_parallelism"></a>总 QUERY_PARALLELISM
可以使用以下公式获取总 QUERY_PARALLELISM 或总并发查询数：

```
Number of total concurrent queries(Tez AMs) = Math.floor( (total cluster memory capacity / size of Tez AM container) x percentage of wm queue capacity)
```

例如： <br/>
假设 Tez AM 容器大小为 4 GB，yarn 群集的总内存容量为 400 GB，其中 10% 分配给 wm 队列， <br/>
总并发查询数 = floor((400/4) x 0.10) = 10

> [!Tip]
> wm 队列中的容量应略大于所需容量，以避免 tez AM 停滞在“已接受”状态，即 `wm` 队列容量可以达到 10.01%，`default` 队列容量可以减少到 4.99%。

### <a name="mappings"></a>映射
映射提供一种将查询定向到特定池的机制。 随着映射数量的增加，多个规则可能适用于一个给定的查询。 若要确定应优先使用哪个规则：如果使用可选 `WITH ORDER` 子句指定顺序，则低顺序规则优先。 否则，`user` 规则优先于规则 `application` ，而 `application` 规则优先于 `group` 规则。 <br/>
具有相同优先级的组规则的顺序未定义。


> [!Note]
> * 当 WLM 计划处于活动状态时，`llap` 队列中的 Tez AM 将保持未使用状态。 在禁用 WLM 资源计划的情况下，`llap` 队列中的这些 Tez AM 将随时可用。
> * 启用 WLM 资源计划启动的 Tez AM 数量等于为给定资源计划配置的总 `QUERY_PARALLELISM`。 应调整 `wm` 队列大小，以避免这些 Tez 停滞在“已接受”状态。
> * 我们只支持在资源计划中使用以下两个计数器：
>    * EXECUTION_TIME
>    * ELAPSED_TIME

## <a name="related-articles"></a>相关文章
* [Hive LLAP 工作负载管理命令摘要](workload-management-commands.md)
* [排除 Hive LLAP 工作负载管理问题](troubleshoot-workload-management-issues.md)


