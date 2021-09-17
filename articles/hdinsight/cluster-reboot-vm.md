---
title: 重新启动 Azure HDInsight 群集的 VM
description: 了解如何重新启动 Azure HDInsight 群集的无响应的 VM。
ms.custom: hdinsightactive, devx-track-azurepowershell
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: f2bb1ebd6e8db80a071dc3f82e1eedded26f6116
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634483"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>重新启动 HDInsight 群集的 VM

Azure HDInsight 群集包含作为群集节点的虚拟机 (VM) 组。 对于长时间运行的群集，这些节点可能因为各种原因而无法响应。 本文介绍如何在 HDInsight 群集中重新启动无响应的 VM。

## <a name="when-to-reboot"></a>何时重新启动

> [!WARNING]
> 重新启动群集中的 VM 时，该节点不可用，且节点上的服务必须重启。

节点重新启动时，群集可能会运行不正常，作业运行速度也可能会减慢或作业会失败。 如果正在尝试重新启动活动的头节点，则所有正在运行的作业都将停止。 在服务启动并再次运行之前，将无法向群集提交作业。 出于这些原因，仅应在必要时重新启动 VM。 在以下情况时，请考虑重新启动 VM：

- 你无法使用 SSH 连接到该节点，但它会响应 ping。
- 工作器节点在 Ambari UI 中未检测信号的情况下关闭。
- 节点上的临时磁盘已满。
- VM 上的进程表中有很多条目的进程已完成，但状态显示为“终止状态”。

> [!NOTE]
> HBase 和 Kafka 群集不支持重启 VM，因为重启可能会导致数据丢失 。

## <a name="use-powershell-to-reboot-vms"></a>使用 PowerShell 重新启动 VM

使用“节点重新启动”操作需执行两个步骤：列出节点和重新启动节点。

1. 列出节点。 可以在 [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost) 中获取群集节点列表。

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. 重新启动主机。 获取想要重新启动的节点名称后，请使用 [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost) 重启节点。

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>使用 REST API 重新启动 VM

可以使用 API 文档中的“试一试”功能将请求发送到 HDInsight 。 使用“节点重新启动”操作需执行两个步骤：列出节点和重新启动节点。

1. 列出节点。 可以在 REST API 或 Ambari 中获取群集节点列表。 有关详细信息，请参阅 [HDInsight 列表主机 REST API 操作](/rest/api/hdinsight/virtualmachines/listhosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. 重新启动主机。 获取想要重新启动的节点名称后，请使用 REST API 重新启动节点来重启节点。 节点名称遵循的模式为 *NodeType(wn/hn/zk)*  + *x* + *群集名称的前六个字符*。 有关详细信息，请参阅 [HDInsight 重启主机 REST API 操作](/rest/api/hdinsight/virtualmachines/restarthosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

要重新启动的节点的实际名称在请求正文的 JSON 数组中指定。

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>后续步骤

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight 虚拟机 REST API](/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](/rest/api/hdinsight/)
