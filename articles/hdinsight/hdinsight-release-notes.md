---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f6b7000812f1adfe6ff7bd93711c9b8fe4ff9adc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988365"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关 **最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

如果要订阅发行说明，请查看[此 GitHub 存储库](https://github.com/hdinsight/release-notes/releases)上的版本。

## <a name="release-date-02052021"></a>发布日期：02/05/2021

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="dav4-series-support"></a>Dav4 系列支持
HDInsight 在此版本中添加了 Dav4 系列支持。 [在此处了解有关 Dav4 系列的](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)详细信息。

### <a name="kafka-rest-proxy-ga"></a>Kafka REST 代理 GA 
使用 Kafka REST 代理可以通过基于 HTTPS 的 REST API 与 Kafka 群集交互。 从此版本开始，Kafka Rest 代理已正式发布。 [在此处了解有关 KAFKA REST 代理](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy)的详细信息。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 服务将逐步迁移到 [Azure 虚拟机规模集](../virtual-machine-scale-sets/overview.md)。 整个过程可能需要几个月。 迁移区域和订阅后，新创建的 HDInsight 群集将在虚拟机规模集上运行，而无需客户执行任何操作。 预计不会有中断性变更。

## <a name="deprecation"></a>弃用
### <a name="disabled-vm-sizes"></a>禁用的 VM 大小
自 2021 年 1 月 9 日起，HDInsight 将阻止所有客户使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集。 现有群集将照常运行。 请考虑迁移到 HDInsight 4.0，避免出现潜在的系统/支持中断。

## <a name="behavior-changes"></a>行为更改
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>默认群集 VM 大小更改为 Ev3 系列 
默认群集 VM 大小将从 D 系列更改为 Ev3 系列。 此更改适用于头节点和工作器节点。 若要避免此更改影响测试的工作流，请在 ARM 模板中指定要使用的 VM 大小。

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>对于在 Azure 虚拟机规模集上运行的群集，网络接口资源不可见
HDInsight 将逐步迁移到 Azure 虚拟机规模集。 适用于使用 Azure 虚拟机规模集的群集的客户不再看到虚拟机的网络接口。


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Apache Spark 1.0.0 的 .NET 的重大更改
HDInsight 在下一个版本中引入了 .NET for Apache Spark 的第一个主要版本。 它提供 Spark 2.4. x 和 Spark 3.0. x 与其他功能的数据帧 API 完整性。 此主要版本有重大更改，请参阅 [此迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) ，了解更新代码和管道所需的步骤。 在[此处](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)了解更多信息。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="default-cluster-version-will-be-changed-to-40"></a>默认群集版本将更改为 4.0
自 2021 年 2 月起，HDInsight 群集的默认版本将从 3.6 更改为 4.0。 有关可用版本的详细信息，请参阅[可用版本](./hdinsight-component-versioning.md#available-versions)。 详细了解 [HDInsight 4.0](./hdinsight-version-release.md)中的新增功能。

### <a name="os-version-upgrade"></a>OS 版本升级
HDInsight 正在从 Ubuntu 16.04 升级到18.04。 升级将在2021年4月之前完成。

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>将于 2021 年 6 月 30 日终止支持 HDInsight 3.6
将终止支持 HDInsight 3.6。 自 2021 年 6 月 30 日起，客户无法创建新的 HDInsight 3.6 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑迁移到 HDInsight 4.0，避免出现潜在的系统/支持中断。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](./hdinsight-component-versioning.md)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。

