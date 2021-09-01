---
title: 使用 PowerShell 管理 Apache Hadoop 群集 - Azure HDInsight
description: 了解如何使用 Azure PowerShell 针对 HDInsight 中的 Apache Hadoop 群集执行管理任务。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive, devx-track-azurepowershell
ms.date: 02/13/2020
ms.openlocfilehash: 8855c1004406d8db3b34fe442c9c764293df5119
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "122653112"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 HDInsight 中的 Apache Hadoop 群集

[!INCLUDE [selector](includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 可用于在 Azure 中控制和自动执行工作负荷的部署和管理。 本文介绍了如何使用 Azure PowerShell Az 模块管理 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 群集。 有关 HDInsight PowerShell cmdlet 的列表，请查看 [Az.HDInsight 参考](/powershell/module/az.hdinsight)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

已安装 PowerShell [Az 模块](/powershell/azure/)。

## <a name="create-clusters"></a>创建群集

请参阅[使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>列出群集

使用以下命令可列出当前订阅中的所有群集：

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>显示群集

使用以下命令可显示当前订阅中特定群集的详细信息：

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>删除群集

使用以下命令来删除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

还可以通过删除包含群集的资源组来删除群集。 删除资源群将删除组中的所有资源（包括默认存储帐户）。

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>缩放群集

使用群集缩放功能，可更改 Azure HDInsight 中运行的群集使用的辅助节点数，而无需重新创建群集。 若要使用 Azure PowerShell 更改 Hadoop 群集大小，请从客户端计算机运行以下命令：

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 有关缩放群集的详细信息，请参阅[缩放 HDInsight 群集](./hdinsight-scaling-best-practices.md)。

## <a name="update-http-user-credentials"></a>更新 HTTP 用户凭据

[Set-AzHDInsightGatewayCredential](/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) 设置 Azure HDInsight 群集的网关 HTTP 凭据。

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>查找默认存储帐户

以下 PowerShell 脚本演示了如何获取群集的默认存储帐户名称和相关信息：

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>查找资源组

在 Resource Manager 模式下，每个 HDInsight 群集都属于一个 Azure 资源组。  若要查找资源组：

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>提交作业

**提交 MapReduce 作业**

请参阅[运行 HDInsight 随附的 MapReduce 示例](hadoop/apache-hadoop-run-samples-linux.md)。

**提交 Apache Hive 作业**

请参阅[使用 PowerShell 运行 Apache Hive 查询](hadoop/apache-hadoop-use-hive-powershell.md)。

**提交 Apache Sqoop 作业**

请参阅[将 Apache Sqoop 与 HDInsight 配合使用](hadoop/hdinsight-use-sqoop.md)。

**提交 Apache Oozie 作业**

请参阅[在 HDInsight 中将 Apache Oozie 与 Apache Hadoop 配合使用以定义和运行工作流](hdinsight-use-oozie-linux-mac.md)。

## <a name="upload-data-to-azure-blob-storage"></a>将数据上传到 Azure Blob 存储

请参阅[将数据上传到 HDInsight](hdinsight-upload-data.md)。

## <a name="see-also"></a>另请参阅

* [Az.HDInsight cmdlet](/powershell/module/az.hdinsight/#hdinsight)
* [使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-portal-linux.md)
* [使用命令行接口管理 HDInsight](hdinsight-administer-use-command-line.md)
* [创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
* [以编程方式提交 Apache Hadoop 作业](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
