---
title: Apache Spark thrift 的 RpcTimeoutException - Azure HDInsight
description: 使用 Apache Spark thrift 服务器处理大型数据集时看到 502 错误
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 353301b55031e1dde181cac61f47edd68cc33131
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282180"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Spark thrift 服务器发生 RpcTimeoutException

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Spark 应用程序失败并出现 `org.apache.spark.rpc.RpcTimeoutException` 异常和以下示例中所示的消息：`Futures timed out`：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`sparkthriftdriver.log` 中还可能出现 `OutOfMemoryError` 和 `overhead limit exceeded` 错误，如以下示例中所示：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

这些错误的原因是数据处理期间内存资源不足。 如果启动 Java 垃圾回收进程，可能会导致 Spark 应用程序停止响应。 查询将开始超时并停止处理。 `Futures timed out` 错误表示群集遭受严重的压力。

## <a name="resolution"></a>解决方法

通过添加更多的工作器节点或增加现有群集节点的内存容量，来增大群集大小。 还可以调整数据管道，以减少一次性处理的数据量。

`spark.network.timeout` 将控制所有网络连接的超时。 增大网络超时可为某些关键操作提供更多的时间，但不能彻底解决该问题。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]