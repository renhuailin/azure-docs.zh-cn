---
title: Azure HDInsight 磁盘已满，无法启用代理
description: Apache Kafka 因磁盘已满而无法启动代理进程的故障排除步骤。
ms.service: hdinsight
ms.topic: troubleshooting
author: Jacky-hdi
ms.author: linjzhu
ms.date: 10/11/2021
ms.openlocfilehash: 3e7d113da1c086c0a95916dd29c9149beb897862
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859567"
---
# <a name="scenario-brokers-are-unhealthy-or-cant-restart-due-to-disk-space-full-issue"></a>场景：由于磁盘空间已满，代理运行不正常或无法重启

本文介绍在 Azure HDInsight 群集中使用 Apache Kafka 遇到问题时的故障排除步骤及可行的解决方案。

## <a name="issue"></a>问题

Azure HDInsight 群集中 Apache Kafka 代理使用的数据磁盘可能已满。 发生这种情况时，Apache Kafka 代理进程将因磁盘已满错误而无法启动并出现故障。 如果最近更改了任何配置，则这些更改不会生效，因为 Kafka 代理进程并未启动。

## <a name="cause"></a>原因

这种延迟可能是由以下一个或多个原因所致：

- 创建 Kafka 群集后，无法增加磁盘数或增加磁盘大小。
- 通常可以在 Apache Ambari UI 中配置磁盘警报。 每当磁盘使用量超过 60% 时，会有警报通知你必须横向扩展或减少 Kafka 群集中的日志数。
- 此问题只会在你忽略磁盘警报时发生。 可以横向扩展群集，留出更多可用空间来响应磁盘空间警报。
- 即使保留时间已过，消息也不会立即删除。 要删除的消息将标记为“删除”。 后台清理进程将在稍后删除消息。 仅删除被动段中的消息。

> [!IMPORTANT]
> 可以使用配置来增强日志清理器性能，但请谨慎应用此增强功能，因为它可能会影响消息的生成或使用。

## <a name="mitigation"></a>缓解措施

若要缓解问题：

1. 检查 `server.properties` 文件以查找每个主题的保留时间。 有时，日志保留策略是在主题级别设置。 若要查找在主题级别配置的保留时间，请运行以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper <zookeeper-list>
    ```

2. 获得该信息后，请检查有哪些繁重的分区占用了最大磁盘空间。

    ```bash
    # Command to sort the directories based on size:
    du -hs * | sort -rh | head -5 
    ```

3. 备份超过新保留时间的文件。

4. 当有一些空间可用时，可以重启代理以使用新的保留时间配置。 重新启动代理会清理较旧的日志，并释放磁盘上的一些空间。

   > [!IMPORTANT]
   > 有时，由于 OS 磁盘可能已满，或者会重载其他 Kafka 磁盘，因此可能无法进行备份。 在这种情况下，可能需要删除超过保留时间的文件。

## <a name="resolution"></a>解决方法

尽管可以缩短保留时间，但如果想在群集中添加更多主题或者如果引入的加载或数据量增加，则配置将无法缩放。

若要避免这些情况，请考虑使用下列选项之一：

- 如果分区太大，可以增加最繁重主题的分区数。

  > [!NOTE]
  > 增加现有主题中的分区不会重新整理主题中的数据。 必须先将数据从旧的低分区主题手动复制到新的高分区主题。 

- 横向扩展群集，并跨多个磁盘重新平衡所有分区。

- 创建附加了更大 SKU 和更多磁盘的群集。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
