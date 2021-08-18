---
title: Apache Spark 流式处理应用程序在 Azure HDInsight 中执行 24 天后停止
description: Apache Spark 流式处理应用程序在执行 24 天后停止，日志文件中没有错误。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 8d446063c7e8836a1163e0b6327539b055c41476
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288930"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>方案：Apache Spark 流式处理应用程序在 Azure HDInsight 中执行 24 天后停止

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Spark 流式处理应用程序在执行 24 天后停止，日志文件中没有错误。

## <a name="cause"></a>原因

`livy.server.session.timeout` 值控制 Apache Livy 应等待会话完成的时间。 会话长度达到 `session.timeout` 值后，会自动终止 Livy 会话和应用程序。

## <a name="resolution"></a>解决方法

对于长时间运行的作业，请使用 Ambari UI 增加 `livy.server.session.timeout` 值。 可使用 URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` 从 Ambari UI 访问 Livy 配置。

将 `<yourclustername>` 替换为门户中显示的 HDInsight 群集的名称。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]