---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 03874f76772d8722c7161ef43a2297c2e01b7da9
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95748829"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关 **最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

如果要订阅发行说明，请观看 [此 GitHub 存储库](https://github.com/hdinsight/release-notes/releases)中的发布。

## <a name="release-date-11182020"></a>发布日期：11/18/2020

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>为静态客户托管密钥加密自动轮替密钥
从此发行版开始，客户可以使用 Azure KeyValut 版本更少的加密密钥 Url 来管理静态的客户托管密钥。 当密钥过期或替换为新版本时，HDInsight 将自动轮换密钥。 请访问[此处](https://docs.microsoft.com/azure/hdinsight/disk-encryption)了解更多详细信息。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>能够为 Spark、Hadoop 和 ML 服务选择不同的 Zookeeper 虚拟机大小
HDInsight 以前不支持为 Spark、Hadoop 和 ML 服务群集类型自定义 Zookeeper 节点大小。 默认情况下为 A2_v2/A2 虚拟机大小（免费提供）。 在此版本中，你可以选择最适合你的方案的 Zookeeper 虚拟机大小。 虚拟机大小不是 A2_v2/A2 的 Zookeeper 节点需要付费。 A2_v2 和 A2 虚拟机仍免费提供。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 从此版本开始，该服务将逐渐迁移到 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整个过程可能需要几个月。 迁移区域和订阅后，新创建的 HDInsight 群集将在虚拟机规模集上运行，而无需客户执行任何操作。 预计不会有中断性变更。

## <a name="deprecation"></a>弃用
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>弃用 HDInsight 3.6 ML 服务群集
HDInsight 3.6 ML 服务群集类型将在12月 31 2020 结束支持。 31 2020 年12月之后，客户不会创建新的 3.6 ML 服务群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)检查 HDInsight 版本的有效期限和群集类型。

### <a name="disabled-vm-sizes"></a>禁用的 VM 大小
自 16 2020 年11月起，HDInsight 将阻止使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集的新客户。 过去三个月内使用这些 VM 大小的现有客户将不会受到影响。 从 9 2021 年1月开始，HDInsight 会阻止使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集的所有客户。 现有群集将按原样运行。 请考虑移动到 HDInsight 4.0，以避免潜在的系统/支持中断。

### <a name="behavior-changes"></a>行为更改
此版本没有行为变更。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="default-cluster-version-will-be-changed-to-40"></a>默认群集版本将更改为4。0
从2021年2月开始，HDInsight 群集的默认版本将从3.6 更改为4.0。 有关可用版本的详细信息，请参阅 [可用版本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)。 详细了解[HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)中的新增功能

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>3.6 年 6 30 2021 月的 HDInsight 结束支持
HDInsight 3.6 将提供支持。 启动窗体6月 30 2021，客户无法创建新的 HDInsight 3.6 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑移动到 HDInsight 4.0，以避免潜在的系统/支持中断。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](./hdinsight-component-versioning.md)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。
