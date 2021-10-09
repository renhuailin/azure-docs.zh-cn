---
title: Azure Monitor Application Insights Java
description: 无需修改代码即可监视在任何环境中运行的 Java 应用程序的应用程序性能。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 06/24/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 900430b1b1897479b4551c9e12e28ad87eaf0ad9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642370"
---
# <a name="java-codeless-application-monitoring-with-azure-monitor-application-insights"></a>使用 Azure Monitor Application Insights 进行 Java 无代码应用程序监视

> [!NOTE]
> 如果要查找旧的 2.x 文档，请转到[此处](./java-2x-get-started.md)。

Java 无代码应用程序监视只是为了简化操作 - 无需更改代码，只需更改几个配置即可启用 Java 代理。

Java 代理可在任何环境中正常工作，并允许你监视所有 Java 应用程序。 换句话说，无论你是在 VM 上、本地、AKS 中还是在 Windows、Linux 上运行 Java 应用，不管什么位置，Application Insights Java 代理都可以监视你的应用。

不再需要将 Application Insights Java 2.x SDK 添加到你的应用程序，因为 Application Insights Java 3.x 代理会自动收集请求、依赖项并自行记录所有内容。

你仍可以从应用程序发送自定义遥测。
3\.x 代理会跟踪它并将它与所有自动收集的遥测数据相关联。

3\.x 代理支持 Java 8 及更高版本。

## <a name="quickstart"></a>快速入门

**1.下载代理**

> [!WARNING]
> **如果要从 3.0 预览版升级**
>
> 请仔细检查所有的[配置选项](./java-standalone-config.md)，因为除了文件名本身全部变为小写外，json 结构也已完全改变。

> [!WARNING]
> **如果要从 3.0.x 升级**
>
> 操作名称和请求遥测名称现在以 http 方法为前缀（`GET`、`POST` 等等）。
> 如果自定义仪表板或警报依赖于以前的无前缀的值，则可能会影响这些仪表板或警报。
> 有关更多详细信息，请参阅 [3.1.0 发行说明](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0)。

下载 [applicationinsights-agent-3.1.1.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.1.1/applicationinsights-agent-3.1.1.jar)

**2.将 JVM 指向该代理**

将 `-javaagent:path/to/applicationinsights-agent-3.1.1.jar` 添加到应用程序的 JVM 参数。 

有关配置应用程序的 JVM 参数的帮助，请参阅[更新 JVM 参数的技巧](./java-standalone-arguments.md)。

**3.将代理指向 Application Insights 资源**

如果还没有 Application Insights 资源，可以按照[资源创建指南](./create-new-resource.md)中的步骤创建一个新资源。

通过设置环境变量，将代理指向 Application Insights 资源：

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

另一种方法是创建一个名为 `applicationinsights.json` 的配置文件，并将其置于 `applicationinsights-agent-3.1.1.jar` 所在的目录中，该文件包含以下内容：

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

可以在 Application Insights 资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::

**4.就这么简单！**

现在启动应用程序，并访问 Azure 门户中的 Application Insights 资源以查看监视数据。

> [!NOTE]
> 监视数据可能需要几分钟时间才能在门户中显示。


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

## <a name="auto-collected-requests"></a>自动收集的请求

* JMS 使用者
* Kafka 使用者
* Netty/WebFlux
* Servlet
* Spring 计划

## <a name="auto-collected-dependencies"></a>自动收集的依赖项

自动收集的依赖项和下游分布式跟踪传播：

* Apache HttpClient 和 HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Netty 客户端
* OkHttp

自动收集的依赖项（无下游分布式跟踪传播）：

* Cassandra
* JDBC
* MongoDB（异步和同步）
* Redis（Lettuce 和 Jedis）

## <a name="auto-collected-logs"></a>自动收集的日志

* java.util.logging
* Log4j（包含 MDC 属性）
* SLF4J/Logback（包含 MDC 属性）

## <a name="auto-collected-metrics"></a>自动收集的指标

* Micrometer（包括 Spring Boot Actuator 指标）
* JMX 指标

## <a name="azure-sdks-preview"></a>Azure SDK（预览版）

请参阅[配置选项](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview)以启用此预览功能，并自动收集这些 Azure SDK 所发出的遥测数据：

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

## <a name="send-custom-telemetry-from-your-application"></a>从应用程序发送自定义遥测

我们在 Application Insights Java 3.x 版本中的目标是让你能够使用标准 API 发送自定义遥测。

到目前为止，我们支持 Micrometer、热门的日志记录框架和 Application Insights Java 2.x SDK。
Application Insights Java 3.x 会自动捕获通过这些 API 发送的遥测，并将其与自动收集的遥测相关联。

### <a name="supported-custom-telemetry"></a>支持的自定义遥测

下表显示了当前支持的自定义遥测类型，你可以使用它们来对 Java 3.x 代理进行补充。 总而言之，通过 Micrometer 可以支持自定义指标，通过记录框架可以启用自定义异常和跟踪，通过 [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk) 可以支持任何类型的自定义遥测。

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **自定义事件**   |            |                     |  是    |
| **自定义指标**  |  是       |                     |  是    |
| **依赖项**    |            |                     |  是    |
| **异常**      |            |  是                |  是    |
| **页面视图**      |            |                     |  是    |
| **请求**        |            |                     |  是    |
| **跟踪**          |            |  是                |  是    |

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

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>使用 2.x SDK 添加请求自定义维度

> [!NOTE]
> 此功能仅在 3.0.2 及更高版本中提供

将 `applicationinsights-web-2.6.3.jar` 添加到应用程序（Application Insights Java 3.x 支持所有 2.x 版本，但如果你可以选择，最好使用最新版本）：

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

并在代码中添加自定义维度：

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>使用 2.x SDK 设置请求遥测 user_Id

> [!NOTE]
> 此功能仅在 3.0.2 及更高版本中提供

将 `applicationinsights-web-2.6.3.jar` 添加到应用程序（Application Insights Java 3.x 支持所有 2.x 版本，但如果你可以选择，最好使用最新版本）：

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

并在代码中设置 `user_Id`：

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>使用 2.x SDK 替代请求遥测名称

> [!NOTE]
> 此功能仅在 3.0.2 及更高版本中提供

将 `applicationinsights-web-2.6.3.jar` 添加到应用程序（Application Insights Java 3.x 支持所有 2.x 版本，但如果你可以选择，最好使用最新版本）：

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

并在代码中设置名称：

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>使用 2.x SDK 获取请求遥测 ID 和操作 ID

> [!NOTE]
> 此功能仅在 3.0.3 及更高版本中提供

将 `applicationinsights-web-2.6.3.jar` 添加到应用程序（Application Insights Java 3.x 支持所有 2.x 版本，但如果你可以选择，最好使用最新版本）：

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

并在代码中获取请求遥测 ID 和操作 ID：

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
