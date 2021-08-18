---
title: 创建 Azure HDInsight - Azure Data Lake Storage Gen2 - 门户
description: 了解如何通过门户配合使用 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集。
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 372eb428c1ddde52cb557265052bc9fee27d7c83
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280398"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>通过 Azure 门户创建使用 Data Lake Storage Gen2 的群集

Azure 门户是一种基于 Web 的管理工具，用于管理 Microsoft Azure 云中托管的服务和资源。 本文介绍如何使用门户创建基于 Linux 的 Azure HDInsight 群集。 [创建 HDInsight 群集](./hdinsight-hadoop-provision-linux-clusters.md)一文提供了其他详细信息。

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要创建将 Data Lake Storage Gen2 用作存储的 HDInsight 群集，请按照以下步骤操作，配置具有分层命名空间的存储帐户。

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

创建用户分配的托管标识（如果还没有）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左上角，单击“创建资源”。
1. 在搜索框中键入“用户分配”并单击“用户分配的托管标识”。
1. 单击 **创建**。
1. 输入托管标识的名称，选择正确的订阅、资源组和位置。
1. 单击 **创建**。

有关 Azure HDInsight 中托管标识的工作原理的详细信息，请参阅 [Azure HDInsight 中的托管标识](hdinsight-managed-identities.md)。

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="创建用户分配的托管标识":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>创建与 Data Lake Storage Gen2 配合使用的存储帐户

创建与 Azure Data Lake Storage Gen2 配合使用的存储帐户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左上角，单击“创建资源”。
1. 在搜索框中，键入“存储”，然后单击“存储帐户” 。
1. 单击 **创建**。
1. 在“创建存储帐户”屏幕上：
    1. 选择正确的订阅和资源组。
    1. 为要与 Data Lake Storage Gen2 配合使用的存储帐户输入名称。
    1. 单击“高级”选项卡。
    1. 单击 **Data Lake Storage Gen2** 下的“分层命名空间”旁边的“启用”。
    1. 单击“查看 + 创建”。
    1. 单击“创建” 

有关存储帐户创建过程中其他选项的详细信息，请参阅[快速入门：为 Azure Data Lake Storage Gen2 创建存储帐户](../storage/blobs/create-data-lake-storage-account.md)。

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="显示 Azure 门户中存储帐户创建情况的屏幕截图":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>在 Data Lake Storage Gen2 上设置托管标识的权限

将托管标识分配到存储帐户上的“存储 Blob 数据所有者”角色。

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。
1. 选择存储帐户，然后选择“访问控制(IAM)”以显示该帐户的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="显示存储访问控制设置的屏幕截图":::

1. 选择“+ 添加角色分配”按钮以添加一个新角色。
1. 在“添加角色分配”窗口中，选择“存储 Blob 数据所有者”角色 。 然后，选择具有托管标识和存储帐户的订阅。 接下来，搜索并找到之前创建的用户分配托管标识。 最后，选择托管标识，它将在“选定成员”下列出。

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="显示如何分配 Azure 角色的屏幕截图":::

1. 选择“保存” 。 现在，选定的用户分配的标识会列在选定的角色下。
1. 此初始设置完成后，可通过门户创建群集。 群集必须与存储帐户位于同一 Azure 区域中。 在群集创建菜单的“存储”选项卡中，选择以下选项：

    * 对于“主要存储类型”，请选择“Azure Data Lake Storage Gen2” 。
    * 在“主存储帐户”下，搜索并选择新建的 Data Lake Storage Gen2 存储的存储帐户。

    * 在“标识”下，选择新建的用户分配的托管标识。

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="用于配合使用 Data Lake Storage Gen2 和 Azure HDInsight 的存储设置":::

    > [!NOTE]
    > * 若要在存储帐户级别向 Data Lake Storage Gen2 添加辅助存储帐户，只需将先前创建的托管标识分配给要添加的新 Data Lake Storage Gen2 即可。 请注意，不支持通过 HDInsight 上的“其他存储帐户”边栏选项卡向 Data Lake Storage Gen2 添加辅助存储帐户。
    > * 可以在 HDInsight 使用的 Azure Blob 存储帐户上启用 RA-GRS 或 RA-ZRS。 但是，不支持针对 RA-GRS 或 RA-ZRS 辅助终结点创建群集。

## <a name="delete-the-cluster"></a>删除群集

请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](./hdinsight-delete-cluster.md)。

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>后续步骤

你已成功创建 HDInsight 群集。 现在可以了解如何使用群集了。

### <a name="apache-spark-clusters"></a>Apache Spark 群集

* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)
* [使用 Scala 创建独立的应用程序](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)
