---
title: 使用 DistCp 将数据复制到 Azure Data Lake Storage Gen2 | Microsoft Docs
description: 使用 Apache Hadoop 分布式复制工具 (DistCp) 在 Azure Data Lake Storage Gen2 之间复制数据。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 2a1e14e0c0ea8f2a58234d7f36b5dad14bd9968c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605461"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>使用 DistCp 在 Azure 存储 Blob 与 Data Lake Storage Gen2 之间复制数据

可以使用 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 在常规用途 V2 存储帐户与启用了分层命名空间的常规用途 V2 存储帐户之间复制数据。 本文提供如何使用 DistCp 工具的说明。

DistCp 提供了各种命令行参数，强烈建议你阅读本文以优化对 DistCp 的使用。 本文介绍了基本功能，同时重点介绍了如何使用 DistCp 将数据复制到支持分层命名空间的帐户。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
- 未启用 Data Lake Storage Gen2 功能（分层命名空间）的现有 Azure 存储帐户。
- 启用 Data Lake Storage Gen2 功能（分层命名空间）的 Azure 存储帐户。 有关如何创建 Azure 存储帐户的说明，请参阅[创建 Azure 存储帐户](../common/storage-account-create.md)
- 在已启用分层命名空间的存储帐户中创建的容器。
- 可以访问启用了分层命名空间功能的存储帐户的 Azure HDInsight 群集。 有关详细信息，请参阅[将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 请确保对该群集启用远程桌面。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>从 HDInsight Linux 群集使用 DistCp

HDInsight 群集附带 DistCp 实用工具，该实用工具可用于从不同源中复制数据到 HDInsight 群集。 如果 HDInsight 群集已配置为一起使用 Azure Blob 存储和 Azure Data Lake Storage，则可以立即使用 DistCp 实用工具在其间复制数据。 本部分介绍如何使用 DistCp 实用工具。

1. 在 HDI 群集中创建 SSH 会话。 有关详细信息，请参阅[连接到基于 Linux 的 HDInsight 群集](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 验证是否可以访问现有的常规用途 V2 帐户（未启用分层命名空间）。

    ```bash
    hdfs dfs -ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   输出应提供容器中内容的列表。

3. 同样，验证是否可从此群集访问启用分层命名空间的存储帐户。 运行以下命令：

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    输出会提供 Data Lake Storage 帐户中文件/文件夹的列表。

4. 使用 DistCp 从 WASB 将数据复制到 Data Lake Storage 帐户。

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    该命令会将 Blob 存储中 /example/data/gutenberg/ 文件夹的内容复制到 Data Lake Storage 帐户中的 /myfolder 。

5. 同样，使用 DistCp 从 Data Lake Storage 帐户将数据复制到 Blob 存储 (WASB)。

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    该命令会将 Data Lake Store 帐户中 /myfolder 的内容复制到 WASB 中的 /example/data/gutenberg/ 文件夹 。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 时的性能注意事项

由于 DistCp 的最小粒度是单个文件，设置同步复制的最大数目是针对 Data Lake Storage 对其进行优化的最重要参数。 同步复制的数目等于命令行上的映射器数 (m) 参数。 此参数指定用于复制数据的映射器的最大数目。 默认值为 20。

**示例**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>如何确定要使用的映射器数？

请参考下面的指导。

- **步骤 1：确定可用于“默认”YARN 应用队列的总内存** - 第一步是确定可用于“默认”YARN 应用队列的内存。 可在与群集关联的 Ambari 门户中获取此信息。 导航到 YARN 并查看“配置”选项卡可看到可用于“默认”应用队列的 YARN 内存。 这是 DistCp 作业（实际是 MapReduce 作业）的总可用内存。

- **步骤 2：计算映射器数** - **m** 的值等于总 YARN 内存除以 YARN 容器大小的商。 YARN 容器大小的信息也可在 Ambari 门户中找到。 导航到 YARN 并查看“配置”选项卡。YARN 容器大小显示在此窗口中。 用于得到映射器数 (**m**) 的公式是

    m = (number of nodes * YARN memory for each node) / YARN container size

**示例**

假设你有一个 4x D14v2s 群集，并且想要从 10 个不同的文件夹传输 10 TB 的数据。 每个文件夹都包含不同数量的数据，并且每个文件夹中的文件大小也不同。

- 总 YARN 内存：从 Ambari 门户确定一个 D14 节点的 YARN 内存为 96 GB。 因此，具有 4 个节点的群集的总 YARN 内存是：

    YARN memory = 4 * 96GB = 384GB

- 映射器数：从 Ambari 门户确定一个 D14 群集节点的 YARN 容器大小为 3,072 MB。 因此，映射器数为：

    m = (4 nodes * 96GB) / 3072MB = 128 mappers

如果其他应用程序正在使用内存，则可以选择仅将群集的部分 YARN 内存用于 DistCp。

### <a name="copying-large-datasets"></a>复制大型数据集

当要移动的数据集非常大（例如，大于 1 TB）时，或者如果有许多不同的文件夹，则应考虑使用多个 DistCp 作业。 可能没有任何性能提升，但它会展开作业，因此如果有任何作业失败，只需重启该特定作业（而不是整个作业）。

### <a name="limitations"></a>限制

- DistCp 会尝试创建大小类似的映射器以优化性能。 增加映射器数不一定始终会提高性能。

- DistCp 被限制为每个文件只有一个映射器。 因此，映射器数不应超过拥有的文件数。 由于 DistCp 只能将一个映射器分配给一个文件，这会限制可用于复制大型文件的并发数量。

- 如果有少量大型文件，则应将它们拆分为 256 MB 的文件块，以便提供更多潜在并发。
