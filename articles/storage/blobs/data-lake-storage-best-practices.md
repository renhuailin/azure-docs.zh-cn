---
title: 使用 Azure Data Lake Storage Gen2 的最佳做法 | Microsoft Docs
description: 了解与 Azure Data Lake Storage Gen2 用法相关的数据引入、日期安全性和性能的最佳做法
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: b2d631d165b4a8cd585fbe012d9ab9afaa2e6bf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555425"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>使用 Azure Data Lake Storage Gen2 的最佳做法

本文提供有关已启用 Data Lake Storage Gen2 的存储帐户中的安全性、复原能力和目录结构的最佳做法建议。 有关性能优化的最佳做法建议，请参阅[优化 Azure Data Lake Storage Gen2 性能](data-lake-storage-performance-tuning-guidance.md)。

## <a name="security-considerations"></a>安全注意事项

### <a name="use-security-groups-instead-of-individual-users"></a>使用安全组而不是单个用户

应用访问控制列表 (ACL) 时，请始终使用 Azure AD 安全组作为 ACL 条目中分配的主体。 拒绝直接分配各个用户或服务主体。 使用此结构，你可以添加和删除用户或服务主体，不需要向整个目录结构重新应用 ACL。 可以仅在适当的 Azure AD 安全组中添加或删除用户和服务主体。

有关应用此最佳做法的详细信息，请参阅[安全组](data-lake-storage-access-control-model.md#security-groups)。

有关 Data Lake Storage Gen2 访问控制模型的一般信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)。

### <a name="configure-the-azure-storage-firewall-with-azure-service-access"></a>将 Azure 服务访问权限配置到 Azure 存储防火墙中

启用 Azure 存储防火墙以限制外部攻击途径。 若要从 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 等服务访问存储帐户，请将该服务的实例部署到虚拟网络。 然后，可以配置防火墙，以便为该服务授予对存储帐户的访问权限。

有关如何应用此最佳做法的详细信息，请参阅[向受信任的 Azure 服务授予访问权限](../common/storage-network-security.md)。

有关一般安全建议，请参阅 [Blob 存储的安全建议](security-recommendations.md)

## <a name="resiliency-considerations"></a>复原注意事项

在设计包含 Data Lake Storage Gen2 或任何云服务的系统时，请考虑到可用性要求以及如何响应可能出现的服务中断情况。 问题可能局限于特定的实例，也可能是区域性的，因此必须对二者都有计划。 可以选择略微激进的策略来确保高可用性和灾难恢复，具体取决于工作负载的恢复时间目标和恢复点目标服务级别协议。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复

Data Lake Storage Gen2 已经可以处理 3 个副本形式的复制，来应对局部硬件故障。 区域冗余存储 (ZRS) 和异地区域冗余存储 (GZRS) 等复制选项可以提高可用性。 使用这些功能，工作负载可以通过切换到本地或新区域中单独复制的实例来响应服务中断。

若要针对区域灾难性故障做好准备，请确保使用异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS) 等复制选项将数据复制到不同的区域。 另外，请考虑到在出现数据损坏这样的极端情况时的需求 - 对于这种极端情况，你可能希望创建可供回退的定期快照。 根据数据的重要性和大小，可以考虑每隔 1 小时、6 小时、24 小时创建滚动性的增量快照，具体取决于风险承受能力。

除了选择适当的复制模型外，还应考虑通过各种方式来帮助连接方客户端自动故障转移到次要区域：通过监视触发器、失败尝试的时长，或者通知管理员进行人工干预。 请注意，是进行故障转移，还是等待服务重新联机？这需要进行慎重的权衡。

### <a name="use-distcp-for-data-movement-between-two-locations"></a>使用 Distcp 在两个位置之间进行数据移动

DistCp 是 distributed copy（分布式复制）的简称，是 Hadoop 随附的一个 Linux 命令行工具，适用于在两个位置之间进行分布式数据移动。 这两个位置可以是 Data Lake Storage Gen2、Hadoop 分布式文件系统 (HDFS) 或 S3。 此工具使用 Hadoop 群集（例如 HDInsight）上的 MapReduce 作业在所有节点上进行横向扩展。 Distcp 被认为是在没有特殊网络压缩设备的情况下移动大数据的最快方式之一。 Distcp 还提供了在两个位置之间仅更新增量数据的选项，可以处理自动重试，并且可以对计算进行动态缩放。 如果需要复制 Hive/Spark 表之类的项（在单个目录中可能有许多大型文件），而且只需要对修改的数据进行复制，则此方法相当有效。 由于这些原因，在大数据存储之间复制数据时，最建议使用的工具是 Distcp。

