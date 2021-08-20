---
title: 指标顾问 C# 快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 07/07/2021
ms.author: mbullwin
ms.openlocfilehash: 02aa150960436317a8307998eb91883e906249ed
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341431"
---
[参考文档](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src) | [包 (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。
* 获得 Azure 订阅后，<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="“创建指标顾问”资源"  target="_blank">在 Azure 门户中创建“指标顾问”资源 </a>，以部署“指标顾问”实例。  
* 带有时序数据的 SQL 数据库。
   
> [!TIP]
> * 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md) 上找到 .NET 指标顾问示例。
> * 指标顾问资源可能需要 10 到 30 分钟才能部署一个服务实例供你使用。 部署成功后，单击“转到资源”。 部署完成后，可以通过 Web 门户和 REST API 这两种方式开始使用指标顾问实例。 
> * 可以在 Azure 门户的资源“概述”部分中找到 REST API 的 URL。 它将如下所示：
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库 

创建新项目后，右键单击“解决方案资源管理器”中的项目解决方案，然后选择“管理 NuGet 包”，以安装客户端库 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `Azure.AI.MetricsAdvisor`。 选择版本 `1.0.0`，然后选择“安装”。 

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `metrics-advisor-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。 

```console
dotnet new console -n metrics-advisor-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安装客户端库 

如果你使用的 IDE 而不是 Visual Studio，则可通过以下命令安装适用于 .NET 的指标顾问客户端库：

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples) 上找到它，其中包含此快速入门中的代码示例。

从项目目录中，打开 Program.cs 文件，并添加以下 `using` 指令：

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

在应用程序的 `Main()` 方法中，添加对本快速入门中使用的方法的调用。 稍后将创建这些内容。

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>对象模型

以下类处理指标顾问 C# SDK 的某些主要功能。

|名称|说明|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | 用途： <br> - 列出事件 <br> - 列出事件的根本原因 <br> - 检索原始时序数据和由服务丰富的时序数据。 <br> - 列出警报 <br> - 添加反馈以优化模型 |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| 允许你： <br> - 管理数据馈送 <br> - 配置异常情况检测配置 <br> - 配置异常情况警报配置 <br> - 管理挂钩  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| 指标顾问从数据源中引入的内容。`DataFeed` 包含以下行： <br> - 时间戳 <br> - 零维度或多个维度 <br> - 一个或多个度量值  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| `DataFeedMetric` 是用于监视和评估特定业务流程状态的可计量度量值。 它可以是划分为多个维度的多个时序值的组合。 例如，Web 运行状况指标可能包含用户数和 en-us 市场的维度  。 |

## <a name="code-examples"></a>代码示例

这些代码片段展示如何借助适用于 .NET 的指标顾问客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [添加数据馈送](#add-a-data-feed)
* [检查引入状态](#check-the-ingestion-status)
* [配置异常情况检测](#configure-anomaly-detection)
* [创建挂钩](#create-a-hook)
* [创建警报配置](#create-an-alert-configuration)
* [查询警报](#query-the-alert)

## <a name="authenticate-the-client"></a>验证客户端

在应用程序的 `Program` 类中，为资源的密钥和终结点创建变量。

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的指标顾问资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 <br><br>若要检索 API 密钥，必须转到 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 为资源选择相应的“目录”、“订阅”和“工作区”，然后选择“入门”   。 然后，你就能够从 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 检索 API 密钥。   
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

获得订阅和 API 密钥后，请创建一个 MetricsAdvisorKeyCredential。 借助终结点和密钥凭据，可以创建 [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs)：

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

你还可创建 [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) 来执行管理操作：

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>添加数据馈送

指标顾问支持多种类型的数据源。 在此示例中，我们将演示如何创建一个从 SQL Server 提取数据的 `DataFeed`。 

将 `connection_String` 替换为你自己的 SQL Server 连接字符串，并将 `query` 替换为在单个时间戳返回数据的查询。 你还需要根据自定义数据调整 `DataFeedMetric` 和 `DataFeedDimension` 值。

> [!IMPORTANT]
> 对于每个维度组合，查询应在每个时间戳处最多返回一条记录。 查询返回的所有记录必须具有相同的时间戳。 指标顾问将针对每个时间戳运行此查询以引入数据。 有关详细信息和示例，请参阅[教程：编写有效的查询](../../tutorials/write-a-valid-query.md)。


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed(dataFeedName, dataFeedSource, dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.AdministratorEmails)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>检查引入状态

检查以前创建的 `DataFeed` 的引入状态

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    MaxPageSize = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>配置异常情况检测 

创建异常情况检测配置，告诉服务应将哪些数据点视为异常。

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>创建挂钩

指标顾问支持 `EmailNotificationHook` 和 `WebNotificationHook` 类作为警报通知的订阅方法。 在此示例中，我们将演示如何创建 `EmailNotificationHook`。 需要将挂钩传递到异常情况警报配置以开始获取通知。 有关详细信息，请参阅[创建异常情况警报配置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration)这一示例。

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>创建警报配置

创建一个 `AnomalyAlertConfiguration`，告诉服务哪些异常会触发警报。

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>查询警报

查看由给定异常警报配置创建的警报。

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    MaxPageSize = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

知道警报的 ID 后，列出触发此警报的[异常](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly)。

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { MaxPageSize = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run
```
