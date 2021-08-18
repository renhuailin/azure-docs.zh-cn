---
title: NoClassDefFoundError - 在 Azure HDInsight 中使用 Apache Kafka 数据的 Apache Spark
description: 从 Apache Kafka 群集读取数据的 Apache Spark 流式处理作业失败，并在 Azure HDInsight 中出现 NoClassDefFoundError
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: fc4eb5aae7da3d036d6fb319c5eb13e41793b4cf
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282270"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>读取 Apache Kafka 数据的 Apache Spark 流式处理作业失败，在 HDInsight 中显示 NoClassDefFoundError

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Spark 群集运行从 Apache Kafka 群集读取数据的 Spark 流式处理作业。 如果启用了 Kafka 流压缩，则 Spark 流式处理作业将会失败。 在这种情况下，由于出现错误，Spark 流式处理 Yarn 应用 application_1525986016285_0193 失败：

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>原因

此错误可能是由于指定的 `spark-streaming-kafka` jar 文件版本与正在运行的 Kafka 群集的版本不同而导致。

例如，如果运行的是 Kafka 群集版本 0.10.1，则以下命令将导致错误：

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>解决方法

使用带 `–packages` 选项的 Spark-submit 命令，并确保 spark-streaming-kafka jar 文件的版本与正在运行的 kafka 群集的版本相同。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]