复制作业可以通过使用频率或数据触发器的 Apache Oozie 工作流触发，也可以通过 Linux cron 作业触发。 对于密集型复制作业，我们建议启动一个单独的 HDInsight Hadoop 群集，该群集可以专门针对复制作业进行优化和缩放。 这样可确保复制作业不会干扰关键作业。 如果运行复制的频率足够宽，甚至可以在每次作业之间关闭群集。 如果故障转移到次要区域，请确保在次要区域也启动另一群集，以便在主要的 Data Lake Storage Gen2 帐户恢复后将新数据复制回该帐户。 有关如何使用 Distcp 的示例，请参阅[使用 Distcp 在 Azure 存储 Blob 和 Data Lake Storage Gen2 之间复制数据](../blobs/data-lake-storage-use-distcp.md)。

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>使用 Azure 数据工厂来计划复制作业

[Azure 数据工厂](../../data-factory/introduction.md)还可以用来通过复制活动对复制作业进行计划，甚至可以通过复制向导设置为按某个频率执行复制作业。 请记住，Azure 数据工厂的云数据移动单位 (DMU) 有限，因此最终会对大数据工作负荷的吞吐量/计算进行限制。 另外，Azure 数据工厂目前不提供在 Data Lake Storage Gen2 帐户之间进行增量更新的功能，因此 Hive 表之类的目录需要获得完整的副本才能进行复制。 有关如何使用数据工厂进行复制的详细信息，请参阅[数据工厂文章](../../data-factory/load-azure-data-lake-storage-gen2.md)。

## <a name="monitoring-considerations"></a>监视注意事项

Data Lake Storage Gen2 提供了一些指标。这些指标可以在 Azure 门户的 Data Lake Storage Gen2 帐户中使用，也可以在 Azure Monitor 中使用。 Data Lake Storage Gen2 的可用性显示在 Azure 门户中。 若要获取 Data Lake Storage Gen2 帐户的最新可用性，必须运行你自己的综合性测试来验证可用性。 其他指标（例如总存储使用率、读/写请求数、入口/出口）可供监视应用程序使用，还可以在超出阈值（例如平均延迟时间或每分钟错误数）时触发警报。

## <a name="directory-layout-considerations"></a>目录布局注意事项

引入数据时，必须对数据结构进行预先规划，以便有效地利用安全性、分区和处理。 以下许多建议适用于所有大数据工作负荷。 每个工作负载对数据使用方式的要求各不相同，不过下面是一些在使用物联网 (IoT) 和批处理方案时要考虑的常见布局。

### <a name="iot-structure"></a>IoT 结构

在 IoT 工作负载中，可能会引入大量来自多个产品、设备、组织和客户的数据。 必须预先规划目录布局，以便对下游使用者的数据进行组织，确保其安全性并提高处理效率。 考虑使用的常规模板可能是以下布局：

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

例如，在英国，飞机引擎的着陆遥测数据可能看起来像以下结构：

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

在此示例中，通过将日期放在目录结构的末尾，你可以使用 ACL 更轻松地保护区域和主题，使其仅供特定用户和组访问。 如果将数据结构放在开头，则保护这些区域和主题会困难得多。 例如，如果你只想提供对英国数据或特定航班的访问，则需要针对每个小时目录下的大量目录应用单独的权限。 随着时间的推移，此结构还会导致目录数量呈指数级增加。

### <a name="batch-jobs-structure"></a>批处理作业结构

批处理中常用的方法是将数据放入“in”目录。 然后，当数据处理完以后，再将新数据置于“out”目录中，供下游进程使用。 有的作业需要对单个文件进行处理，但可能不需要对大型数据集进行大规模并行处理。对于这种作业，有时会使用此目录结构。 与上面建议的 IoT 结构一样，好的目录结构会设置父级目录，用于存储区域和主题之类的内容（例如，组织、产品或制造者）。 考虑在结构中添加日期和时间，以便更好地在处理过程中进行组织、筛选式搜索，增强安全性并提高自动化程度。 日期结构的粒度级别取决于上传或处理数据的时间间隔，例如每小时、每天甚至每月。

有时候，数据损坏或格式异常会导致文件处理失败。 在这种情况下，可以将这些文件移到 /bad 文件夹中进一步进行检查，这样的目录结构更有效。 还可以通过批处理作业将这些损坏的文件报告或通知给相关人员，要求其进行人工干预。 请考虑以下模板结构：

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

例如，一家市场营销公司每天从其北美的客户端提取客户更新数据。 该数据在处理前和处理后可能看起来像以下片段：

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

在常见情况下，批数据在处理后会直接进入 Hive 之类的数据库或传统的 SQL 数据库，不需要 /in 或 /out 目录，因为输出已经进入一个适用于 Hive 表或外部数据库的单独文件夹中 。 例如，每日从客户提取的数据将会进入其各自对应的目录中。 然后，[Azure 数据工厂](../../data-factory/introduction.md)、[Apache Oozie](https://oozie.apache.org/) 或 [Apache Airflow](https://airflow.apache.org/) 等服务将触发每日 Hive 或 Spark 作业来处理数据并将其写入 Hive 表中。

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)
- [优化 Azure Data Lake Storage Gen2 性能](data-lake-storage-performance-tuning-guidance.md)
- [Data Lake 漫游指南](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Azure Data Lake Storage Gen2 概述](data-lake-storage-introduction.md)
