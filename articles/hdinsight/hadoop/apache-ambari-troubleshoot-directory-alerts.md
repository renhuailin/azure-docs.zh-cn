---
title: Azure HDInsight 中的 Apache Ambari 目录警报
description: 讨论和分析 HDInsight 中 Apache Ambari 目录警报的可能原因和解决方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998305"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Ambari 目录警报

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从 Apache Ambari 收到类似于以下内容的错误：

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>原因

受影响的工作器节点上缺少 Ambari 警报中提到的目录。

## <a name="resolution"></a>解决方法

在受影响的工作器节点上手动创建缺少的目录。

1. 通过 SSH 连接到相关的工作器节点。

1. 获取根用户：`sudo su`。

1. 以递归方式创建所需目录。

1. 更改这些目录的所有者和组。

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. 从 Apache Ambari UI 禁用然后启用警报。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]