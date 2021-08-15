---
title: 使用 Azure Log Analytics 收集和可视化指标和日志（预览版）
description: 了解如何启用 Synapse 内置 Azure Log Analytics 连接器，以便收集 Apache Spark 应用程序指标和日志并将其发送到 Azure Log Analytics 工作区。
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 3ed74340c4e234ae1ea4781d8b91451be6e366c4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515605"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>教程：使用 Azure Log Analytics 收集和可视化指标和日志（预览版）

本教程介绍如何启用 Synapse 内置 Azure Log Analytics 连接器，以便收集 Apache Spark 应用程序指标和日志并将其发送到 [Azure Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)。 然后，可以利用 Azure Monitor 工作簿可视化指标和日志。

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>在 Synapse Studio 中配置 Azure Log Analytics 工作区信息

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>步骤 1：创建 Azure Log Analytics 工作区

可以按照以下文档创建 Log Analytics 工作区：
- [在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)
- [使用 Azure CLI 创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace-cli.md)
- [使用 PowerShell 在 Azure Monitor 中创建和配置 Log Analytics 工作区](../../azure-monitor/logs/powershell-workspace-configuration.md)

### <a name="step-2-prepare-a-spark-configuration-file"></a>步骤 2：准备 Spark 配置文件

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>选项 1. 配置 Azure Log Analytics 工作区 ID 和密钥 

复制以下 Spark 配置，将其保存为“spark_loganalytics_conf.txt”，并填充参数：

   - `<LOG_ANALYTICS_WORKSPACE_ID>`：Azure Log Analytics 工作区 ID。
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`Azure Log Analytics 密钥：Azure 门户 > Azure Log Analytics 工作区 > 代理管理 > 主密钥

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>选项 2. 配置 Azure 密钥保管库

> [!NOTE]
>
> 需要向将提交 Spark 应用程序的用户授予读取机密权限。 请参阅[使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../../key-vault/general/rbac-guide.md)

若要配置 Azure 密钥保管库以存储工作区密钥，请执行以下步骤：

1. 在 Azure 门户中，创建并导航到密钥保管库
2. 在 Key Vault 设置页中，选择“机密”。
3. 单击“生成/导入”。
4. 在“创建机密”屏幕上，选择以下值：
   - **名称**：为机密键入名称，默认键入 `"SparkLogAnalyticsSecret"`。
   - **值**：键入机密 <LOG_ANALYTICS_WORKSPACE_KEY>。
   - 让其他值保留默认设置。 单击“创建”。
5. 复制以下 Spark 配置，将其保存为“spark_loganalytics_conf.txt”，并填充参数：

   - `<LOG_ANALYTICS_WORKSPACE_ID>`：Azure Log Analytics 工作区 ID。
   - `<AZURE_KEY_VAULT_NAME>`：配置的 Azure 密钥保管库名称。
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>`（可选）：工作区密钥的 Azure 密钥保管库中的机密名称，默认为“SparkLogAnalyticsSecret”。

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> 还可以将 Log Analytics 工作区 ID 存储到 Azure 密钥保管库。 请参阅上述步骤，并存储包含机密名称 `"SparkLogAnalyticsWorkspaceId"` 的工作区 ID。 或使用配置 `spark.synapse.logAnalytics.keyVault.key.workspaceId` 来指定 Azure 密钥保管库中的工作区 ID 机密名称。

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>选项 3. 配置 Azure 密钥保管库链接服务

> [!NOTE]
>
> 需要向 Synapse 工作区授予读取机密权限。 请参阅[使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../../key-vault/general/rbac-guide.md)

若要在 Synapse Studio 中配置 Azure 密钥保管库链接服务以存储工作区密钥，请执行以下步骤：

1. 执行 `Option 2. Configure with an Azure Key Vault` 部分中的所有步骤。
2. 在 Synapse Studio 中创建 Azure 密钥保管库链接服务：

    a. 导航到“Synapse Studio”>“管理”>“链接服务”，然后单击“新建”按钮 。

    b. 在搜索框中搜索“Azure Key Vault”。

    c. 为链接服务键入名称。

    d. 选择你的 Azure 密钥保管库。 单击“创建”。

3. 将 `spark.synapse.logAnalytics.keyVault.linkedServiceName` 项添加到 Spark 配置。

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>可用的 Spark 配置

| 配置名称                                  | 默认值                | 说明                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark.synapse.logAnalytics.enabled                  | false                        | 若要为 Spark 应用程序启用 Azure Log Analytics 接收器，则为 true。 否则为 false。                                                                                                                  |
| spark.synapse.logAnalytics.workspaceId              | -                            | 目标 Azure Log Analytics 工作区 ID                                                                                                                                                          |
| spark.synapse.logAnalytics.secret                   | -                            | 目标 Azure Log Analytics 工作区机密。                                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | -                            | Azure Log Analytics 工作区 ID 和密钥的 Azure 密钥保管库链接服务名称                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Azure Log Analytics ID 和密钥的 Azure 密钥保管库名称                                                                                                                                                |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Azure Log Analytics 工作区 ID 的 Azure 密钥保管库机密名称                                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.key.secret      | SparkLogAnalyticsSecret      | Azure Log Analytics 工作区密钥的 Azure 密钥保管库机密名称                                                                                                                                      |
| spark.synapse.logAnalytics.uriSuffix       | ods.opinsights.azure.com     | 目标 Azure Log Analytics 工作区 [URI 后缀][uri_suffix]。 如果 Azure Log Analytics 工作区不在 Azure 全局中，则需要根据相应的云更新 URI 后缀。 |

