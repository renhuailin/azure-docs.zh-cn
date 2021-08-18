---
title: 本地 HDFS 在 Azure HDInsight 群集上的安全模式下停止响应
description: 排查本地 Apache HDFS 在 Azure HDInsight 中的 Apache 群集上的安全模式下停止响应的问题
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 6764b2c1b8b2a06f2bd9264a7461f43986f557ad
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299962"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>场景：本地 HDFS 在 Azure HDInsight 群集上的安全模式下停止响应

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

本地 Apache Hadoop 分布式文件系统 (HDFS) 在 HDInsight 群集上的安全模式下停止响应。 收到如下错误消息：

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>原因

HDInsight 群集已纵向缩减为以下非常少的节点，或者节点数接近 HDFS 复制因子。

## <a name="resolution"></a>解决方法

1. 使用以下命令报告 HDInsight 群集上的 HDFS 状态：

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. 使用以下命令检查 HDInsight 群集上的 HDFS 完整性：

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. 如果确定没有缺失、损坏或正在复制的块，或者这些块可以忽略，请运行以下命令使名称节点退出安全模式：

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]