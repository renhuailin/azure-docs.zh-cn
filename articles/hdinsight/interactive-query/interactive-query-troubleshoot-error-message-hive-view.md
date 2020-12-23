---
title: Apache Hive 视图中未显示的错误消息-Azure HDInsight
description: 在 Apache Hive 视图中查询失败，而没有有关 Azure HDInsight 群集的任何详细信息。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288938"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>方案：Azure HDInsight 的 Apache Hive 视图中未显示查询错误消息

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Hive 视图查询错误消息将如下所示，没有进一步的信息：

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

有时，查询失败的错误消息可能太大，无法在 Hive 视图主页上显示。

## <a name="resolution"></a>解决方法

检查 Hive_view 右上角的“通知”选项卡，以查看完整的 Stacktrace 和错误消息。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]