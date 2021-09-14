---
title: 使用 Azure 存储帐户收集 Apache Spark 应用程序日志和指标（预览版）
description: 本文介绍如何使用 Synapse Spark 诊断发射器扩展收集日志、事件日志和指标，以及如何集成 Grafana 仪表板。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: c29cdbd9879397b3e171160f93ccd0ac712467db
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544502"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-storage-accountpreview"></a>使用 Azure 存储帐户收集 Apache Spark 应用程序日志和指标（预览版）

Synapse Apache Spark 诊断发射器扩展是一个库，通过该库，Apache Spark 应用程序能够将日志、事件日志和指标发送到一个或多个目标，包括 Azure Log Analytics、Azure 存储和 Azure 事件中心。 

在本教程中，你将了解如何使用 Synapse Apache Spark 诊断发射器扩展将 Apache Spark 应用程序的日志、事件日志和指标发送到 Azure 存储帐户。

## <a name="collect-logs-and-metrics-to-storage-account"></a>将日志和指标收集到存储帐户

### <a name="step-1-create-a-storage-account"></a>步骤 1：创建存储帐户

若要将诊断日志和指标收集到存储帐户，可以使用现有的 Azure 存储帐户。 如果没有帐户，可以[创建一个 Azure Blob 存储帐户](../../storage/common/storage-account-create.md)，或[创建一个用于 Azure Data Lake Storage Gen2 的存储帐户](../../storage/blobs/create-data-lake-storage-account.md)。

### <a name="step-2-create-an-apache-spark-configuration-file"></a>步骤 2：创建 Apache Spark 配置文件

创建一个 `diagnostic-emitter-azure-storage-conf.txt` 并将以下内容复制到文件中。 或下载 Apache Spark 池配置的[示例模板文件](https://go.microsoft.com/fwlink/?linkid=2169375)。

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureStorage
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.uri https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>
spark.synapse.diagnostic.emitter.MyDestination1.auth AccessKey
spark.synapse.diagnostic.emitter.MyDestination1.secret <storage-access-key>
```

在配置文件中填写以下参数：`<my-blob-storage>`、`<container-name>`、`<folder-name>`、`<storage-access-key>`。
有关参数的更多说明，请参阅 [Azure 存储配置](#available-configurations)

### <a name="step-3-upload-the-apache-spark-configuration-file-to-spark-pool"></a>步骤 3：将 Apache Spark 配置文件上传到 Spark 池

1. 在 Synapse Studio 中导航到 Apache Spark 池（“管理”->“Apache Spark 池”）
1. 单击 Apache Spark 池右侧的“…”按钮并选择“Apache Spark 配置” 
1. 单击“上传”并选择“.txt”配置文件。 然后单击“应用”。

### <a name="step-4-view-the-logs-files-in-azure-storage-account"></a>步骤 4：查看 Azure 存储帐户中的日志文件


将作业提交到配置的 Apache Spark 池后，你应该能够在目标存储帐户中查看日志和指标文件。
`<workspaceName>.<sparkPoolName>.<livySessionId>` 根据不同的应用程序，将日志置于相应的路径中。
所有日志文件都采用 JSON 行格式（也称为换行符分隔的 JSON、ndjson），非常便于数据处理。

## <a name="available-configurations"></a>“可用配置”

| 配置 | 说明 |
| --- | --- |
| `spark.synapse.diagnostic.emitters`                                         | 必需。 诊断发射器以逗号分隔的目标名称。 例如： `MyDest1,MyDest2` |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | 必需。 内置目标类型。 若要启用 Azure 存储目标，此字段中需要包含 `AzureStorage`。 |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | 可选。 以逗号分隔的选定日志类别。 可用的值包括 `DriverLog`、`ExecutorLog`、`EventLog`、`Metrics`。 如果未设置，则默认值为“所有”类别。 |
| `spark.synapse.diagnostic.emitter.<destination>.auth`                       | 必需。 `AccessKey` 表示使用存储帐户[访问密钥](../../storage/common/storage-account-keys-manage.md)授权。 `SAS` 表示[共享访问签名](../../storage/common/storage-sas-overview.md)授权。 |
| `spark.synapse.diagnostic.emitter.<destination>.uri`                        | 必需。 目标 Blob 容器文件夹 URI。 应匹配模式 `https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>`。 |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | 可选。 机密（AccessKey 或 SAS）内容。 |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | 如果未指定 `.secret`，则是必需的。 存储机密（AccessKey 或 SAS）的 [Azure Key Vault](../../key-vault/general/overview.md) 名称。 |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | 如果指定了 `.secret.keyVault`，则该参数是必需的。 存储机密（AccessKey 或 SAS）的 Azure Key Vault 机密名称。 |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | 可选。 以逗号分隔的 Spark 事件名称，你可以指定要收集的事件。 例如：`SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | 可选。 以逗号分隔的 log4j 记录器名称，你可以指定要收集的日志。 例如：`org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | 可选。 以逗号分隔的 Spark 指标名称后缀，你可以指定要收集的指标。 例如：`jvm.heap.used` |

## <a name="log-data-sample"></a>日志数据示例

下面是 JSON 格式的示例日志记录：

```json
{
    "timestamp": "2021-01-02T12:34:56.789Z",
    "category": "Log|EventLog|Metrics",
    "workspaceName": "<my-workspace-name>",
    "sparkPool": "<spark-pool-name>",
    "livyId": "<livy-session-id>",
    "applicationId": "<application-id>",
    "applicationName": "<application-name>",
    "executorId": "<driver-or-executor-id>",
    "properties": {
        // The message properties of logs, events and metrics.
        "timestamp": "2021-01-02T12:34:56.789Z",
        "message": "Registering signal handler for TERM",
        "logger_name": "org.apache.spark.util.SignalUtils",
        "level": "INFO",
        "thread_name": "main"
        // ...
    }
}
```

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>启用了数据外泄防护功能的 Synapse 工作区

Azure Synapse Analytics 工作区支持对工作区启用数据外泄保护。 借助外泄防护功能，日志和指标不能直接发送到目标终结点。 在这种情况下，你可以为不同的目标终结点创建相应的[托管专用终结点](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)或[创建 IP 防火墙规则](../../synapse-analytics/security/synapse-workspace-ip-firewall.md)。


1. 导航到“Synapse Studio”>“管理”>“托管专用终结点”，单击“新建”按钮，选择“Azure Blob 存储”或“Azure Data Lake Storage Gen2”，然后选择“继续”    。
   > [!div class="mx-imgBorder"]
   > ![创建托管专用终结点 1](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-1.png)
2. 在“存储帐户名称”中选择你的 Azure 存储帐户，然后单击“创建”按钮 。
   > [!div class="mx-imgBorder"]
   > ![创建托管专用终结点 2](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-2.png)
3. 预配专用终结点期间请耐心等待。
4. 在 Azure 门户中导航到你的存储帐户，在“网络” > “专用终结点连接”页面上，选择刚刚预配的连接和“批准”  。


