---
title: 优化 Azure Data Lake Storage Gen2 性能 | Microsoft Docs
description: 了解如何优化 Azure Data Lake Storage Gen2 性能。 引入数据、创建数据集结构，等等。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3cb875f14fe3d9b18b5249a9d2cd9e1901000610
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609301"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>优化 Azure Data Lake Storage Gen2 性能

Azure Data Lake Storage Gen2 支持使用高吞吐量进行 I/O 密集型分析和数据移动。 本文可帮助你进行优化，以实现所需的吞吐量和高效数据访问。

> [!NOTE]
> 分析管道的总体性能还取决于特定于分析引擎的因素。 有关工作负载性能优化的最新指导，请参阅你打算使用的每个系统的文档。

## <a name="optimize-data-ingestion"></a>优化数据引入

从源系统引入数据时，源硬件、源网络硬件或与存储帐户的网络连接可能成为瓶颈。

![此图显示了将源系统中的数据引入到 Data Lake Storage Gen2 时要考虑的因素。](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

### <a name="source-hardware"></a>源硬件

无论使用的是本地计算机还是 Azure 中的虚拟机 (VM)，都请确保认真选择适当的硬件。 对于磁盘硬件，请考虑使用固态硬盘 (SSD)，并选择主轴速度较快的磁盘硬件。 对于网络硬件，请尽可能地使用最快的网络接口控制器 (NIC)。 在 Azure 上，我们建议使用 Azure D14 VM，该系列具有相当强大的磁盘和网络硬件。

### <a name="network-connectivity-to-the-storage-account"></a>与存储帐户的网络连接

源数据和存储帐户之间的网络连接有时可能成为瓶颈。 如果源数据位于本地，请考虑将专用链接和 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 结合使用。 如果源数据在 Azure 中，当数据与已启用 Data Lake Storage Gen2 的帐户位于同一 Azure 区域时，可获得最佳性能。

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>配置数据引入工具，实现最大并行化

若要实现最佳性能，请并行执行尽可能多的读取和写入，以使用所有可用吞吐量。

![Data Lake Storage Gen2 性能](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

下表汇总了几个热门引入工具的关键设置。

| 工具               | 设置 |
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (mapper) |
| [Azure 数据工厂](../../data-factory/copy-activity-performance.md) | parallelCopies    |
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m (mapper)   |

帐户可以缩放，以便为所有分析方案提供所需的吞吐量。 默认情况下，已启用 Data Lake Storage Gen2 的帐户在其默认配置中提供足够的吞吐量，以满足广泛类别用例的需要。 如果达到了默认限制，可以联系 [Azure 支持部门](https://azure.microsoft.com/support/faq/)配置帐户，以获得更多吞吐量。

## <a name="structure-your-data-set"></a>调整数据集结构

将数据存储在已启用 Data Lake Storage Gen2 的存储帐户中时，文件大小、文件数和文件夹结构会对性能产生影响。  以下部分介绍了这些方面的最佳做法。

### <a name="file-size"></a>文件大小

通常，HDInsight 等分析引擎在处理每个文件时都会产生开销，这涉及到列出内容、检查访问权限和执行各种元数据操作等任务。 如果将数据存储为多个小文件，这可能会对性能产生负面影响。 通常可将数据组织到较大文件中，以获得更佳性能（大小为 256MB 到 100GB）。 某些引擎和应用程序可能会在高效处理大于 100GB 的文件方面遇到问题。

有时，数据管道对原始数据（含有多个小文件）的控制有限。 通常，建议系统采用某种进程，将小文件聚合为较大的文件，以供下游应用程序使用。 如果你要实时处理数据，可将实时流式处理引擎（例如 [Azure 流分析](../../stream-analytics/stream-analytics-introduction.md)或 [Spark 流式处理](https://databricks.com/glossary/what-is-spark-streaming)）与消息代理（例如[事件中心](../../event-hubs/event-hubs-about.md)或 [Apache Kafka](https://kafka.apache.org/)）一起使用，以将数据作为较大的文件进行存储。

将小文件聚合为较大文件时，请考虑以读取优化格式（例如 [Apache Parquet](https://parquet.apache.org/)）保存这些大文件，供下游处理。 Apache Parquet 是一种开源文件格式，已针对读取密集型分析管道进行优化。 Parquet 的列式存储结构允许跳过不相关的数据。 查询效率将大幅提高，因为它们可以缩小从存储发送到分析引擎的数据的范围。 此外，由于类似的数据类型（适用于列）将一起存储，Parquet 支持高效的数据压缩和编码方案，这可以降低数据存储成本。 [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md)、[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 和 [Azure 数据工厂](../../data-factory/introduction.md)等服务具有利用 Parquet 文件格式的本机功能。

有关数据格式的详细信息，请参阅[最佳做法文章的数据格式部分](data-lake-storage-best-practices.md)。

### <a name="organizing-time-series-data-in-folders"></a>将时序数据组织到文件夹中

对于 Hive 工作负载，对时序数据进行分区修剪将有助于某些查询只读取一部分数据，从而提高性能。

引入时序数据的这些管道通常以非常结构化的方式对其文件和文件夹命名。 下面是一个极常见的示例，其中数据按日期进行了结构化：

\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

请注意，日期/时间信息同时显示为文件夹和文件名。

下方是日期和时间的一种常见模式

\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

同样，选择的文件夹和文件组织方式应针对更大的文件大小和每个文件夹中合理的文件数进行优化。

有关其他目录布局结构建议，请参阅[目录结构](data-lake-storage-best-practices.md#directory-layout-considerations)

### <a name="access-data-efficiently-with-query-acceleration"></a>使用查询加速来高效访问数据

查询加速使应用程序和分析框架可以通过仅检索执行给定操作所需的数据来大幅优化数据处理。 这减少了获取对存储数据的关键见解所需的时间和处理能力。

查询加速接受筛选“谓词”和“列投影”，使应用程序能够在从磁盘读取数据时筛选行和列。 只有满足谓词条件的数据才会通过网络传输到应用程序。 这会减少网络延迟和计算成本。

有关详细信息，请参阅 [Azure Data Lake Storage 查询加速](data-lake-storage-query-acceleration.md)

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>在 HDInsight 上优化 Hadoop 和 Spark 工作负荷中的 I/O 密集型作业

I/O 密集型作业大部分时间都在执行 I/O。  常见示例为，仅执行读取和写入操作的复制作业。  其他示例包括：读取大量数据、执行某些数据转换，然后将数据写回存储区的数据准备作业。  可以具有在单个操作中读取或写入高达 100MB 的作业，但是该大小的缓冲区可能会损害性能。 若要优化性能，请尝试使 I/O 操作的大小保持在 4MB 和 16MB 之间。

### <a name="io-intensive-jobs-with-hdinsight-clusters"></a>在 HDInsight 群集上执行 I/O 密集型作业

- HDInsight 版本。 为获得最佳性能，请使用最新版 HDInsight。
- 区域。 将 Data Lake Storage Gen2 帐户和 HDInsight 群集放置在同一区域。

HDInsight 群集由两个头节点和一些辅助角色节点组成。 每个辅助角色节点提供特定数量的核心和内存，具体取决于 VM 类型。  运行作业时，YARN 充当资源协商者，负责分配可用的内存和核心以创建容器。  每个容器运行完成作业所需的任务。  容器可并行运行以快速处理任务。 因此，通过并行运行尽可能多的容器可以提高性能。

可优化 HDInsight 群集中的以下 3 层，以增加容器数和使用所有可用的吞吐量。

- 物理层
- YARN 层
- 工作负荷层

### <a name="physical-layer"></a>物理层

运行具有更多节点和/或更大 VM 的群集。  更大的群集可运行更多 YARN 容器，如下图所示。

![此图显示了更大的群集如何使你能够运行更多 YARN 容器。](./media/data-lake-storage-performance-tuning-guidance/VM.png)

使用具有更多网络带宽的 VM。  如果网络带宽低于 Data Lake Storage Gen2 吞吐量，则网络带宽量可能成为瓶颈。  网络带宽大小因不同 VM 而异。  请选择具有可能的最大网络带宽的 VM 类型。

### <a name="yarn-layer"></a>YARN 层

使用较小的 YARN 容器。  缩减每个 YARN 容器的大小，创建更多包含相同数量资源的容器。

![此图显示了减小每个 YARN 容器以创建更多容器时的结果。](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

始终需要最小的 YARN 容器，具体取决于工作负荷。 如果选取的容器太小，作业会出现内存不足的问题。 YARN 容器通常不应小于 1 GB。 YARN 容器一般为 3 GB。 对于某些工作负荷，可能需要更大的 YARN 容器。

增加每个 YARN 容器的核心数。  增加分配给每个容器的核心数，以提高每个容器中运行的并行任务数。  这适用于每个容器运行多个任务的应用程序，例如 Spark。  对于在每个容器中运行单个线程的应用程序（如 Hive），最好分配多个容器，而不是为每个容器分配多个核心。

### <a name="workload-layer"></a>工作负荷层

使用所有可用的容器。  将任务数设置为等于或大于可用容器数，以便利用所有资源。

![此图显示了所有容器的使用情况。](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

失败的任务成本高昂。 如果每项任务都有大量数据需要处理，那么任务失败就会导致以高成本重试任务。  因此，最好创建多个任务，每个任务处理少量数据。

除了上述一般指导原则外，每个分析系统或框架还有不同的参数，你可以优化这些参数以获得最佳性能。 有关工作负载性能优化的最新指导，请参阅你打算使用的每个系统的文档。

## <a name="see-also"></a>另请参阅

- [使用 Azure Data Lake Storage Gen2 的最佳做法](data-lake-storage-best-practices.md)
- [Azure Data Lake Storage Gen2 概述](data-lake-storage-introduction.md)
