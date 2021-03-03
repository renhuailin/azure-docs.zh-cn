---
title: 如何在 Azure HDInsight 群集中使用 Apache Hive 复制
description: 了解如何在 HDInsight 群集中使用 Hive 复制来复制 Hive 元存储和 Azure Data Lake Storage Gen2 数据湖。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 2e57b7d8121ac0bd6fc9cb693750c41a0ef6a5bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733051"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>如何在 Azure HDInsight 群集中使用 Apache Hive 复制

在数据库和仓库的上下文中，复制是将实体从一个仓库复制到另一个仓库的过程。 复制可应用于整个数据库，也可应用于更小的级别（例如表或分区）。 目标是让一个副本在基本实体发生更改时进行更改。 Apache Hive 上的复制侧重于灾难恢复，提供单向的主副本复制。 在 HDInsight 群集中，可以使用 Hive 复制来单向复制 Hive 元存储和 Azure Data Lake Storage Gen2 上的关联基础数据湖。  

Hive 复制已经过多年的发展，更新的版本提供了更好的功能，速度更快，资源消耗更少。 在本文中，我们讨论在 HDInsight 3.6 和 HDInsight 4.0 群集类型中都支持的 Hive 复制 (Replv2)。

## <a name="advantages-of-replv2"></a>Replv2 的优点

[Hive ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) (也称为 Replv2) 与使用 Hive [导入/导出](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)的 hive 复制的第一个版本相比，具有以下优点：

- 基于事件的增量复制
- 时间点复制  
- 降低了带宽要求  
- 中间副本数量减少  
- 保留了复制状态
- 复制受到约束  
- 支持中心辐射型模型  
- 支持 ACID 表（在 HDInsight 4.0 中）

## <a name="replication-phases"></a>复制阶段

Hive 基于事件的复制是在主群集与辅助群集之间配置的。 此复制包括两个不同的阶段：启动和增量运行。

### <a name="bootstrapping"></a>启动

启动将运行一次，用于将数据库的基本状态从主数据库复制到辅助数据库。 如果需要，你可以对启动进行配置，以便在需要启用复制的目标数据库中包括表的子集。

### <a name="incremental-runs"></a>增量运行

在启动之后进行的增量运行在主群集上自动进行，在这些增量运行期间生成的事件会在辅助群集上播放。 当辅助群集赶上主群集的进度后，辅助群集的事件会与主群集的事件保持一致。

## <a name="replication-commands"></a>复制命令

Hive 提供了一组 REPL 命令（`DUMP`、`LOAD` 和 `STATUS`）来协调事件流。 `DUMP` 命令生成主群集上所有 DDL/DML 事件的本地日志。 `LOAD` 命令是一种将所记录的元数据和数据惰性复制到提取的复制转储输出中的方法，是在目标群集上执行的。 `STATUS` 命令从目标群集运行，用于提供表明已成功复制最新复制负载的最新事件 ID。

### <a name="set-replication-source"></a>设置复制源

在开始复制之前，请确保要复制的数据库已设置为复制源。 你可以使用 `DESC DATABASE EXTENDED <db_name>` 命令来确定是否为参数 `repl.source.for` 设置了策略名称。

如果已计划策略，但未设置 `repl.source.for` 参数，则需先使用 `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` 设置此参数。

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>将元数据转储到数据湖

在启动阶段使用 `REPL DUMP [database name]. => location / event_id` 命令将相关元数据转储到 Azure Data Lake Storage Gen2 中。 `event_id` 指定将与之相关的元数据置于 Azure Data Lake Storage Gen2 中的最小事件。 
 
```sql
repl dump tpcds_orc; 
```
示例输出：

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>将数据加载到目标群集

对于复制的启动和增量阶段，都使用 `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` 命令将数据加载到目标群集中。 `[database name]` 可以与源同名，也可以是目标群集上的另一名称。 `[location]` 表示之前的 `REPL DUMP` 命令的输出中的位置。 这意味着目标群集应该能够与源群集通信。 添加 `WITH` 子句主要是为了防止目标群集重启，以便进行复制。

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>输出最后一个复制的事件 ID

`REPL STATUS [database name]` 命令在目标群集上执行，并输出最后一个复制的 `event_id`。 用户还可以使用此命令了解已将其目标群集复制成什么状态。 可以使用此命令的输出来构造用于增量复制的下一个 `REPL DUMP` 命令。

```sql
repl status tpcds_orc;
```

示例输出：

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>将相关数据和元数据转储到数据湖

使用 `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` 命令将相关元数据和数据转储到 Azure Data Lake Storage 中。 此命令在增量阶段使用，在源仓库上运行。 对于增量阶段，`FROM [event-id]` 是必需的，`event-id` 的值可以通过在目标仓库上运行 `REPL STATUS [database name]` 命令来派生。

