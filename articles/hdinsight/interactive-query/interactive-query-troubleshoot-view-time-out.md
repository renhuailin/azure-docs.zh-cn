---
title: 在查询结果时 Apache Hive 视图超时 - Azure HDInsight
description: 在 Azure HDInsight 中提取查询结果时，Apache Hive 视图超时
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 5b1ec7fec182d5b0b6f2d68467d6e3e84fdb5f3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601399"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>场景：在 Azure HDInsight 中提取查询结果时，Apache Hive 视图超时

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从 Apache Hive 视图运行某些查询时，可能会遇到以下错误：

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>原因

Hive 视图默认超时值可能不适用于正在运行的查询。 指定的时间段对于 Hive 视图而言太短，无法提取查询结果。

## <a name="resolution"></a>解决方法

1. 通过在中 `/etc/ambari-server/conf/ambari.properties` 为这两个头节点设置以下属性，增大 Apache Ambari Hive 视图的超时值。
  ```
  views.ambari.request.read.timeout.millis=300000
  views.request.read.timeout.millis=300000
  views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
  ```
  `HIVE_VIEW_INSTANCE_NAME` 的值可在 Hive 视图 URL 的末尾找到。

2. 通过运行以下命令重新启动活动的 Ambari 服务器。 如果收到一条错误消息，指出它不是活动的 Ambari 服务器，只需通过 ssh 连接到下一个头节点并重复此步骤。
  ```
  sudo ambari-server restart
  ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
