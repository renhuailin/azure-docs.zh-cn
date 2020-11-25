---
title: Azure HDInsight 中的 Apache Ambari 检测信号问题
description: 查看 Azure HDInsight 中 Apache Ambari 检测信号问题的各种原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: adc5dfcef8cce269b6b6d982178433b8ee163f92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998256"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 检测信号问题

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="scenario-high-cpu-utilization"></a>方案：CPU 利用率较高

### <a name="issue"></a>问题

Ambari 代理的 CPU 利用率很高，这会导致 Ambari UI 发出警报，即对于某些节点，Ambari 代理检测信号丢失。 检测信号丢失警报通常是短暂的。

### <a name="cause"></a>原因

由于各种 Ambari 代理 bug，在极少数情况下，Ambari 代理可能具有很高的 CPU 利用率（接近 100%）。

### <a name="resolution"></a>解决方法

1. 确定 Ambari 代理的进程 ID (PID)：

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 然后，运行以下命令来显示 CPU 利用率：

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 重启 Ambari 代理以缓解问题：

    ```bash
    service ambari-agent restart
    ```

1. 如果重启不起作用，请终止 Ambari 代理进程，然后启动它：

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>方案：Ambari 代理未启动

### <a name="issue"></a>问题

Ambari 代理未启动，这会导致 Ambari UI 发出的针对某些节点的警报丢失 Ambari 代理检测信号。

### <a name="cause"></a>原因

这些警报是由未运行 Ambari 代理导致的。

### <a name="resolution"></a>解决方法

1. 确认 Ambari 代理的状态：

    ```bash
    service ambari-agent status
    ```

1. 确认故障转移控制器服务是否正在运行：

    ```bash
    ps -ef | grep failover
    ```

    如果故障转移控制器服务未运行，则很可能是由于某个问题导致 hdinsight 代理无法启动故障转移控制器。 通过 `/var/log/hdinsight-agent/hdinsight-agent.out` 文件检查 hdinsight 代理日志。

## <a name="scenario-heartbeat-lost-for-ambari"></a>方案：Ambari 的检测信号丢失

### <a name="issue"></a>问题

Ambari 检测信号代理已丢失。

### <a name="cause"></a>原因

OMS 日志导致 CPU 使用率高。

### <a name="resolution"></a>解决方法

* 使用 [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell cmdlet 禁用 Azure Monitor 日志记录。
* 删除 `mdsd.warn` 日志文件

---

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]