```sql
repl dump tpcds_orc from 2925;
```

示例输出：

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Hive 复制过程

以下步骤是在 Hive 复制过程中发生的顺序事件。

1. 确保要复制的表已设置为特定策略的复制源。

1. `REPL_DUMP` 命令将发布到主群集，其中包含关联的约束，例如数据库名称、事件 ID 范围和 Azure Data Lake Storage Gen2 存储 URL。

1. 系统将元存储中所有已跟踪事件的转储序列化为最新版本。 此转储存储在主群集上由 `REPL_DUMP` 指定的 URL 处的 Azure Data Lake Storage Gen2 存储帐户中。  

1. 主群集将复制元数据持久保存到主群集的 Azure Data Lake Storage Gen2 存储。 该路径可在 Ambari 的 Hive 配置 UI 中进行配置。 此过程提供元数据的存储路径，以及最新的已跟踪 DML/DDL 事件的 ID。

1. `REPL_LOAD` 命令是从辅助群集发出的。 此命令指向步骤 3 中配置的路径。

1. 辅助群集读取元数据文件，其中包含在第 3 步创建的已跟踪事件。 确保辅助群集具有到主群集的 Azure Data Lake Storage Gen2 存储（其中存储了来自 `REPL_DUMP` 的已跟踪事件）的网络连接。  

1. 辅助群集生成分布式复制 (`DistCP`) 计算。

1. 辅助群集从主群集的存储复制数据。  

1. 对辅助群集上的元存储进行更新。  

1. 将最后一个已跟踪事件 ID 存储在主元存储中。

增量复制遵循相同的过程，它需要最后一个复制的事件 ID 作为输入。 这会导致自上次复制事件以来的增量复制。 增量复制通常以预定的频率自动执行，以实现所需的恢复点目标 (RPO)。

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Hive 复制关系图":::

## <a name="replication-patterns"></a>复制模式  

复制通常配置为在主群集与辅助群集之间单向进行，在这种情况下，主群集用于满足读取和写入请求。 辅助群集仅用于满足读取请求。 如果发生灾难，则允许在辅助群集上执行写入，但需要配置到主群集的反向复制。

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Hive 复制模式":::

有许多适用于 Hive 复制的模式，包括“主 – 辅”模式、“中心辐射”模式和“中继”模式。

在 HDInsight 中，“主动主群集 – 备用辅助群集”是一种常见的业务连续性和灾难恢复 (BCDR) 模式，HiveReplicationV2 可通过 VNet 对等互连将此模式与区域分隔的 HDInsight Hadoop 群集配合使用。 可以使用对等互连到这两个群集的公用虚拟机来承载复制自动化脚本。 若要详细了解可能的 HDInsight BCDR 模式，请参阅 [HDInsight 业务连续性文档](../hdinsight-business-continuity.md)。  

### <a name="hive-replication-with-enterprise-security-package"></a>涉及企业安全性套餐的 Hive 复制  

如果在使用企业安全性套餐的 HDInsight Hadoop 群集上计划 Hive 复制，则必须考虑 Ranger 元存储和 Azure Active Directory 域服务 (AD DS) 的复制机制。  

使用 Azure AD DS 副本集功能跨多个区域为每个 Azure AD 租户创建多个 Azure AD DS 副本集。 每个单独的副本集需要在各自区域中通过 HDInsight VNet 进行对等互连。 在此配置中，对 Azure AD DS 进行的更改（包括配置、用户标识和凭据、组、组策略对象、计算机对象以及其他更改）会应用于使用 Azure AD DS 复制的托管域中的所有副本集。
  
可使用 Ranger 导入-导出功能定期备份 Ranger 策略并将其从主群集复制到辅助群集。 你可以选择复制所有或部分 Ranger 策略，具体取决于要在辅助群集上实现的授权级别。  

## <a name="sample-code"></a>代码示例  

下面的代码序列提供了一个示例，展示了如何在名为 `tpcds_orc` 的示例表上实现启动和增量复制。  

1. 将该表设置为复制策略的源。

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. 主群集上的启动转储。

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   示例输出：
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. 辅助群集上的启动负载。 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. 检查辅助群集上的 `REPL` 状态。

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. 主群集上的增量转储。

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   示例输出：
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. 辅助群集上的增量负载。  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. 检查辅助群集上的 `REPL` 状态。

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>后续步骤

若要详细了解本文中所述的项，请参阅：

- [Azure HDInsight 业务连续性](../hdinsight-business-continuity.md)
- [Azure HDInsight 业务连续性体系结构](../hdinsight-business-continuity-architecture.md)
- [Azure HDInsight 高度可用的解决方案体系结构案例研究](../hdinsight-high-availability-case-study.md)
- [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](../hadoop/hdinsight-use-hive.md)