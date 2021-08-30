---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、Hive 等工具的开发技巧和详细信息。
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: cf726033a90e256a2d41a3249e237f5eb91aee84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732916"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关 **最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

如果要订阅发行说明，请查看[此 GitHub 存储库](https://github.com/hdinsight/release-notes/releases)上的版本。

## <a name="release-date-07272021"></a>发行日期：2021 年 7 月 27 日

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

此发行版的 OS 版本为：
- HDInsight 3.6：Ubuntu 16.04.7 LTS
- HDInsight 4.0：Ubuntu 18.04.5 LTS

## <a name="new-features"></a>新增功能
### <a name="new-azure-monitor-integration-experience-preview"></a>新的 Azure Monitor 集成体验（预览版）
此次发布将在美国东部和西欧推出预览版的新 Azure Monitor 集成体验。 请在[此处](./log-analytics-migration.md#migrate-to-the-new-azure-monitor-integration)详细了解新的 Azure Monitor 体验。

## <a name="deprecation"></a>弃用
### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>从 2021 年 7 月 1 日开始为 HDInsight 3.6 提供基本支持
从 2021 年 7 月 1 日开始，Microsoft 为某些 HDInsight 3.6 群集类型提供[基本支持](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)。 基本支持计划的可用日期截至 2022 年 4 月 3 日。 从 2021 年 7 月 1 日开始，你会被自动加入基本支持。 无需执行任何操作即可选择加入。 请参阅[文档](hdinsight-36-component-versioning.md)了解基本支持中包含哪些群集类型。 

不建议在 HDInsight 3.6 上构建任何新的解决方案，因为将在现有的 3.6 环境上冻结更改。 建议[将群集迁移到 HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)。 详细了解 [HDInsight 4.0 中的新增功能](hdinsight-version-release.md#whats-new-in-hdinsight-40)。

## <a name="behavior-changes"></a>行为更改
### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query 仅支持基于计划的自动缩放
随着客户方案越来越成熟和多样化，我们发现，使用基于负载的自动缩放 Interactive Query (LLAP) 存在一些限制。 存在这些限制的原因可能是 LLAP 查询动态的性质、未来的负载预测准确性问题以及 LLAP 计划程序任务重新分发中的问题。 由于这些限制，启用自动缩放后，用户可能会看到其查询在 LLAP 群集上的运行速度变慢。 自动缩放对性能的影响超出了其成本优势。

从 2021 年 7 月开始，HDInsight 中的 Interactive Query 工作负载仅支持基于计划的自动缩放。 你无法再对新 Interactive Query 群集启用基于负载的自动缩放。 现有正在运行的群集可以继续运行，但存在上述已知限制。 

Microsoft 建议你改用基于计划的自动缩放以使用 LLAP。  可以通过 Grafana Hive 仪表板分析群集的当前使用模式。 有关详细信息，请参阅[自动缩放 Azure HDInsight 群集](hdinsight-autoscale-clusters.md)。 

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="built-in-llap-component-in-esp-spark-cluster-will-be-removed"></a>将删除 ESP Spark 群集中的内置 LLAP 组件
HDInsight 4.0 ESP Spark 群集具有在两个头节点上运行的内置 LLAP 组件。 ESP Spark 群集中的 LLAP 组件最初是为 HDInsight 3.6 ESP Spark 添加的，但 HDInsight 4.0 ESP Spark 没有实际用例。 在定于 2021 年 9 月的下一次发布中，HDInsight 将从 HDInsight 4.0 ESP Spark 群集中删除内置 LLAP 组件。 此更改有助于消除头节点工作负载，并避免混淆 ESP Spark 和 ESP Interactive Hive 群集类型。

## <a name="new-region"></a>新区域
- 美国西部 3
- Jio 印度西部
- 澳大利亚中部

## <a name="component-version-change"></a>组件版本更改
此次发布更改了以下组件版本：
- ORC 版本从 1.5.1 更新为 1.5.9

可以在[此文档](./hdinsight-component-versioning.md)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。

## <a name="back-ported-jiras"></a>反向移植的 JIRA
下面是此次发布中反向移植的 Apache JIRA：

| 受影响的功能    |   Apache JIRA                                                      |
|---------------------|--------------------------------------------------------------------|
| 日期/时间戳    | [HIVE-25104](https://issues.apache.org/jira/browse/HIVE-25104)     |
|                     | [HIVE-24074](https://issues.apache.org/jira/browse/HIVE-24074)     |
|                     | [HIVE-22840](https://issues.apache.org/jira/browse/HIVE-22840)     |
|                     | [HIVE-22589](https://issues.apache.org/jira/browse/HIVE-22589)     |
|                     | [HIVE-22405](https://issues.apache.org/jira/browse/HIVE-22405)     |
|                     | [HIVE-21729](https://issues.apache.org/jira/browse/HIVE-21729)     |
|                     | [HIVE-21291](https://issues.apache.org/jira/browse/HIVE-21291)     |
|                     | [HIVE-21290](https://issues.apache.org/jira/browse/HIVE-21290)     |
| UDF                 | [HIVE-25268](https://issues.apache.org/jira/browse/HIVE-25268)     |
|                     | [HIVE-25093](https://issues.apache.org/jira/browse/HIVE-25093)     |
|                     | [HIVE-22099](https://issues.apache.org/jira/browse/HIVE-22099)     |
|                     | [HIVE-24113](https://issues.apache.org/jira/browse/HIVE-24113)     |
|                     | [HIVE-22170](https://issues.apache.org/jira/browse/HIVE-22170)     |
|                     | [HIVE-22331](https://issues.apache.org/jira/browse/HIVE-22331)     |
| ORC                 | [HIVE-21991](https://issues.apache.org/jira/browse/HIVE-21991)     |
|                     | [HIVE-21815](https://issues.apache.org/jira/browse/HIVE-21815)     |
|                     | [HIVE-21862](https://issues.apache.org/jira/browse/HIVE-21862)     |
| 表架构        | [HIVE-20437](https://issues.apache.org/jira/browse/HIVE-20437)     |
|                     | [HIVE-22941](https://issues.apache.org/jira/browse/HIVE-22941)     |
|                     | [HIVE-21784](https://issues.apache.org/jira/browse/HIVE-21784)     |
|                     | [HIVE-21714](https://issues.apache.org/jira/browse/HIVE-21714)     |
|                     | [HIVE-18702](https://issues.apache.org/jira/browse/HIVE-18702)     |
|                     | [HIVE-21799](https://issues.apache.org/jira/browse/HIVE-21799)     |
|                     | [HIVE-21296](https://issues.apache.org/jira/browse/HIVE-21296)     |
| 工作负载管理 | [HIVE-24201](https://issues.apache.org/jira/browse/HIVE-24201)     |
| 压缩          | [HIVE-24882](https://issues.apache.org/jira/browse/HIVE-24882)     |
|                     | [HIVE-23058](https://issues.apache.org/jira/browse/HIVE-23058)     |
|                     | [HIVE-23046](https://issues.apache.org/jira/browse/HIVE-23046)     |
| 具体化视图   | [HIVE-22566](https://issues.apache.org/jira/browse/HIVE-22566)     |