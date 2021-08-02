---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/02/2021
ms.openlocfilehash: 61b32852de0cb15db3860b72561f87e56db8f40d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903844"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关 **最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

如果要订阅发行说明，请查看[此 GitHub 存储库](https://github.com/hdinsight/release-notes/releases)上的版本。


## <a name="release-date-06022021"></a>发行日期：2021 年 6 月 2 日

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

此发行版本的操作系统版本包括：
- HDInsight 3.6：Ubuntu 16.04.7 LTS
- HDInsight 4.0：Ubuntu 18.04.5 LTS

## <a name="new-features"></a>新增功能
### <a name="os-version-upgrade"></a>OS 版本升级
如 [Ubuntu 发布周期](https://ubuntu.com/about/release-cycle)中所述，Ubuntu 16.04 内核将于 2021 年 4 月终止生命周期 (EOL)。 我们从此发行版本开始推出在 Ubuntu 18.04 上运行的新 HDInsight 4.0 群集映像。 默认情况下，新创建的 HDInsight 4.0 群集可用后，将在 Ubuntu 18.04 上运行。 Ubuntu 16.04 上的现有群集将按原样运行，并可获得完全支持。

HDInsight 3.6 将继续在 Ubuntu 16.04 上运行。 从 2021 年 7 月 1 日开始，此版本将变更为基本支持（从标准支持）。 有关日期和支持选项的详细信息，请参阅 [Azure HDInsight 版本](./hdinsight-component-versioning.md#supported-hdinsight-versions)。 Ubuntu 18.04 不支持 HDInsight 3.6。 如果想要使用 Ubuntu 18.04，需要将群集迁移到 HDInsight 4.0。 

如果想要将现有 HDInsight 4.0 群集迁移到 Ubuntu 18.04，则需要删除群集并重新创建。 在 Ubuntu 18.04 支持可用后，可计划创建或重新创建群集。

创建新群集后，可以通过 SSH 连接到群集，并运行 `sudo lsb_release -a` 验证此群集是否在 Ubuntu 18.04 上运行。 建议先在测试订阅环境中测试应用程序，然后再迁移到生产环境。 [了解有关 HDInsight Ubuntu 18.04 更新的详细信息](./hdinsight-ubuntu-1804-qa.md)。

### <a name="scaling-optimizations-on-hbase-accelerated-writes-clusters"></a>HBase 加速写入群集的缩放优化
HDInsight 对启用 HBase 加速写入的群集缩放做出了一些改进和优化。 [了解有关 HBase 加速写入的详细信息](./hbase/apache-hbase-accelerated-writes.md)。

## <a name="deprecation"></a>弃用
此版本没有任何弃用功能。

## <a name="behavior-changes"></a>行为更改
### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsight-40"></a>禁用将 Stardard_A5 VM 大小作为 HDInsight 4.0 的头节点
HDInsight 群集头节点负责初始化和管理群集。 Standard_A5 VM 大小在作为 HDInsight 4.0 的头节点时存在可靠性问题。 从此版本开始，客户将无法创建以 Standard_A5 VM 大小作为头节点的新群集。 你可以使用其他双核 VM，比如 E2_v3 或 E2s_v3。 现有群集将照常运行。 强烈建议将四核 VM 用于头节点，以确保生产 HDInsight 群集的高可用性和高可靠性。

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>对于在 Azure 虚拟机规模集上运行的群集，网络接口资源不可见
HDInsight 正在逐步迁移到 Azure 虚拟机规模集。 对于使用 Azure 虚拟机规模集的群集的客户，虚拟机的网络接口不再可见。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query 仅支持基于计划的自动缩放

随着客户方案日益成熟和多样化，我们发现 Interactive Query (LLAP) 基于负载的自动缩放存在一些限制。 这些限制是由 LLAP 查询动态性、未来负载预测准确度问题以及 LLAP 计划程序的任务再分发问题引起的。 鉴于这些限制，用户可能会发现，启用自动缩放时，LLAP 群集中的查询运行速度较慢。 自动缩放对性能的影响超出了其成本优势。

从 2021 年 7 月开始，HDInsight 中的 Interactive Query 工作负载仅支持基于计划的自动缩放。 你无法再对新 Interactive Query 群集启用自动缩放。 正在运行的现有群集可在存在上述已知限制的情况下继续运行。 

Microsoft 建议将 LLAP 更改为基于计划的自动缩放。  你可以通过 Grafana Hive 仪表板分析群集的当前使用模式。 有关详细信息，请参阅[自动缩放 Azure HDInsight 群集](hdinsight-autoscale-clusters.md)。 

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>从 2021 年 7 月 1 日开始为 HDInsight 3.6 提供基本支持
从 2021 年 7 月 1 日开始，Microsoft 将为某些 HDInsight 3.6 群集类型提供[基本支持](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)。 基本支持计划的可用日期截至 2022 年 4 月 3 日。 从 2021 年 7 月 1 日开始，你将被自动加人基本支持。 无需执行任何操作即可选择加入。 请参阅[文档](hdinsight-36-component-versioning.md)了解基本支持中包含哪些群集类型。 

不建议在 HDInsight 3.6 上构建任何新的解决方案，因为将在现有的 3.6 环境上冻结更改。 建议[将群集迁移到 HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)。 详细了解 [HDInsight 4.0 中的新增功能](hdinsight-version-release.md#whats-new-in-hdinsight-40)。

### <a name="vm-host-naming-will-be-changed-on-july-1-2021"></a>VM 主机命名将在 2021 年 7 月 1 日发生变化
HDInsight 目前使用 Azure 虚拟机来预配群集。 此服务正在逐步迁移到 [Azure 虚拟机规模集](../virtual-machine-scale-sets/overview.md)。 此迁移将改变群集主机名 FQDN 名称格式，并且也不能保证按顺序显示主机名中的编号。 如果要获取每个节点的 FQDN 名称，请参阅[查找群集节点的主机名](./find-host-name.md)。

### <a name="move-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 此服务将逐步迁移到 [Azure 虚拟机规模集](../virtual-machine-scale-sets/overview.md)。 整个过程可能需要几个月。 迁移区域和订阅后，新创建的 HDInsight 群集将在虚拟机规模集上运行，而无需客户执行任何操作。 预计不会有中断性变更。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
可以在[此文档](./hdinsight-component-versioning.md)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。
