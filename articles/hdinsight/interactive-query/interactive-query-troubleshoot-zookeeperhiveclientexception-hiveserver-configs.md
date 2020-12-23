---
title: Apache Hive Zeppelin 解释器错误-Azure HDInsight
description: Apache Zeppelin Hive JDBC 解释器指向 Azure HDInsight 中的错误 URL
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8e66db96f895d0d29d6c5018478e98c1f39e62ad
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288700"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>方案：Apache Hive Zeppelin 解释器在 Azure HDInsight 中给出 Zookeeper 错误

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 Apache Hive LLAP 群集上，Zeppelin 解释器在尝试执行查询时给出以下错误消息：

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>原因

Zeppelin Hive JDBC 解释器指向错误的 URL。

## <a name="resolution"></a>解决方法

1. 导航到 Hive 组件摘要并将“Hive JDBC URL”复制到剪贴板。

1. 导航到 `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. 编辑 JDBC 设置：将 hive.url 值更新为步骤 1 中复制的 Hive JDBC URL

1. 保存，然后重试查询

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]