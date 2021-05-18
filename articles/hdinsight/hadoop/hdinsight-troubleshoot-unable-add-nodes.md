---
title: 无法将节点添加到 Azure HDInsight 群集
description: 排查无法将节点添加到 Azure HDInsight 中的 Apache Hadoop 群集的原因
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944326"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>场景：无法将节点添加到 Azure HDInsight 群集

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法将节点添加到 Azure HDInsight 群集。

## <a name="cause"></a>原因

原因可能有所不同。

## <a name="resolution"></a>解决方法

使用[群集大小](../hdinsight-scaling-best-practices.md)功能计算群集所需的附加核心数。 此值基于新辅助角色节点中的核心总数。 然后，请尝试下面的一个或多个步骤：

* 检查以确定群集位置是否有任何核心可用。

* 查看其他位置可用的核心数。 考虑在具有足够可用核心的其他位置重新创建群集。

* 若要提高特定位置的核心配额，请开具一份支持票证，指出想要提高 HDInsight 核心配额。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]