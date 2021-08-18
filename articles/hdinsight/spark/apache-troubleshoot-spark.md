---
title: 对 Azure HDInsight 上的 Apache Spark 进行故障排除
description: 获取有关使用 Apache Spark 和 Azure HDInsight 的常见问题的解答。
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 6841a6965646afbf4192c502c0a9eba260531f5e
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369067"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Apache Spark 进行故障排除

了解处理 [Apache Ambari](https://ambari.apache.org/) 中的 Apache Spark 有效负载时的最常见问题及其解决方法。

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>如何在群集上使用 Apache Ambari 配置 Apache Spark 应用程序？

可以优化 Spark 配置值，帮助避免出现 Apache Spark 应用程序 `OutofMemoryError` 异常。 以下步骤显示了 Azure HDInsight 中的默认 Spark 配置值：

1. 使用群集凭据通过 `https://CLUSTERNAME.azurehdidnsight.net` 登录到 Ambari。 初始屏幕显示了概述仪表板。 HDInsight 3.6 和 4.0 在外观上略有不同。

1. 导航到“Spark2” > “配置” 。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="选择“配置”选项卡" border="true":::

1. 在配置列表中，选择并展开 **Custom-spark2-Defaults**。

1. 找到需要调整的值设置，例如 **spark.executor.memory**。 在本例中，值 **9728m** 太大。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="选择 custom-spark-defaults" border="true":::

1. 将值设置为建议的设置。 建议为此设置使用值 **2048m**。

1. 保存值，并保存配置。 选择“保存”。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="将值更改为 2048m" border="true":::

    编写有关配置更改的注释，并选择“保存”。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="输入有关所做更改的注释" border="true":::

    如果有任何配置需要引以注意，系统会发出通知。 记下这些项，并选择“仍然继续”。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="选择“仍然继续”" border="true":::

1. 每次保存配置时，系统都会提示重启服务。 选择“重启”。 

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="选择“重启”" border="true":::

    确认重启。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="选择“确认全部重启”" border="true":::

    可以查看正在运行的进程。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="查看正在运行的进程" border="true":::

1. 可以添加配置。 在配置列表中，依次选择“Custom-spark2-defaults”、“添加属性”。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="选择“添加属性”" border="true":::

1. 定义新属性。 可以使用对话框为特定的设置（例如数据类型）定义单个属性。 或者，可以定义多个属性（每行定义一个属性）。

    在本示例中，使用值 **4g** 定义了 **spark.driver.memory** 属性。

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="定义新属性" border="true":::

1. 根据步骤 6 和 7 中所述保存配置并重启服务。

这些更改会应用到整个群集，但可以在提交 Spark 作业时将其覆盖。

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>如何在群集上使用 Jupyter Notebook 配置 Apache Spark 应用程序？

在 Jupyter Notebook 第一个单元中的 %%configure 指令后面，使用有效的 JSON 格式指定 Spark 配置。 根据需要更改实际值：

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="添加配置" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>如何在群集上使用 Apache Livy 配置 Apache Spark 应用程序？

使用 cURL 等 REST 客户端将 Spark 应用程序提交到 Livy。 使用如下所示的命令。 根据需要更改实际值：

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>如何在群集上使用 spark-submit 配置 Apache Spark 应用程序？

使用如下所示的命令启动 spark-shell。 根据需要更改实际配置值：

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>附加阅读材料

[在 HDInsight 群集上提交 Apache Spark 作业](/archive/blogs/azuredatalake/spark-job-submission-on-hdinsight-101)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* [Spark 内存管理概述](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [在 HDInsight 群集上调试 Spark 应用程序](/archive/blogs/azuredatalake/spark-debugging-101)。

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。
