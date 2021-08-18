---
title: Azure HDInsight 群集中出现监视器 BUG 软锁定 CPU 错误
description: 监视器 BUG 软锁定 CPU 出现在 Azure HDInsight 群集的内核系统日志中
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: d2deb421dce440c1c8f949c858269fed038ac3d5
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299746"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>场景：Azure HDInsight 群集中出现“监视器 BUG 软锁定 CPU”错误

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

内核 syslog 包含错误消息：`watchdog: BUG: soft lockup - CPU`。

## <a name="cause"></a>原因

Linux 内核中的一个 [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) 导致 CPU 软锁定。

## <a name="resolution"></a>解决方法

应用内核修补程序。 以下脚本将升级 Linux 内核，并在 24 小时内的不同时间重新启动计算机。 在两个批处理文件中执行脚本操作。 第一个批处理文件在除头节点以外的所有节点上运行。 第二个批处理文件在头节点上运行。 不要同时在头节点和其他节点上运行。

1. 从 Azure 门户导航到你的 HDInsight 群集。

1. 转到“脚本操作”。

1. 选择“提交新项”并按如下所示提供输入

    | 属性 | 值 |
    | --- | --- |
    | 脚本类型 | -Custom |
    | 名称 |内核软锁定问题的修复 |
    | Bash 脚本 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 节点类型 |Worker、Zookeeper |
    | 参数 |不适用 |

    若要在添加新节点时执行脚本，请选择“保留此脚本操作...”。

1. 选择“创建”  。

1. 等待执行成功。

1. 按照与步骤 3 相同的步骤在“头节点”上执行脚本操作，但这一次需使用节点类型：头节点

1. 等待执行成功。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]