---
title: 故障排除：Hive 日志填满磁盘空间 - Azure HDInsight
description: 本文提供了当 Apache Hive 日志填满 Azure HDInsight 中头节点上的磁盘空间时要遵循的故障排除步骤。
ms.service: hdinsight
ms.topic: troubleshooting
author: kcheeeung
ms.author: kecheung
ms.date: 05/21/2021
ms.openlocfilehash: d4fb0c4a197f28321e4328d17f00173f45b28d1b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290730"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>方案：Apache Hive 日志即将填满 Azure HDInsight 的头节点的磁盘空间

本文介绍了与 Azure HDInsight 群集的头节点上的磁盘空间不足相关的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 HDI 4.0 Apache Hive/LLAP 群集中，不需要的日志占用了头节点上的整个磁盘空间。 这种情况可能会导致以下问题：

- 由于头节点上没有剩余空间，SSH 访问失败。
- HiveServer2 Interactive 无法重启。

## <a name="cause"></a>原因

未在高级 hive-log4j2 配置中配置 Hive 日志自动删除功能。 就客户的使用模式来说，60GB 的默认大小限制占用了太多的空间。

## <a name="resolution"></a>解决方法

1. 在 Ambari 门户中转到 Hive 组件摘要，然后选择“配置”选项卡。

2. 转到“高级设置”中的 `Advanced hive-log4j2` 部分。

3. 确保已启用这些设置。 如果看不到任何相关设置，请追加这些设置：
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfFileName
    appender.RFA.strategy.action.condition.regex = hive*.*log.*
    appender.RFA.strategy.action.condition.nested_condition.type = IfAny
    # Deletes logs based on total accumulated size, keeping the most recent
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

4. 我们将介绍进行删除所需的三个基本选项：
- **总大小**
    - 将 `appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds` 更改为所选的大小限制。

- **日期**
    - 还可以取消注释和转换条件。 然后将 `appender.RFA.strategy.action.condition.nested_condition.lastMod.age` 更改为所选的期限。

    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

- **组合使用“总大小”和“日期”**
    - 可以通过取消注释将两个选项组合在一起，如下所示。 当满足其中一个条件时，log4j2 就会开始删除日志。
    
    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```
5. 保存配置并重启所需的组件。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
