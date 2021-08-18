---
title: Apache Spark 的 IllegalArgumentException 错误 - Azure HDInsight
description: Azure 数据工厂的 Azure HDInsight 中 Apache Spark 活动的 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 2b892e6419045d362ac8675a6e8976600650851a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288912"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>方案：Azure HDInsight 中 Apache Spark 活动的 IllegalArgumentException

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

尝试在 Azure 数据工厂管道中执行 Spark 活动时，收到以下异常：

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

如果应用程序 jar 文件不在 Spark 群集的默认/主存储中，则 Spark 作业将失败。

这是在此 bug 中跟踪的 Spark 开源框架的一个已知问题：[如果 fs.defaultFS 和应用程序 jar 是不同的 URL，Spark 作业将失败](https://issues.apache.org/jira/browse/SPARK-22587)。

此问题已在 Spark 2.3.0 中解决。

## <a name="resolution"></a>解决方法

请确保将应用程序 jar 存储在 HDInsight 群集的默认/主存储上。 对于 Azure 数据工厂，请确保 ADF 链接服务指向 HDInsight 默认容器，而不是辅助容器。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]