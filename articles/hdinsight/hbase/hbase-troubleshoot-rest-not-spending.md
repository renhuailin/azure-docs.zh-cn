---
title: Azure HDInsight 中的 Apache HBase REST 未响应请求
description: 解决了 Azure HDInsight 中的 Apache HBase REST 未响应请求的问题。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 0315ced51a57224113e7af490f15bcf9db894738
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291306"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>场景：Azure HDInsight 中的 Apache HBase REST 未响应请求

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Azure HDInsight 中的 Apache HBase REST 服务未响应请求。

## <a name="cause"></a>原因

此处的可能原因是 Apache HBase REST 服务正在泄漏套接字，这种情况在服务长时间运行（例如运行了几个月）时尤其常见。 在客户端 SDK 中，可以看到如下所示的错误消息：

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>解决方法

通过 SSH 连接到主机后，使用以下命令重启 HBase REST。 也可以使用脚本操作在所有工作器节点上重启此服务：

```bash
sudo service hdinsight-hbrest restart
```

此命令将停止同一主机上的 HBase 区域服务器。 可以通过 Ambari 手动启动 HBase 区域服务器，或者让 Ambari 的自动重启功能自动恢复 HBase 区域服务器。

如果此问题仍旧出现，你可以安装以下缓解脚本 - 每隔 5 分钟在每个工作器节点上运行的 CRON 作业。 此缓解脚本将 ping REST 服务，并在 REST 服务无响应时将它重启。

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]