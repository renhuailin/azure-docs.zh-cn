---
title: 使用 Azure Monitor 日志监视 Azure HDInsight 群集
description: 了解如何使用 Azure Monitor 日志监视在 HDInsight 群集中运行的作业。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurepowershell, references_regions
ms.date: 08/02/2021
ms.openlocfilehash: 0627cbb6c590178c5f393cfd519fb4a4504d050f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180489"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>使用 Azure Monitor 日志监视 HDInsight 群集

了解如何启用 Azure Monitor 日志监视 HDInsight 中的 Hadoop 群集操作。 以及如何添加 HDInsight 监视解决方案。

[Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md) 是用来监视云和本地环境的 Azure Monitor 服务。 该监视服务用于维持上述环境的可用性和性能。 它将收集云、本地环境和其他监视工具中由资源生成的数据。 而数据用于提供跨多个源的分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

#### <a name="new-azure-monitor-experience"></a>[新的 Azure Monitor 体验](#tab/new)

> [!Important]
> 新的 Azure Monitor 体验仅在美国东部和西欧作为预览版功能提供。  
>

## <a name="prerequisites"></a>先决条件

* Log Analytics 工作区。 可将此工作区视为独特的 Azure Monitor 日志环境，包含自身的数据存储库、数据源和解决方案。 有关说明，请参阅[创建 Log Analytics 工作区](../azure-monitor/vm/monitor-virtual-machine.md)。

