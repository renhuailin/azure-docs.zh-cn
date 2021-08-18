---
title: Azure HDInsight 中的 Apache Ambari 目录警报
description: 讨论和分析 HDInsight 中导致 Apache Ambari 目录警报的可能原因和解决方法。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 2e5e74c49b7654c2ade20e78f7098192649f9385
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300122"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>场景：Azure HDInsight 中的 Apache Ambari 目录警报

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

收到来自 Apache Ambari 的错误，这些错误类似于：

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

1. 从 Apache Ambari UI 中禁用，然后启用警报。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]