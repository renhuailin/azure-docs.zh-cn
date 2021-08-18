---
title: Apache Hive 与 Apache Zookeeper 建立连接 - Azure HDInsight
description: 由于 Azure HDInsight 中的 Apache Zookeeper 问题，因此无法访问 Apache Hive 视图
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: cc7ad8d32e4d15b8e0671d162b1796619b4879c7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290712"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>方案：Apache Hive 无法与 Azure HDInsight 中的 Apache Zookeeper 建立连接

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Hive 视图不可访问，`/var/log/hive` 中的日志显示类似于以下内容的错误：

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (<zookeepername1>.cloud.wbmi.com:2181,<zookeepername2>.cloud.wbmi.com:2181,<zookeepername3>.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive 可能无法建立与 Zookeeper 的连接，这会阻止 Hive 视图启动。

## <a name="resolution"></a>解决方法

1. 检查 Zookeeper 服务是否正常运行。

1. 检查 Zookeeper 服务是否具有 Hive Server2 的 ZNode 条目。 该值会缺失或不正确。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server <zookeepername1>
    [zk: <zookeepername1>(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 若要重新建立连接，请重新启动 Zookeeper 节点，并重新启动 HiveServer2。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]