---
title: 优化群集顾问建议
titleSuffix: Azure HDInsight
description: 优化 Azure HDInsight 中的 Apache HBase 群集顾问建议。
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943019"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache HBase 建议

本文介绍了几条建议，可帮助你优化 Azure HDInsight 中的 Apache HBase 性能。 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>优化 HBase 以读取最近写入的数据

如果你的用例涉及读取来自 HBase 的最近写入的数据，则此建议可帮助到你。 对于高性能，最好从 memstore 而不是远程存储提供 HBase 读取。

查询建议指示，对于表中的给定列系列，应大于从 memstore 获取的 75% 的读取量。 此指示器表明，即使 memstore 上发生刷新，也需要访问最近的文件，并且这些文件需要放入缓存中。 首先将数据写入 memstore，系统会在其中访问最近数据。 内部 HBase 刷新线程可能会检测到给定区域已达到 128M（默认）大小，并可能触发刷新。 这种情况甚至发生在 memstore 大小约为 128M 时写入的最近数据上。 因此，稍后读取这些最近的记录可能需要读取文件，而不是从 memstore 读取。 因此最好优化，确保最近刷新的数据也可以驻留在缓存中。

若要优化缓存中的最新数据，请考虑以下配置设置：

1. 将 `hbase.rs.cacheblocksonwrite` 设置为 `true`。 HDInsight HBase 中的此默认配置为 `true`，因此请检查其是否未被重置为 `false`。

2. 增加 `hbase.hstore.compactionThreshold` 值，以避免开始时压缩。 默认情况下，此值为 `3`。 可以将其更改为一个更大的值，如 `10`。

3. 如果遵循步骤 2 并设置 compactionThreshold，则将 `hbase.hstore.compaction.max` 更改为较大的值（例如 `100`），并将配置 `hbase.hstore.blockingStoreFiles` 的值更改为更大的值（例如 `300`）。

4. 如果确定只需读取最近的数据，请将 `hbase.rs.cachecompactedblocksonwrite` 配置设置为“启用”。 此配置告知系统，即使发生压缩，数据仍保留在缓存中。 还可以在系列级设置配置。 

   在 HBase Shell 中，运行以下命令以设置 `hbase.rs.cachecompactedblocksonwrite` 配置：
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. 可以禁用表中给定系列的块缓存。 对于具有最近数据读取的系列，请确保该配置已设置为“启用”。 默认情况下，已为表中的所有系列启用块缓存。 如果已禁用系列的块缓存，并且需要将其启用，可使用 hbase shell 中的 alter 命令。

   这些配置有助于确保数据在缓存中可用，并且最近数据不会进行压缩。 如果你的场景中可以使用 TTL，请考虑使用日期分层压缩。 有关详细信息，请参阅 [Apache HBase 参考指南：日期分层压缩](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>优化刷新队列

此建议指示 HBase 刷新可能需要优化。 刷新处理程序的当前配置可能不够高，无法处理写入流量，这可能会导致刷新速度变慢。

在区域服务器 UI 中，请注意刷新队列是否增长到 100 以上。 此阈值表示刷新速度很慢，建议你调整 `hbase.hstore.flusher.count` 配置。 默认情况下，该值为 2。 请确保最大刷新线程数不超过 6。

此外，查看是否有区域计数优化方面的建议。 如果有，建议你尝试区域调优，看看这是否有助于提高刷新速度。 否则，请优化刷新线程。

## <a name="region-count-tuning"></a>区域计数优化

区域计数优化建议指示 HBase 已阻止更新，并且区域计数可能超过受支持的堆大小上限。 你可以优化堆大小、memstore 大小和区域计数。

下面是一个示例场景：

- 假设区域服务器的堆大小为 10 GB。 默认情况下，`hbase.hregion.memstore.flush.size` 为 `128M`。 `hbase.regionserver.global.memstore.size` 的默认值为 `0.4`。 这意味着在 10 GB 中，4 GB 分配给 memstore（全局）。

- 假设所有区域的写入负载分布均匀，并且每个区域只增加到 128 MB，则此设置中的最大区域数为 `32` 个区域。 如果给定的区域服务器配置为具有 32 个区域，则系统最好避免阻止更新。

- 设置到位后，区域数为 100。 4 GB 全局 memstore 现在拆分为 100 个区域。 因此，实际上每个区域只能获得 40 MB 的 memstore。 如果写入是统一的，系统会频繁刷新，并且顺序大小更小，小于 40 MB。 增加刷新线程数可以提高刷新速度 `hbase.hstore.flusher.count`。

该建议意味着，可以重新考虑每台服务器的区域数、堆大小和全局 memstore 大小配置以及刷新线程的优化，以避免更新被阻止。

## <a name="compaction-queue-tuning"></a>压缩队列优化

如果 HBase 压缩队列增长到 2000 以上并且定期发生，则可将压缩线程数更改为一个更大的值。

如果用于压缩的文件数量过多，可能会导致与文件和 Azure 文件系统的交互方式相关的更多堆占用。 因此，最好尽快完成压缩。 在较旧的群集中，有时与限制相关的压缩配置可能会导致压缩速率变慢。

查看配置 `hbase.hstore.compaction.throughput.lower.bound` 和 `hbase.hstore.compaction.throughput.higher.bound`。 如果它们已设置为 50M 和 100M，请保持原样。 但是，如果将这些设置配置为较低的值（较旧的群集就是这种情况），请将限制分别更改为 50M 和 100M。

配置 `hbase.regionserver.thread.compaction.small` 和 `hbase.regionserver.thread.compaction.large`（每个默认值为 1）。
此配置的最大值上限为小于 3。

## <a name="full-table-scan"></a>全表扫描

完整表扫描建议显示，发出的扫描中超过 75% 是完整表/区域扫描。 可重新考虑代码调用扫描的方式，以提高查询性能。 请考虑以下做法：

* 为每次扫描设置正确的开始和停止行。

* 使用 MultiRowRangeFilter API，以便你可以在一次扫描调用中查询不同的范围。 有关详细信息，请参阅 [MultiRowRangeFilter API 文档](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)。

* 如果需要完整表或区域扫描，请检查是否有可能避免这些查询使用缓存，以便使用缓存的其他查询可能不会逐出热块。 为确保扫描不使用缓存，需要使用代码中的“setCaching(false)”选项的扫描 API ： 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>后续步骤

[使用 Ambari 优化 Apache HBase](../optimize-hbase-ambari.md)