> [!NOTE]  
> - 对于 Azure 中国云，“spark.synapse.logAnalytics.uriSuffix”参数应为“ods.opinsights.azure.cn”。 
> - 对于 Azure 政府云，“spark.synapse.logAnalytics.uriSuffix”参数应为“ods.opinsights.azure.us”。 

[uri_suffix]: ../../azure-monitor/logs/data-collector-api.md#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>步骤 3：将 Spark 配置上传到 Spark 池
可以将配置文件上传到 Synapse Studio 中的 Synapse Spark 池。

   1. 在 Azure Synapse Studio 中导航到 Apache Spark 池（“管理”->“Apache Spark 池”）
   2. 单击 Apache Spark 池右侧的“…”按钮
   3. 选择 Apache Spark 配置 
   4. 单击“上传”，然后选择创建的“spark_loganalytics_conf.txt” 。
   5. 单击“上传”和“应用” 。

      > [!div class="mx-imgBorder"]
      > ![spark 池配置](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> 提交到上述 Spark 池的所有 Spark 应用程序都将使用该配置设置将 Spark 应用程序指标和日志推送到指定的 Azure Log Analytics 工作区。

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>提交 Spark 应用程序并查看 Azure Log Analytics 中的日志和指标

 1. 可以使用以下方法之一将 Spark 应用程序提交到上一步中配置的 Spark 池：
    - 运行 Synapse Studio 笔记本。 
    - 通过 Spark 作业定义提交 Synapse Apache Spark 批处理作业。
    - 运行包含 Spark 活动的管道。

 2. 转到指定的 Azure Log Analytics 工作区，然后在 Spark 应用程序开始运行时查看应用程序指标和日志。

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>使用示例 Azure Log Analytics 工作簿可视化指标和日志

1. 在此处[下载工作簿](https://aka.ms/SynapseSparkLogAnalyticsWorkbook)。
2. 打开并“复制”工作簿文件内容。
3. 导航到 Azure Log Analytics 工作簿（[“Azure 门户”](https://portal.azure.com/)>“Log Analytics 工作区”>“工作簿”）
4. 在“高级编辑器”模式下打开“空”Azure Log Analytics 工作簿（按“</>”图标） 。
5. 粘贴任何存在的 json。
6. 然后依次按“应用”和“完成编辑” 。

    > [!div class="mx-imgBorder"]
    > ![新建工作簿](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![导入工作簿](./media/apache-spark-azure-log-analytics/import-workbook.png)

然后，将 Apache Spark 应用程序提交到配置的 Spark 池。 在应用程序进入正在运行状态后，在工作簿下拉列表中选择正在运行的应用程序。

> [!div class="mx-imgBorder"]
> ![工作簿图像](./media/apache-spark-azure-log-analytics/workbook.png)

可以通过 Kusto 查询和配置警报来自定义工作簿。

> [!div class="mx-imgBorder"]
> ![kusto 查询和警报](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>示例 Kusto 查询

1. 查询 Spark 事件示例。

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. 查询 Spark 应用程序驱动程序和执行程序日志示例。

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. 查询 Spark 指标示例。

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="write-custom-application-logs"></a>编写自定义应用程序日志

可以使用 Apache Log4j 库编写自定义日志。

Scala 的示例：

```scala
%%spark
val logger = org.apache.log4j.LogManager.getLogger("com.contoso.LoggerExample")
logger.info("info message")
logger.warn("warn message")
logger.error("error message")
```

PySpark 的示例：

```python
%%pyspark
logger = sc._jvm.org.apache.log4j.LogManager.getLogger("com.contoso.PythonLoggerExample")
logger.info("info message")
logger.warn("warn message")
logger.error("error message")
```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>使用 Azure Log Analytics 创建和管理警报

通过 Azure Monitor 警报，用户可以使用 Log Analytics 查询按每个设置的频率评估指标和日志，并根据结果触发警报。

有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/alerts/alerts-log.md)。

## <a name="limitation"></a>限制

不支持已启用[托管虚拟网络](../security/synapse-workspace-managed-vnet.md)的 Azure Synapse Analytics 工作区。

## <a name="next-steps"></a>后续步骤

 - 了解如何[在 Synapse Studio 中使用无服务器 Apache Spark 池](../quickstart-create-apache-spark-pool-studio.md)。
 - 了解如何[在笔记本中运行 Spark 应用程序](./apache-spark-development-using-notebooks.md)。
 - 了解如何[在 Synapse Studio 中创建 Apache Spark 作业定义](./apache-spark-job-definitions.md)。
