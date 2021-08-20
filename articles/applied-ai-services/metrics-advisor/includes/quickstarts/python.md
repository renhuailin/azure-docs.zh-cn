---
title: 指标顾问 Python 快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 07/07/2021
ms.author: mbullwin
ms.openlocfilehash: 630219c51f484e3674facb4827910e8851cfffb9
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341428"
---
[参考文档](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [库源代码](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [包 (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* 获得 Azure 订阅后，<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="“创建指标顾问”资源"  target="_blank">在 Azure 门户中创建“指标顾问”资源 </a>，以部署“指标顾问”实例。  
* 带有时序数据的 SQL 数据库。
  
> [!TIP]
> * 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples) 上找到 Python 指标顾问示例。
> * 指标顾问资源可能需要 10 到 30 分钟才能部署一个服务实例供你使用。 部署成功后，单击“转到资源”。 部署完成后，可以通过 Web 门户和 REST API 这两种方式开始使用指标顾问实例。
> * 可以在 Azure 门户的资源“概述”部分中找到 REST API 的 URL。 它将如下所示：
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

在安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建新的 Python 文件并导入以下库。

```python
import os
import datetime
```

为资源的 Azure 终结点和密钥创建变量。

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的指标顾问资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 <br><br>若要检索 API 密钥，必须转到 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 为资源选择相应的“目录”、“订阅”和“工作区”，然后选择“入门”   。 然后，你就能够从 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 检索 API 密钥。   
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>对象模型

以下类处理指标顾问 Python SDK 的某些主要功能。

|名称|说明|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | 用途： <br> - 列出事件 <br> - 列出事件的根本原因 <br> - 检索原始时序数据和由服务丰富的时序数据。 <br> - 列出警报 <br> - 添加反馈以优化模型 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | 允许你： <br> - 管理数据馈送 <br> - 创建、配置、检索、列出和删除异常情况检测配置 <br> - 创建、配置、检索、列出和删除异常情况警报配置 <br> - 管理挂钩  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| 指标顾问从数据源中引入的内容。`DataFeed` 包含以下行： <br> - 时间戳 <br> - 零维度或多个维度 <br> - 一个或多个度量值  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | `DataFeedMetric` 是用于监视和评估特定业务流程状态的可计量度量值。 它可以是划分为多个维度的多个时序值的组合。 例如，Web 运行状况指标可能包含用户数和 en-us 市场的维度  。 |

## <a name="code-examples"></a>代码示例

这些代码片段展示如何借助适用于 Python 的指标顾问客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [添加数据馈送](#add-a-data-feed)
* [检查引入状态](#check-the-ingestion-status)
* [设置检测配置和警报配置](#configure-anomaly-detection)
* [创建警报配置](#create-an-alert-configuration)
* [查询异常情况检测结果](#query-the-alert)

## <a name="authenticate-the-client"></a>验证客户端

在此示例中，客户端是一个使用终结点的 `MetricsAdvisorAdministrationClient` 对象，以及一个包含密钥的 `MetricsAdvisorKeyCredential` 对象。 不需要复制此代码示例。 稍后创建的方法将实例化客户端。 备用客户端名为 `MetricsAdvisorClient`；有关此客户端的详细信息，请参阅[参考文档](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient)。

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>添加数据馈送

在新方法中，创建类似于以下示例的 import 语句。 将 `sql_server_connection_string` 替换为你自己的 SQL Server 连接字符串，并将 `query` 替换为在单个时间戳返回数据的查询。 你还需要根据自定义数据调整 `DataFeedmetric` 和 `DataFeedDimension` 值。

> [!IMPORTANT]
> 对于每个维度组合，查询应在每个时间戳处最多返回一条记录。 查询返回的所有记录必须具有相同的时间戳。 指标顾问将针对每个时间戳运行此查询以引入数据。  有关详细信息和示例，请参阅[教程：编写有效的查询](../../tutorials/write-a-valid-query.md)。

通过密钥和终结点创建客户端，并使用 `client.create_data_feed()` 配置名称、源、粒度和架构。 你还可以设置引入时间、汇总设置等。


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SqlServerDataFeedSource,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedRollupSettings,
        DataFeedMissingDataPointFillSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SqlServerDataFeedSource(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    data_feed_description="cost/revenue data feed",
    rollup_settings=DataFeedRollupSettings(
        rollup_type="AutoRollup",
        rollup_method="Sum",
        rollup_identification_value="__CUSTOM_SUM__"
    ),
    missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
        fill_type="SmartFilling"
    ),
    access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>检查引入状态

在新方法中，创建类似于以下示例的 import 语句。 将 `data_feed_id` 替换为你创建的数据馈送的 ID。 通过密钥和终结点创建客户端，并使用 `client.list_data_feed_ingestion_status()` 获取引入进度。 输出详细信息，如最后一个有效且成功的时间戳。


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>配置异常情况检测

在新方法中，创建类似于以下示例的 import 语句。 将 `metric_id` 替换为要配置的指标的 ID。 通过密钥和终结点创建客户端，并使用 `client.create_detection_configuration` 创建新的检测配置。 阈值条件为异常情况检测指定参数。

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        condition_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>创建挂钩

在新方法中，创建类似于以下示例的 import 语句。 通过密钥和终结点创建客户端，并使用 `client.create_hook()` 创建挂钩。 输入说明、要向其发送警报的电子邮件列表，以及将在警报中显示的外部链接。

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts"   #A customized field configured by users, which will be displayed in the anomaly alert. It's usually been used to link to a troubleshooting guide to help further diagnose the issue.
    )
)
```

##  <a name="create-an-alert-configuration"></a>创建警报配置

在新方法中，创建类似于以下示例的 import 语句。 将 `detection_configuration_id` 替换为异常情况检测配置的 ID，并将 `hook_id` 替换为之前创建的挂钩。 通过密钥和终结点创建客户端，并使用 `client.create_alert_configuration()` 创建警报配置。 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>查询警报

在新方法中，创建类似于以下示例的 import 语句。 将 `alert_id` 替换为警报的 ID，并将 `alert_config_id` 替换为警报配置 ID。 通过密钥和终结点创建客户端，并使用 `client.list_anomalies` 列出要警报的异常。 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```
