---
title: 故障排除： Apache Hive 日志填满磁盘空间-Azure HDInsight
description: 本文提供了在 Apache Hive 日志填充 Azure HDInsight 中头节点上的磁盘空间时要遵循的故障排除步骤。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 107ec012bf2ff76ee1cbe4c5f8252566a5a16127
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288932"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>方案： Apache Hive 日志在 Azure HDInsight 中的头节点上填满磁盘空间

本文介绍 Azure HDInsight 群集中头节点上的磁盘空间不足的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

在 Apache Hive/LLAP 群集上，不需要的日志占用了头节点上的整个磁盘空间。 这种情况可能会导致以下问题：

- 由于头节点上没有剩余空间，SSH 访问失败。
- Ambari 引发 *HTTP 错误：503服务不可用* 。
- 无法重新启动 HiveServer2 交互。

`ambari-agent`发生此问题时，日志将包含以下条目：
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在 "高级 Hive log4j 配置" 中，当前的默认删除计划是基于上次修改日期删除30天以前的文件。

## <a name="resolution"></a>解决方法

1. 在 Ambari 门户中转到 Hive 组件摘要，然后 **选择 "配置" 选项卡** 。

2. 请参阅 `Advanced hive-log4j` **高级设置** 中的部分。

3. 将 `appender.RFA.strategy.action.condition.age` 参数设置为你选择的期限。 此示例将年龄设置为14天： `appender.RFA.strategy.action.condition.age = 14D`

4. 如果看不到任何相关设置，请附加这些设置：
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. 设置 `hive.root.logger` 为 `INFO,RFA` ，如下面的示例中所示。 默认设置为 `DEBUG` ，这会使日志变大。

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. 保存配置并重新启动所需的组件。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
