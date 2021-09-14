---
title: 使用 Azure 事件中心收集 Apache Spark 应用程序日志和指标（预览）
description: 在本教程中，你将了解如何使用 Synapse Apache Spark 诊断发射器扩展将 Apache Spark 应用程序的日志、事件日志和指标发送到 Azure 事件中心。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: c3b8fbda0eaaa932f784540cc55d0a4e583927f0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544491"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-event-hubs-preview"></a>使用 Azure 事件中心收集 Apache Spark 应用程序日志和指标（预览）

Synapse Apache Spark 诊断发射器扩展是一个库，通过该库，Apache Spark 应用程序能够将日志、事件日志和指标发送到一个或多个目标，包括 Azure Log Analytics、Azure 存储和 Azure 事件中心。 

在本教程中，你将了解如何使用 Synapse Apache Spark 诊断发射器扩展将 Apache Spark 应用程序的日志、事件日志和指标发送到 Azure 事件中心。

## <a name="collect-logs-and-metrics-to-azure-event-hubs"></a>将日志和指标收集到 Azure 事件中心

### <a name="step-1-create-an-azure-event-hub-instance"></a>步骤 1：创建 Azure 事件中心实例

若要将诊断日志和指标收集到 Azure 事件中心，可使用现有的 Azure 事件中心实例。
如果没有，可[创建一个事件中心](../../event-hubs/event-hubs-create.md)。

### <a name="step-2-create-a-apache-spark-configuration-file"></a>步骤 2：创建 Apache Spark 配置文件

创建一个 `diagnostic-emitter-azure-event-hub-conf.txt`，并将以下内容复制到文件中。 或者，下载 Apache Spark 池配置的[示例模板文件](https://go.microsoft.com/fwlink/?linkid=2169375)。

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureEventHub
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.secret <connection-string>
```

在配置文件中填写以下参数：`<connection-string>`。
有关参数的更多说明，请参阅 [Azure EventHub 配置](#available-configurations)

### <a name="step-3-upload-the-apache-spark-configuration-file-to-apache-spark-pool"></a>步骤 3：将 Apache Spark 配置文件上传到 Apache Spark 池

1. 在 Synapse Studio 中导航到 Apache Spark 池（“管理”->“Apache Spark 池”）
2. 单击 Apache Spark 池右侧的“…”按钮并选择“Apache Spark 配置” 
3. 单击“上传”并选择“.txt”配置文件，然后单击“应用” 。

## <a name="available-configurations"></a>“可用配置”

| 配置                                                               | 说明                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `spark.synapse.diagnostic.emitters`                                         | 必需。 诊断发射器的目标名称（以逗号分隔）。                                                                                                                              |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | 必需。 内置目标类型。 要启用 Azure 事件中心目标，该值应为 `AzureEventHub`。                                                                                    |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | 可选。 以逗号分隔的选定日志类别。 可用的值包括 `DriverLog`、`ExecutorLog`、`EventLog` 和 `Metrics`。 如果未设置，则默认值为类别“所有”。              |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | 可选。 Azure Eventhub 实例连接字符串。 此字段应与模式 `Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<PathName>` 匹配 |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | 如果未指定 `.secret`，则是必需的。 存储机密（连接字符串）的 [Azure 密钥保管库](../../key-vault/general/overview.md)名称。                                                                  |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | 如果指定了 `.secret.keyVault`，则该参数是必需的。 存储机密（连接字符串）的 Azure 密钥保管库机密名称。                                                                         |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | 可选。 以逗号分隔的 Spark 事件名称，你可指定要收集的事件。 例如： `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | 可选。 以逗号分隔的 log4j 记录器名称，你可指定要收集的日志。 例如： `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | 可选。 以逗号分隔的 Spark 指标名称后缀，你可指定要收集的指标。 例如： `jvm.heap.used` |


> [!NOTE]
>
> Azure Eventhub 实例连接字符串应始终包含 `EntityPath`，它是 Azure 事件中心实例的名称。

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

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>启用了数据外泄保护功能的 Synapse 工作区

Azure Synapse Analytics 工作区支持对工作区启用数据外泄保护。 启用外泄保护后，日志和指标不能直接发送到目标终结点。 在此场景中，你可为不同的目标终结点创建相应的[托管专用终结点](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)或[创建 IP 防火墙规则](../../synapse-analytics/security/synapse-workspace-ip-firewall.md)。




