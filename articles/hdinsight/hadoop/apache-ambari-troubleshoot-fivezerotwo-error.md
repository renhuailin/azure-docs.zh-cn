---
title: Azure HDInsight 中的 Apache Ambari UI 502 错误
description: 尝试访问 Azure HDInsight 群集时出现 Apache Ambari UI 502 错误
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: da396873fe91777130c407c2b679b0192141cae1
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291414"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>场景：Azure HDInsight 中的 Apache Ambari UI 502 错误

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

在尝试访问 HDInsight 群集的 Apache Ambari UI 时，收到如下类似消息：“502 - Web 服务器在充当网关或代理服务器时收到了无效响应”。

## <a name="cause"></a>原因

通常，HTTP 502 状态代码表示 Ambari 服务器未在活动头节点上正常运行。 此问题有多种可能的根本原因。

## <a name="resolution"></a>解决方法

在大多数情况下，若要缓解此问题，可以重启活动头节点。 或者，为头节点选择更大的 VM 大小。

### <a name="ambari-server-failed-to-start"></a>Ambari 服务器无法启动

可以检查 ambari-server 日志，以找出 Ambari 服务器无法启动的原因。 一个常见原因是数据库一致性检查出错。 可在以下日志文件中找到相关信息：`/var/log/ambari-server/ambari-server-check-database.log`。

如果对群集节点进行了任何修改，请撤消修改。 始终使用 Ambari UI 来修改任何 Hadoop/Spark 相关的配置。

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 服务器的 CPU 使用率达到了 100%

在极少数情况下，我们会看到 ambari-server 进程的 CPU 利用率持续接近 100%。 作为缓解措施，可以通过 SSH 连接到活动头节点，终止 Ambari 服务器进程，然后将其重启。

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>oom-killer 终止的 Ambari 服务器

在某些情况下，头节点会耗尽内存，此时，Linux oom-killer 将会启动，以选取要终止的进程。 可以通过搜索 AmbariServer 进程 ID（应该找不到它）来验证此情况。 然后查找 `/var/log/syslog`，并查看如下所示的内容：

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

然后确定哪些进程占用了内存，并尝试进一步查找根本原因。

### <a name="other-issues-with-ambari-server"></a>Ambari 服务器的其他问题

在极少的情况下，Ambari 服务器无法处理传入的请求，你可以通过查看 ambari-server 日志中的任何错误来了解详细信息。 此类情况的一个示例是出现如下所示的错误：

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]