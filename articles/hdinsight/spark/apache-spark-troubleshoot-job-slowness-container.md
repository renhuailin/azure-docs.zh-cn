---
title: 当 Azure HDInsight 存储包含许多文件时，Apache Spark 运行速度缓慢
description: 当 Azure 存储容器包含 Azure HDInsight 中的许多文件时，Apache Spark 作业运行速度缓慢
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: 21fd9d14b2d289a3cf652b553891cc8bdf0d616b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282216"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>当 Azure 存储容器包含 Azure HDInsight 中的许多文件时，Apache Spark 作业运行速度缓慢

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

运行 HDInsight 群集时，如果存在多个文件/子文件夹，写入 Azure 存储容器的 Apache Spark 作业将很缓慢。 例如，写入新容器只需 20 秒，而写入包含 20 万个文件的容器却需要大约 2 分钟。

## <a name="cause"></a>原因

这是一个已知的 Spark 问题。 速度变慢的原因在于执行 Spark 作业期间的 `ListBlob` 和 `GetBlobProperties` 操作。

为了跟踪分区，Spark 必须维护一个 `FileStatusCache`，其中包含有关目录结构的信息。 使用此缓存，Spark 可以分析路径并识别可用分区。 跟踪分区的好处是，Spark 只在读取数据时才处理必要的文件。 为了将此信息保持最新状态，当你写入新数据时，Spark 必须列出目录下的所有文件，并更新此缓存。

在 Spark 2.1 中，尽管不需要在每次写入后更新缓存，但 Spark 会检查现有分区列是否与当前写入请求中的建议分区列匹配，因此它也会在每次写入时导致发生列出操作。

在 Spark 2.2 中，使用追加模式写入数据时，此性能问题应可解决。

在 Spark 2.3 中，预期会出现与 Spark 2.2 相同的行为。

## <a name="resolution"></a>解决方法

创建分区数据集时必须使用分区方案来更新 `FileStatusCache`，该方案用于限制 Spark 必须列出的文件数。

对于 N% 100 == 0（100 只是一个示例）的每第 N 个微批，请将现有数据移到可由 Spark 加载的另一个目录。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
