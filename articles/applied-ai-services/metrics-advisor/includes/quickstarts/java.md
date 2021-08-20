---
title: 指标顾问 Java 快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 07/07/2021
ms.author: mbullwin
ms.openlocfilehash: e7c922e891ba744b8d74407069c3b0472e96db4b
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713657"
---
[参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [项目 (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 获得 Azure 订阅后，<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="“创建指标顾问”资源"  target="_blank">在 Azure 门户中创建“指标顾问”资源 </a>，以部署“指标顾问”实例。  
* 带有时序数据的 SQL 数据库。
  
  
> [!TIP]
> * 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) 上找到 Java 指标顾问示例。
> * 指标顾问资源可能需要 10 到 30 分钟才能部署一个服务实例供你使用。 部署成功后，单击“转到资源”。 部署完成后，可以通过 Web 门户和 REST API 这两种方式开始使用指标顾问实例。 
> * 可以在 Azure 门户的资源“概述”部分中找到 REST API 的 URL。 它将如下所示：
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>设置

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

本快速入门使用 Gradle 依赖项管理器。 可在 [Maven 中央存储库](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)中找到有关客户端库的详细信息。

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

### <a name="install-the-client-library"></a>安装客户端库

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后将以下生成配置复制到其中。 请确保包含项目依赖项。

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0")
}
```

### <a name="create-a-java-file"></a>创建 Java 文件

为示例应用创建一个文件夹。 在工作目录中运行以下命令：

```console
mkdir -p src/main/java
```

导航到新文件夹，并创建名为 MetricsAdvisorQuickstarts.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) 上找到它，其中包含此快速入门中的代码示例。

在应用程序的 `MetricsAdvisorQuickstarts` 类中，为资源的密钥和终结点创建变量。


> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的指标顾问资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 <br><br>若要检索 API 密钥，必须转到 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 为资源选择相应的“目录”、“订阅”和“工作区”，然后选择“入门”   。 然后，你就能够从 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 检索 API 密钥。   
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

在应用程序的 `Main()` 方法中，添加对本快速入门中使用的方法的调用。 稍后将创建这些内容。

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>对象模型

以下类处理指标顾问 Java SDK 的某些主要功能。

|名称|说明|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | 用途： <br> - 列出异常事件 <br> - 列出事件的根本原因 <br> - 检索原始时序数据和由服务丰富的时序数据。 <br> - 列出警报 <br> - 添加反馈以优化模型 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| 允许你： <br> - 管理数据馈送 <br> - 配置异常情况检测配置 <br> - 配置异常情况警报配置 <br> - 管理挂钩  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0/com/azure/ai/metricsadvisor/administration/models/DataFeed.html) | 指标顾问从数据源中引入的内容。`DataFeed` 包含以下行： <br> - 时间戳 <br> - 零维度或多个维度 <br> - 一个或多个度量值  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0/com/azure/ai/metricsadvisor/administration/models/DataFeedMetric.html) | `DataFeedMetric` 是用于监视和评估特定业务流程状态的可计量度量值。 它可以是划分为多个维度的多个时序值的组合。 例如，Web 运行状况指标可能包含用户数和 en-us 市场的维度  。 |

## <a name="code-examples"></a>代码示例

这些代码片段展示如何借助适用于 Java 的指标顾问客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [添加数据馈送](#add-a-data-feed)
* [检查引入状态](#check-the-ingestion-status)
* [配置异常情况检测](#configure-anomaly-detection)
* [创建挂钩](#create-a-hook)
* [创建警报配置](#create-an-alert-configuration)
* [查询警报](#query-the-alert)

## <a name="authenticate-the-client"></a>验证客户端

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>使用 MetricsAdvisorKeyCredential 创建指标顾问客户端

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>使用 MetricsAdvisorKeyCredential 创建指标管理客户端

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>添加数据馈送

将 `sql_server_connection_string` 替换为你自己的 SQL Server 连接字符串，并将 `query` 替换为在单个时间戳返回数据的查询。 你还需要根据自定义数据调整 `DataFeedMetric` 和 `DataFeedDimension` 值。

> [!IMPORTANT]
> 对于每个维度组合，查询应在每个时间戳处最多返回一条记录。 查询返回的所有记录必须具有相同的时间戳。 指标顾问将针对每个时间戳运行此查询以引入数据。 有关详细信息和示例，请参阅[教程：编写有效的查询](../../tutorials/write-a-valid-query.md)。

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("conn-string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric("cost"),
            new DataFeedMetric("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension("city"),
            new DataFeedDimension("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SqlServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>检查引入状态

此示例检查以前提供的数据馈送源的引入状态。

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>配置异常情况检测 

此示例演示用户如何为其数据配置异常情况检测配置。

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition(
        20, 
        10, 
        true, 
        AnomalyDetectorDirection.BOTH, 
        new SuppressCondition(1, 2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition(
        AnomalyDetectorDirection.DOWN, 
        new SuppressCondition(1, 1))
    .setLowerBound(5.0);

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition(
        10.0, 
        AnomalyDetectorDirection.UP,
        new SuppressCondition(1, 2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setConditionOperator(DetectionConditionOperator.OR))
    );
```

## <a name="create-a-hook"></a>创建挂钩

此示例创建一个接收异常事件警报的电子邮件挂钩。

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>创建警报配置

此示例演示用户如何为其数据中检测到的异常配置警报配置。

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition(AnomalySeverity.HIGH,
                                AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>查询警报

此示例演示用户如何查询警报检测配置触发的警报，并获取该警报的异常。

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

可使用以下命令生成应用：

```console
gradle build
```
### <a name="run-the-application"></a>运行应用程序

使用 `run` 目标运行应用程序：

```console
gradle run
```