* 一个 Azure HDInsight 群集。 当前，可以将 Azure Monitor 日志与以下 HDInsight 群集类型配合使用：

  * Hadoop
  * HBase
  * 交互式查询
  * Kafka
  * Spark
  * Storm

  有关如何创建 HDInsight 群集的说明，请参阅 [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。  

* 如果使用 PowerShell，则需要 [Az 模块](/powershell/azure/)。 确保已安装了最新版本。 如有必要，请运行 `Update-Module -Name Az`。

* 如果想要使用 Azure CLI，但尚未安装，请参阅 [安装 Azure CLI](/cli/azure/install-azure-cli)。

> [!NOTE]  
> 新的 Azure Monitor 体验仅在美国东部和西欧作为预览版功能提供。 建议将 HDInsight 群集和 Log Analytics 工作区放置在同一区域中，以实现更好的性能。 Azure Monitor 日志并非在所有 Azure 区域中均可用。
>

## <a name="enable-azure-monitor-using-the-portal"></a>通过门户启用 Azure Monitor

在本部分中，将配置现有 HDInsight Hadoop 群集，以使用 Azure Log Analytics 工作区来监视作业、调试日志等等。

1. 从 [Azure 门户](https://portal.azure.com/)中，选择群集。 群集将在新的门户页面中打开。

2. 在左侧的“监视”下，选择“监视集成” **** 。 

3. 在主视图中的“用于 HDInsight 群集集成的 Azure Monitor”下，选择“启用” ****  **** 。 

4. 在“选择工作区”下拉列表中，选择现有的 Log Analytics 工作区 **** 。 

5. 选择“保存”。 **** 需要几分钟来保存设置。 

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-azure-monitor.png" alt-text="启用 HDInsight 群集的监视":::

如果你要禁用 Azure Monitor，可以在此门户中禁用。 

## <a name="enable-azure-monitor-using-azure-powershell"></a>使用 Azure PowerShell 启用 Azure Monitor

可以使用 Azure PowerShell Az 模块 [Enable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/enable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) cmdlet 启用 Azure Monitor 日志。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster
```

若要禁用，请使用 [Disable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/disable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) cmdlet：

```powershell
Disable-AzHDInsightAzureMonitor -ResourceGroupName $resourceGroup `
-ClusterName $cluster
```

## <a name="enable-azure-monitor-using-azure-cli"></a>使用 Azure CLI 启用 Azure Monitor

可以使用 Azure CLI [`az hdinsight azure-monitor enable`](/cli/azure/hdinsight/azure-monitor?view=azure-cli-latest&preserve-view=true) 命令启用 Azure Monitor 日志。

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor show --name $cluster --resource-group $resourceGroup
```

若要禁用，请使用 [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) 命令。

```azurecli
az hdinsight azure-monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="use-hdinsight-out-of-box-insights-to-monitor-a-single-cluster"></a>使用 HDInsight 中现成的见解来监视单个群集

HDInsight 提供特定于工作负载的工作簿来帮助你快速获取见解。 此工作簿从 HDInsight 群集收集重要性能指标，并为最常用的方案提供可视化效果和仪表板。 现成的见解提供单个 HDInsight 群集的完整视图，包括资源利用率和应用程序状态。

可用的 HDInsight 工作簿：

- HDInsight Spark 工作簿
- HDInsight Kafka 工作簿
- HDInsight HBase 工作簿
- HDInsight Hive/LLAP 工作簿
- HDInsight Storm 工作簿

Spark 工作簿的屏幕截图 :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-spark-workbook.png" alt-text="Spark 工作簿屏幕截图":::

## <a name="use-at-scale-insights-to-monitor-multiple-clusters"></a>使用大规模见解监视多个群集

可以登录到 Azure 门户并选择“监视”。 在“见解”部分，可以选择“见解中心” 。 然后可以找到 HDInsight 群集。

在此视图中，可以在一个位置监视多个 HDInsight 群集。
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-monitor-insights.png" alt-text="群集监视器见解屏幕截图":::

可以选择要监视的订阅和 HDInsight 群集。 
 - “受监视的群集”显示已启用 Azure Monitor 集成的群集数。
 - “未受监视的群集”显示未启用 Azure Monitor 集成的群集数。

可在每个部分查看详细的群集列表。 

在“受监视的群集”下的“概述”选项卡中，可以看到群集类型、关键警报和资源利用率 。
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-cluster-alerts.png" alt-text="群集监视器警报屏幕截图":::

此外，还可以看到每种工作负载类型（包括 Spark、HBase、Hive、Kafka 和 Storm）中的群集。

将显示每种工作负载类型的概要指标，包括处于活动状态的节点管理器数、正在运行的应用程序数，等等。

:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/spark-metrics.png" alt-text="群集监视器 Spark 指标":::

## <a name="configuring-performance-counters"></a>配置性能计数器

Azure monitor 支持收集并分析群集中节点的性能指标。 有关详细信息，请参阅 [Azure Monitor 中的 Linux 性能数据源](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)。

## <a name="cluster-auditing"></a>群集审核

HDInsight 支持通过导入以下日志类型进行 Azure Monitor 日志的群集审核：

* `log_gateway_audit_CL` - 此表提供来自群集网关节点的审核日志，其中显示了成功和失败的登录尝试。
* `log_auth_CL` - 此表提供 SSH 日志，其中显示了成功和失败的登录尝试。
* `log_ambari_audit_CL` - 此表提供了 Ambari 中的审核日志。
* `log_ranger_audti_CL`此表提供了针对 ESP 群集的 Apache Ranger 审核日志。


#### <a name="classic-azure-monitor-experience"></a>[经典 Azure Monitor 体验](#tab/previous)

## <a name="prerequisites"></a>先决条件

* Log Analytics 工作区。 可将此工作区视为独特的 Azure Monitor 日志环境，包含自身的数据存储库、数据源和解决方案。 有关说明，请参阅[创建 Log Analytics 工作区](../azure-monitor/vm/monitor-virtual-machine.md)。

* 一个 Azure HDInsight 群集。 当前，可以将 Azure Monitor 日志与以下 HDInsight 群集类型配合使用：

  * Hadoop
  * HBase
  * 交互式查询
  * Kafka
  * Spark
  * Storm

  有关如何创建 HDInsight 群集的说明，请参阅 [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。  

* 如果使用 PowerShell，则需要 [Az 模块](/powershell/azure/)。 确保已安装了最新版本。 如有必要，请运行 `Update-Module -Name Az`。

* 如果想要使用 Azure CLI，但尚未安装，请参阅 [安装 Azure CLI](/cli/azure/install-azure-cli)。

> [!NOTE]  
> 建议将 HDInsight 群集和 Log Analytics 工作区放置在同一区域中，以实现更好的性能。 Azure Monitor 日志并非在所有 Azure 区域中均可用。

## <a name="enable-azure-monitor-using-the-portal"></a>通过门户启用 Azure Monitor

在本部分中，将配置现有 HDInsight Hadoop 群集，以使用 Azure Log Analytics 工作区来监视作业、调试日志等等。

1. 从 [Azure 门户](https://portal.azure.com/)中，选择群集。 群集将在新的门户页面中打开。

1. 从左侧的“监视”下，选择“Azure Monitor”。 

1. 从主视图的“Azure Monitor 集成”下，选择“启用”。 

1. 在“选择工作区”下拉列表中，选择现有的 Log Analytics 工作区。

1. 选择“保存”。  需要几分钟来保存设置。

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="启用 HDInsight 群集的监视":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>使用 Azure PowerShell 启用 Azure Monitor

可以使用 Azure PowerShell Az 模块 [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet 来启用 Azure Monitor 日志。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

若要禁用，请使用 [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) cmdlet：

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>使用 Azure CLI 启用 Azure Monitor

可以使用 Azure CLI `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable) 命令来启用 Azure Monitor 日志。

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

若要禁用，请使用 [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) 命令。

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>安装 HDInsight 群集管理解决方案

HDInsight 提供特定于群集的管理解决方案，可为 Azure Monitor 日志添加这些解决方案。 [管理解决方案](../azure-monitor/insights/solutions.md)添加了用于处理 Azure Monitor 日志的功能，并提供其他数据和分析工具。 这些解决方案从 HDInsight 群集中收集重要的性能指标。 并且提供工具用于搜索指标。 这些解决方案还为 HDInsight 支持的大多数群集类型提供可视化和仪表板。 使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。

可用的 HDInsight 解决方案：

* HDInsight Hadoop 监视
* HDInsight HBase 监视
* HDInsight 交互式查询监视
* HDInsight Kafka 监视
* HDInsight Spark 监视
* HDInsight Storm 监视

有关管理解决方案的说明，请参阅 [Azure 中的管理解决方案](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)。 若要体验，请安装 HDInsight Hadoop 监视解决方案。 完成后，将看到“摘要”下列出的“HDInsightHadoop”磁贴。  选择“HDInsightHadoop”磁贴。 HDInsightHadoop 解决方案如下所示：

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight 监视解决方案视图":::

由于群集是全新的群集，因此报告不会显示任何活动。

## <a name="configuring-performance-counters"></a>配置性能计数器

Azure monitor 支持收集并分析群集中节点的性能指标。 有关详细信息，请参阅 [Azure Monitor 中的 Linux 性能数据源](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)。

## <a name="cluster-auditing"></a>群集审核

HDInsight 支持通过导入以下日志类型进行 Azure Monitor 日志的群集审核：

* `log_gateway_audit_CL` - 此表提供来自群集网关节点的审核日志，其中显示了成功和失败的登录尝试。
* `log_auth_CL` - 此表提供 SSH 日志，其中显示了成功和失败的登录尝试。
* `log_ambari_audit_CL` - 此表提供了 Ambari 中的审核日志。
* `log_ranger_audti_CL`此表提供了针对 ESP 群集的 Apache Ranger 审核日志。

## <a name="next-steps"></a>后续步骤

* [ Azure Monitor 日志以监视 HDInsight 群集](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [任何通过 Apache Ambari 和 Azure Monitor 日志监视群集可用性](./hdinsight-cluster-availability.md)