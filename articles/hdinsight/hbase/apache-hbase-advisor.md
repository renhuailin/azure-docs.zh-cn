---
title: 优化群集顾问建议
titleSuffix: Azure HDInsight
description: 针对 Azure HDInsight 中的群集顾问建议优化 Apache HBase。
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943019"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache HBase 建议

本文介绍了若干建议，可帮助你在 Azure HDInsight 中优化 Apache HBase 性能。 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>优化 HBase 以读取最近写入的数据

如果你的用例涉及到从 HBase 读取最近写入的数据，则这一建议可以为你带来帮助。 为了实现高性能，最佳做法是从 memstore 而不是远程存储提供 HBase 读取。

查询建议表明，对于表中的给定列系列 > 从 memstore 提供的75% 读取。 此指标表明即使 memstore 上发生刷新，也需要访问最近的文件，需要将其放入缓存中。 首先将数据写入到 memstore 系统访问那里的最新数据。 内部 HBase 惰性线程可能会检测到给定的区域已达到 128M (默认) 大小，并可触发刷新。 这种情况甚至是在 memstore 大约128M 时编写的最新数据。 因此，稍后读取这些最近记录可能需要读取文件而不是 memstore。 因此最好优化，甚至最近刷新的最近的数据也可以驻留在缓存中。

若要优化缓存中的最新数据，请考虑以下配置设置：

1. 将 `hbase.rs.cacheblocksonwrite` 设置为 `true`。 HDInsight HBase 中的此默认配置为 `true` ，因此检查是否不会将其重置为 `false` 。

2. 增大此 `hbase.hstore.compactionThreshold` 值，以便可以避免在中开始例行的压缩。 默认情况下，此值为 `3`。 可以将其增大到更大的值，如 `10` 。

3. 如果按照步骤2操作并设置 compactionThreshold，请将更改 `hbase.hstore.compaction.max` 为更高的值（例如 `100` ），并将配置的值增加 `hbase.hstore.blockingStoreFiles` 到更大的值（例如） `300` 。

4. 如果你确定只需要读取最新数据，请将 `hbase.rs.cachecompactedblocksonwrite` "配置" 设置为 **"开**"。 此配置告知系统即使发生压缩，数据仍保留在缓存中。 还可以在 "家族" 级别设置配置。 

   在 HBase Shell 中运行以下命令以设置 `hbase.rs.cachecompactedblocksonwrite` 配置：
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. 对于表中的给定系列，可以关闭块缓存。 确保为具有最新数据 **读取的系列** 启用此功能。 默认情况下，将为表中的所有系列启用块缓存。 如果已禁用系列的块缓存并需要将其打开，请使用 hbase shell 中的 alter 命令。

   这些配置有助于确保数据在缓存中可用，并确保最近的数据不会进行压缩。 如果在你的方案中可以使用 TTL，则考虑使用日期分层压缩。 有关详细信息，请参阅 [Apache HBase 参考指南：日期分层压缩](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>优化刷新队列

此通报表明 HBase 刷新可能需要优化。 刷新处理程序的当前配置可能不够高，无法处理写入流量，这可能会导致刷新速度变慢。

在区域服务器 UI 中，注意刷新队列是否增长到100以上。 此阈值表示刷新速度较慢，你可能必须优化   `hbase.hstore.flusher.count` 配置。 默认情况下，值为2。 确保最大惰性线程数不超过6。

此外，请查看是否有对区域计数优化的建议。 如果是，我们建议您尝试区域调整，以查看是否有助于更快地刷新。 否则，优化惰性线程可能会有所帮助。

## <a name="region-count-tuning"></a>区域计数优化

区域计数优化建议表明 HBase 已阻止更新，并且区域计数可能大于支持的最佳堆大小。 可以调整堆大小、memstore 大小和区域计数。

作为示例方案：

- 假设区域服务器的堆大小为 10 GB。 默认情况下， `hbase.hregion.memstore.flush.size` 为 `128M` 。 `hbase.regionserver.global.memstore.size` 的默认值为 `0.4`。 这意味着，对于 memstore，将在全局) 为 (分配 4 GB 的内存。

- 假设在所有区域上都有一个写入负载分布，并且假设每个区域仅增长到 128 MB，则此设置中的最大区域数为 `32` 区域。 如果给定的区域服务器配置为具有32个区域，则系统可以更好地避免阻止更新。

- 设置好这些设置后，区域数为100。 4 GB 的全局 memstore 现在跨100个区域拆分。 因此，对于 memstore，每个区域有效只获得 40 MB。 如果写入是统一的，则系统会频繁地刷新并减小顺序 < 40 MB。 具有很多惰性线程可能会提高刷新速度 `hbase.hstore.flusher.count` 。

这一建议意味着，最好重新考虑每个服务器的区域数、堆大小和全局 memstore 大小配置，同时调整刷新线程以避免更新被阻止。

## <a name="compaction-queue-tuning"></a>压缩队列优化

如果 HBase 压缩队列增长到超过2000并且定期发生，则可以将压缩线程增加到更大的值。

如果有大量的压缩文件，则可能会导致更多与文件与 Azure 文件系统交互的方式相关的堆使用情况。 因此最好尽快完成压缩。 在较旧的群集中，一些时间与限制相关的压缩配置可能会导致压缩速度降低。

检查配置 `hbase.hstore.compaction.throughput.lower.bound` 和 `hbase.hstore.compaction.throughput.higher.bound` 。 如果已将其设置为50M 和100M，请将其保留原样。 但是，如果将这些设置配置为较小的值 (这是较旧的群集) 的情况，请将限制分别更改为50M 和100M。

配置为 `hbase.regionserver.thread.compaction.small` ， `hbase.regionserver.thread.compaction.large` 每)  (默认值为1。
上限此配置的最大值小于3。

## <a name="full-table-scan"></a>全表扫描

全表扫描公告表明，超过75% 的扫描是全表/区域扫描。 您可以重新访问代码调用扫描的方式以提高查询性能。 请考虑以下做法：

* 为每个扫描设置正确的开始和结束行。

* 使用 **MultiRowRangeFilter** API，以便可以在一个扫描调用中查询不同的范围。 有关详细信息，请参阅 [MULTIROWRANGEFILTER API 文档](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)。

* 如果需要完整的表或区域扫描，请检查是否有可能避免对这些查询使用缓存，以便使用缓存的其他查询可能不会逐出处于热状态的块。 若要确保扫描不使用缓存，请在代码中使用带有 **setCaching (false)** 选项的 **scan** API： 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>后续步骤

[使用 Ambari 优化 Apache HBase](../optimize-hbase-ambari.md)
