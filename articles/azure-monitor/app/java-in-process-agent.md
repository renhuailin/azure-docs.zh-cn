---
title: Azure Monitor Application Insights Java
description: 无需修改代码即可监视在任何环境中运行的 Java 应用程序的应用程序性能。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 9ebfaea28e249af5f8ecd140e08178398f38fd6f
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858287"
---
# <a name="azure-monitor-opentelemetry-based-auto-instrumentation-for-java-applications"></a>适用于 Java 应用程序的 Azure Monitor 基于 OpenTelemetry 的自动检测

本文介绍如何启用和配置基于 OpenTelemetry 的 Azure Monitor Java 产品/服务。 按照本文说明执行操作后，你将能够使用 Azure Monitor Application Insights 来监视应用程序。

## <a name="get-started"></a>入门
无需更改任何代码即可启用 Java 自动检测。

### <a name="prerequisites"></a>先决条件
- 使用版本 8+ 的 Java 应用程序
- Azure 订阅 - [免费创建 Azure 订阅](https://azure.microsoft.com/free/)
- Application Insights 资源 - [创建 Application Insights 资源](create-workspace-resource.md#create-workspace-based-resource)

### <a name="enable-azure-monitor-application-insights"></a>启用 Azure Monitor Application Insights
1.下载自动检测 jar 文件

#### <a name="1-download-jar-file"></a>1.下载 jar 文件

下载 [applicationinsights-agent-3.2.0.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.0/applicationinsights-agent-3.2.0.jar) 文件。

> [!WARNING]
> 
> - 如果要从 3.0 预览版升级
>
>    请仔细检查所有的[配置选项](./java-standalone-config.md)，因为除了文件名本身全部变为小写外，json 结构也已完全改变。
> 
> - 如果要从 3.0.x 升级
> 
>    操作名称和请求遥测名称现在以 http 方法为前缀（`GET`、`POST` 等等）。
>    如果自定义仪表板或警报依赖于以前的值，则可能会影响这些仪表板或警报。
>    有关更多详细信息，请参阅 [3.1.0 发行说明](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0)。
>
> - 如果要从 3.1.x 升级
> 
>    数据库依赖项名称现在更简洁，`data` 字段中仍显示完整（净化）的查询。 HTTP 依赖项名称现在更具描述性。
>    如果自定义仪表板或警报依赖于以前的值，则可能会影响这些仪表板或警报。
>    有关更多详细信息，请参阅 [3.2.0 发行说明](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0)。

#### <a name="2-point-the-jvm-to-the-jar-file"></a>2.将 JVM 指向 jar 文件

将 `-javaagent:path/to/applicationinsights-agent-3.2.0.jar` 添加到应用程序的 JVM 参数。 

> [!TIP]
> 有关配置应用程序的 JVM 参数的帮助，请参阅[更新 JVM 参数的技巧](./java-standalone-arguments.md)。

#### <a name="3-set-application-insights-connection-string"></a>3.设置 Application Insights 连接字符串

通过设置环境变量，将 jar 文件指向 Application Insights 资源：

```console
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

另一种方法是创建一个名为 `applicationinsights.json` 的配置文件，并将其置于 `applicationinsights-agent-3.2.0.jar` 所在的目录中，该文件包含以下内容：

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

在 Application Insights 资源上查找连接字符串。

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::

#### <a name="4-confirm-data-is-flowing"></a>4.确认有数据流

运行应用程序，然后打开 Azure 门户中的“Application Insights 资源”选项卡。 门户中显示数据可能需要数分钟。

> [!NOTE]
> 如果无法运行应用程序或未如预期获取数据，请转到[故障排除](#troubleshooting)。

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="“Application Insights 概述”选项卡的屏幕截图，其中突出显示服务器请求和服务器响应时间。":::

> [!IMPORTANT]
> 如果有两个或多个服务向同一 Application Insights 资源发出遥测数据，则需要[设置云角色名称](java-standalone-config.md#cloud-role-name)以在应用程序映射中正确表示这些服务。


## <a name="configuration-options"></a>配置选项

在 `applicationinsights.json` 文件中，还可以配置以下内容：

* 云角色名称
* 云角色实例
* 采样
* JMX 指标
* 自定义维度
* 遥测处理器（预览版）
* 自动收集的日志记录
* 自动收集的 Micrometer 指标（包括 Spring Boot Actuator 指标）
* 检测信号
* HTTP 代理
* 自我诊断

如需完整的详细信息，请参阅[配置选项](./java-standalone-config.md)。

## <a name="instrumentation-libraries"></a>检测库

Java 3.X 包括以下检测库。

### <a name="auto-collected-requests"></a>自动收集的请求

* JMS 使用者
* Kafka 使用者
* Netty/WebFlux
* Servlet
* Spring 计划

### <a name="auto-collected-dependencies"></a>自动收集的依赖项

自动收集的依赖项和下游分布式跟踪传播：

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS Client
* Jetty HttpClient
* JMS
* Kafka
* Netty 客户端
* OkHttp

自动收集的依赖项（无下游分布式跟踪传播）：

* Cassandra
* JDBC
* MongoDB（异步和同步）
* Redis（Lettuce 和 Jedis）

### <a name="auto-collected-logs"></a>自动收集的日志

* java.util.logging
* Log4j（包含 MDC 属性）
* SLF4J/Logback（包含 MDC 属性）

### <a name="auto-collected-metrics"></a>自动收集的指标

* Micrometer（包括 Spring Boot Actuator 指标）
* JMX 指标

### <a name="azure-sdks-preview"></a>Azure SDK（预览版）

默认情况下，自动收集以下 Azure SDK 发出的遥测数据：

* [应用程序配置](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [认知搜索](/java/api/overview/azure/search-documents-readme) 11.3.0+
* [通信聊天](/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [通信通用](/java/api/overview/azure/communication-common-readme) 1.0.0+
* [通信标识](/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [通信电话号码](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0+
* [通信短信](/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [数字孪生 - 核心](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0+
* [事件网格](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [事件中心](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [事件中心 - Azure Blob 存储检查点存储](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [表单识别器](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [标识](/java/api/overview/azure/identity-readme) 1.2.4+
* [密钥保管库 - 证书](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [密钥保管库 - 密钥](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [密钥保管库 - 机密](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [服务总线](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [存储 - Blob](/java/api/overview/azure/storage-blob-readme) 12.11.0+
* [存储 - Blob 批处理](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0+
* [存储 - Blob 加密](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0+
* [存储 - 通用](/java/api/overview/azure/storage-common-readme) 12.11.0+
* [存储 - 文件数据湖](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0+
* [存储 - 文件共享](/java/api/overview/azure/storage-file-share-readme) 12.9.0+
* [存储 - 队列](/java/api/overview/azure/storage-queue-readme) 12.9.0+
* [文本分析](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "从 https://azure.github.io/azure-sdk/releases/latest/java.html 抓取的上述名称和链接"
[//]: # "与构建在 azure-core 1.14.0 上的 maven central 的最早版本手动执行版本同步"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "   继续"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="modify-telemetry"></a>修改遥测

### <a name="add-span-attributes"></a>添加范围属性
可以使用 `opentelemetry-api` 向范围添加属性。 这些属性可能包括向遥测添加自定义业务维度。 还可以使用属性设置 Application Insights 架构中的可选字段，如用户 ID 或客户端 IP。

#### <a name="add-custom-dimension"></a>添加自定义维度
添加一个或多个自定义维度将填充请求、依赖项和/或异常表中的 _customDimensions_ 字段。

> [!NOTE]
> 此功能仅在 3.2.0 及更高版本中提供

向应用程序添加 `opentelemetry-api-1.6.0.jar`

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

并在代码中添加自定义维度：

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("mycustomdimension", "myvalue1");
```

#### <a name="set-user-id"></a>设置用户 ID
填充请求、依赖项和/或异常表中的用户 ID 字段。

> [!IMPORTANT]
> 在设置经过身份验证的用户 ID 之前，请参考适用的隐私法律。

> [!NOTE]
> 此功能仅在 3.2.0 及更高版本中提供

向应用程序添加 `opentelemetry-api-1.6.0.jar`

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

并在代码中设置 `user_Id`：

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("enduser.id", "myuser");
```

### <a name="get-trace-id-or-span-id"></a>获取跟踪 ID 或范围 ID

可以使用 `opentelemetry-api` 获取跟踪 ID 或范围 ID。 这样做的目的是将这些标识符添加到现有日志记录遥测中，以便在调试和诊断问题时改善关联性。

> [!NOTE]
> 此功能仅在 3.2.0 及更高版本中提供

向应用程序添加 `opentelemetry-api-1.6.0.jar`

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

并在代码中获取请求跟踪 ID 和范围 ID：

```java
import io.opentelemetry.api.trace.Span;

String traceId = Span.current().getSpanContext().getTraceId();
String spanId = Span.current().getSpanContext().getSpanId();
```
## <a name="custom-telemetry"></a>自定义遥测

我们在 Application Insights Java 3.x 版本中的目标是让你能够使用标准 API 发送自定义遥测。

到目前为止，我们支持 Micrometer、热门的日志记录框架和 Application Insights Java 2.x SDK。
Application Insights Java 3.x 会自动捕获通过这些 API 发送的遥测，并将其与自动收集的遥测相关联。

### <a name="supported-custom-telemetry"></a>支持的自定义遥测

下表显示了当前支持的自定义遥测类型，你可以使用它们来对 Java 3.x 代理进行补充。 总而言之，通过 Micrometer 可以支持自定义指标，通过日志记录框架可以启用自定义异常和跟踪，通过 `opentelemetry-api` 可以启用自定义请求、依赖项和异常，通过 [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk) 可以支持任何类型的自定义遥测。

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| **自定义事件**   |            |                     |  是    |                   |
| **自定义指标**  |  是       |                     |  是    |                   |
| **依赖项**    |            |                     |  是    |  是              |
| **异常**      |            |  是                |  是    |  是              |
| **页面视图**      |            |                     |  是    |                   |
| **请求**        |            |                     |  是    |  是              |
| **跟踪**          |            |  是                |  是    |                   |

我们目前不打算发布带有 Application Insights 3.x 的 SDK。

Application Insights Java 3.x 已在侦听发送到 Application Insights Java 2.x SDK 的遥测。 对于现有 2.x 用户来说，此功能是升级过程中的一个重要部分。在 OpenTelemetry API 正式发布之前，它填补了我们在自定义遥测支持方面的一个重要空白。

### <a name="send-custom-metrics-using-micrometer"></a>使用 Micrometer 发送自定义指标

将 Micrometer 添加到应用程序：

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

使用 Micrometer [全局注册表](https://micrometer.io/docs/concepts#_global_registry)来创建计量：

```java
static final Counter counter = Metrics.counter("test_counter");
```

并使用它来记录指标：

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>使用你喜爱的日志记录框架发送自定义跟踪和异常

系统自动检测 Log4j、Logback 和 java.util.logging，通过这些日志记录框架执行的日志记录会作为跟踪和异常遥测自动收集。

默认情况下，仅当在 INFO 级别或更高级别执行日志记录时，才收集该日志记录。
有关如何更改此级别的信息，请参阅[配置选项](./java-standalone-config.md#auto-collected-logging)。

如果要将自定义维度附加到日志，可以使用 [Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html)、[Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) 或 [Log4j 2 MDC](http://logback.qos.ch/manual/mdc.html)，Application Insights Java 3.x 将自动捕获这些 MDC 属性作为跟踪和异常遥测的自定义维度。

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>使用 2.x SDK 发送自定义遥测

将 `applicationinsights-core-2.6.3.jar` 添加到应用程序（Application Insights Java 3.x 支持所有 2.x 版本，但如果你可以选择，最好使用最新版本）：

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.3</version>
</dependency>
```

创建 TelemetryClient：

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

并使用它发送自定义遥测：

##### <a name="events"></a>事件

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>指标

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>依赖项

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>日志

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>异常

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

## <a name="troubleshooting"></a>疑难解答
请参阅 [疑难解答](java-standalone-troubleshoot.md)。

## <a name="support"></a>支持
- 请查看[故障排除步骤](java-standalone-troubleshoot.md)。
- 有关 Azure 支持问题，可打开 [Azure 支持票证](https://azure.microsoft.com/support/create-ticket/)。
- 有关 OpenTelemetry 问题，请直接与 [OpenTelemetry 社区](https://opentelemetry.io/community/)联系。

## <a name="opentelemetry-feedback"></a>OpenTelemetry 反馈
- 填写 OpenTelemetry 社区的[客户反馈调查](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform)。
- 加入 [OpenTelemetry 早期采用者社区](https://aka.ms/AzMonOTel/)告诉 Microsoft 有关你的信息。
- 在 [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)与其他 Azure Monitor 用户联系。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Monitor Java 自动检测 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-Java)中查看源代码。
- 若要详细了解 OpenTelemetry 及其社区，请访问 [OpenTelemetry Java GitHub 存储库](https://github.com/open-telemetry/opentelemetry-java-instrumentation)。
- [启用 Web/浏览器用户监视](javascript.md)，以进行使用体验。
