---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 88e2161cfddf95f7f250b8b76c067d045f1529da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092228"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关 **最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

如果要订阅发行说明，请查看[此 GitHub 存储库](https://github.com/hdinsight/release-notes/releases)上的版本。

## <a name="release-date-11182020"></a>发行日期：2020/11/18

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>为客户管理的密钥静态加密自动轮替密钥
从此发行版开始，客户可使用不限 Azure KeyValut 版本的加密密钥 URL 来管理客户管理的密钥静态加密。 密钥过期时，HDInsight 会自动轮替密钥，或将其替换为新的版本。 请访问[此处](./disk-encryption.md)了解更多详细信息。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>能够为 Spark、Hadoop 和 ML 服务选择不同的 Zookeeper 虚拟机大小
HDInsight 之前不支持为 Spark、Hadoop 和 ML 服务群集类型自定义 Zookeeper 节点大小。 默认情况下为 A2_v2/A2 虚拟机大小（免费提供）。 从此版本开始，你可以选择最适合自己方案的 Zookeeper 虚拟机大小。 虚拟机大小不是 A2_v2/A2 的 Zookeeper 节点需要付费。 A2_v2 和 A2 虚拟机仍免费提供。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 从此版本开始，该服务将逐渐迁移到 [Azure 虚拟机规模集](../virtual-machine-scale-sets/overview.md)。 整个过程可能需要几个月。 迁移区域和订阅后，新创建的 HDInsight 群集将在虚拟机规模集上运行，而无需客户执行任何操作。 预计不会有中断性变更。

## <a name="deprecation"></a>弃用
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>弃用 HDInsight 3.6 ML 服务群集
HDInsight 3.6 ML 服务群集类型将于 2020 年 12 月 31 日终止支持。 2020 年 12 月 31 日之后，客户将不能创建新的 3.6 ML 服务群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请在[此处](./hdinsight-component-versioning.md#available-versions)检查 HDInsight 版本的有效期限和群集类型。

### <a name="disabled-vm-sizes"></a>禁用的 VM 大小
自 2020 年 11 月 16 日起，HDInsight 将阻止新客户使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集。 过去三个月内使用过这些 VM 大小的现有客户将不会受到影响。 自 2021 年 1 月 9 日起，HDInsight 将阻止所有客户使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集。 现有群集将照常运行。 请考虑迁移到 HDInsight 4.0，避免出现潜在的系统/支持中断。

## <a name="behavior-changes"></a>行为更改
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>添加在缩放操作前进行的 NSG 规则检查
HDInsight 为缩放操作添加了网络安全组 (NSG) 和用户定义的路由 (UDR) 检查。 除了群集创建外，还会对群集缩放执行相同的验证。 此验证有助于防止不可预知的错误。 如果验证未通过，则缩放会失败。 若要详细了解如何正确配置 NSG 和 UDR，请参阅 [HDInsight 管理 IP 地址](./hdinsight-management-ip-addresses.md)。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="breaking-change-for-net-for-apache-spark-100"></a>Apache Spark 1.0.0 的 .NET 的重大更改
HDInsight 将在下一版本中介绍 .NET for Apache Spark 的第一个主要版本。 它提供 Spark 2.4. x 和 Spark 3.0. x 与其他功能的数据帧 API 完整性。 此主要版本有重大更改，请参阅 [此迁移 guid](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) ，了解更新代码和管道所需的步骤。 在[此处](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)了解更多信息。

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>默认群集 VM 大小将更改为 Ev3 系列
从下一个版本开始（1 月底左右），默认群集 VM 大小将从 D 系列更改为 Ev3 系列。 此更改适用于头节点和工作器节点。 要避免此更改，请指定要在 ARM 模板中使用的 VM 大小。

### <a name="default-cluster-version-will-be-changed-to-40"></a>默认群集版本将更改为 4.0
自 2021 年 2 月起，HDInsight 群集的默认版本将从 3.6 更改为 4.0。 有关可用版本的详细信息，请参阅[可用版本](./hdinsight-component-versioning.md#available-versions)。 详细了解 [HDInsight 4.0](./hdinsight-version-release.md) 中的新增功能

### <a name="os-version-upgrade"></a>OS 版本升级
HDInsight 正在将 OS 版本从 16.04 升级到 18.04。 升级将在2021年4月之前完成。

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>将于 2021 年 6 月 30 日终止支持 HDInsight 3.6
将终止支持 HDInsight 3.6。 自 2021 年 6 月 30 日起，客户无法创建新的 HDInsight 3.6 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑迁移到 HDInsight 4.0，避免出现潜在的系统/支持中断。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](./hdinsight-component-versioning.md)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。

## <a name="known-issues"></a>已知问题
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>防止 HDInsight 群集 VM 定期重启

从2020年11月开始，你可能已注意到 HDInsight 群集 Vm 定期重启。 这可能是由以下原因导致的：

1.  在群集上启用了 Clamav。 新的 azsec-clamav 包消耗大量的内存，这会触发节点重启。 
2.  每天会安排一个 CRON 作业，该作业监视 Azure 服务使用的证书颁发机构 (CA) 列表的变更。 当有新的 CA 证书可用时，该脚本会将证书添加到 JDK 信任存储，并安排重启。

对于这两个问题，HDInsight 正在为所有正在运行的群集部署修补程序并应用补丁。 若要立即应用修补程序，避免意外的 VM 重启，可以在所有群集节点上以持久脚本操作的形式运行以下脚本操作。 在修补程序和修补完成后，HDInsight 会发布另一个通知。
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```