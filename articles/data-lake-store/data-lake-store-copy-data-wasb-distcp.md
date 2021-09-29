---
title: 使用 DistCp 在 WASB 与 Azure Data Lake Storage Gen1 之间往来复制数据
description: 使用 DistCp 工具在 Azure 存储 Blob 与 Azure Data Lake Storage Gen1 之间往来复制数据
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: normesta
ms.openlocfilehash: 5e1f4cda87bbc73218826e76e3e11760c31dc159
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637427"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>使用 DistCp 在 Azure 存储 Blob 与 Azure Data Lake Storage Gen1 之间复制数据

> [!div class="op_single_selector"]
> * [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

如果你有可以访问 Azure Data Lake Storage Gen1 的 HDInsight 群集，则可以使用 DistCp 等 Hadoop 生态系统工具在 HDInsight 群集存储 (WASB) 与 Data Lake Storage Gen1 帐户之间往来复制数据。 本文介绍了如何使用 DistCp 工具。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)。
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>从 HDInsight Linux 群集使用 DistCp

HDInsight 群集附带了 DistCp 工具，它可用于将数据从不同的源复制到 HDInsight 群集中。 如果已经将 HDInsight 群集配置为使用 Data Lake Storage Gen1 作为额外的存储，则可以使用现成 Distcp 向/从 Data Lake Storage Gen1 帐户复制数据。 此部分介绍了如何使用 DistCp 工具。

1. 从桌面使用 SSH 连接到群集。 请参阅[连接到基于 Linux 的 HDInsight 群集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。 通过 SSH 提示符运行命令。

1. 验证能否访问 Azure 存储 Blob (WASB)。 运行以下命令：

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   输出列出了存储 blob 中的内容。

1. 同样，验证是否可从此群集访问 Data Lake Storage Gen1 帐户。 运行以下命令：

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    输出列出了 Data Lake Storage Gen1 帐户中的文件和文件夹。

1. 使用 DistCp 将数据从 WASB 复制到 Data Lake Storage Gen1 帐户。

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    该命令会将 WASB 中 /example/data/gutenberg/ 文件夹的内容复制到 Data Lake Storage Gen1 帐户中的 /myfolder。

1. 同样，使用 DistCp 将数据从 Data Lake Storage Gen1 帐户复制到 WASB。

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    此命令会将 Data Lake Storage Gen1 帐户中 /myfolder 的内容复制到 WASB 中的 /example/data/gutenberg/ 文件夹。

## <a name="performance-considerations-while-using-distcp"></a>使用 DistCp 时的性能注意事项

由于 DistCp 工具的最小粒度是单个文件，因此设置同步副本数量上限是针对 Data Lake Storage Gen1 进行优化的最重要参数。 可以通过在命令行上设置映射器数量 (m) 参数来控制同步副本数量。 此参数指定用于复制数据的映射器的最大数目。 默认值为 20。

示例：

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>如何确定要使用的映射器数量

请参考下面的指导。

* **步骤 1：确定总 YARN 内存** - 第一步是确定可供运行 DistCp 作业的群集使用的 YARN 内存。 可在与群集关联的 Ambari 门户中获取此信息。 转到 YARN 并查看“配置”选项卡，以查看 YARN 内存。 要获取总 YARN 内存，可将每个节点的 YARN 内存与你在群集中拥有的节点数相乘。

* **步骤 2：计算映射器数** - **m** 的值等于总 YARN 内存除以 YARN 容器大小的商。 YARN 容器大小信息也可以在 Ambari 门户中找到。 导航到 YARN 并查看“配置”选项卡。YARN 容器大小显示在此窗口中。 用于计算映射器数量 (m) 的公式是：

   `m = (number of nodes * YARN memory for each node) / YARN container size`

示例：

假设你在群集中有 4 个 D14v2s 节点，并且想要从 10 个不同的文件夹传输 10 TB 的数据。 每个文件夹都包含不同数量的数据，并且每个文件夹中的文件大小也不同。

* 总 YARN 内存 - 从 Ambari 门户确定一个 D14 节点的 YARN 内存为 96 GB。 因此，具有 4 个节点的群集的总 YARN 内存是：

   `YARN memory = 4 * 96GB = 384GB`

* 映射器数 - 从 Ambari 门户确定一个 D14 群集节点的 YARN 容器大小为 3072。 因此，映射器数量为：

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

如果其他应用程序正在使用内存，则你可以选择只对 DistCp 使用群集的一部分 YARN 内存。

### <a name="copying-large-datasets"></a>复制大型数据集

如果要移动的数据集非常大（例如，大于 1 TB），或者如果有许多不同的文件夹，请考虑使用多个 DistCp 作业。 这可能不会提高性能，但它会分散作业，因此如果有任何作业失败，你只需重启特定作业，而不需要重启整个作业。

### <a name="limitations"></a>限制

* DistCp 会尝试创建大小类似的映射器以优化性能。 增加映射器数不一定始终会提高性能。

* DistCp 被限制为每个文件只有一个映射器。 因此，映射器数量不应超过文件数量。 由于 DistCp 只能将一个映射器分配给一个文件，这限制了可用于复制大文件的并发量。

* 如果有少量的大文件，请将它们拆分为 256 MB 的文件块，以提供更多的潜在并发。

* 若要从 Azure Blob 存储帐户复制，那么你的复制作业可能会在 blob 存储端受到限制。 这会降低复制作业的性能。 若要详细了解 Azure Blob 存储限制，请查看 [Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)中的 Azure 存储限制。

## <a name="see-also"></a>另请参阅

* [将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
