---
title: Azure Monitor Application Insights Java
description: Java 应用程序的应用程序性能监视，无需修改代码即可在任何环境中运行。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 36e2b419da2bccdf2f5f13227457172cf644994c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351531"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java 无代码置备应用程序监视 Azure Monitor Application Insights

Java 无代码应用程序监视只是为了简化操作 - 无需更改代码，只需更改几个配置即可启用 Java 代理。

 Java 代理可在任何环境中正常工作，并允许你监视所有 Java 应用程序。 换句话说，无论你是在 VM 上、本地、AKS 中还是在 Windows、Linux 上运行 Java 应用，不管什么位置，Java 3.0 代理都可以监视你的应用。

不再需要将 Application Insights Java SDK 添加到应用程序，因为3.0 代理会自动收集所有请求、依赖项和日志。

你仍可以从应用程序发送自定义遥测。 3.0 agent 将跟踪该代理，并将其与所有自动收集的遥测数据相关联。

3\.0 代理支持 Java 8 及更高版本。

## <a name="quickstart"></a>快速入门

**1.下载代理**

> [!WARNING]
> **如果要从3.0 预览版升级**
>
> 请仔细检查所有 [配置选项](./java-standalone-config.md) ，因为 json 结构已完全更改，而文件名本身则以全部小写。

下载 [applicationinsights-agent-3.0.0](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar)

**2.将 JVM 指向该代理**

将 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 添加到应用程序的 JVM 参数

典型的 JVM 参数包括 `-Xmx512m` 和 `-XX:+UseG1GC`。 如果你知道在何处添加这些参数，则你已知道要将此项添加到何处。

有关配置应用程序的 JVM 参数的其他帮助，请参阅 [更新 JVM 参数的技巧](./java-standalone-arguments.md)。

**3.将代理指向 Application Insights 资源**

如果还没有 Application Insights 资源，可以按照[资源创建指南](./create-new-resource.md)中的步骤创建一个新资源。

通过设置环境变量，将代理指向 Application Insights 资源：

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

另一种方法是创建一个名为 `applicationinsights.json` 的配置文件，并将其置于 `applicationinsights-agent-3.0.0.jar` 所在的目录中，该文件包含以下内容：

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
*  (预览版的遥测处理器) 
* 自动收集的日志记录
* 自动收集的 Micrometer 指标 (包括弹簧 Boot 制动器指标) 
* 检测信号
* HTTP 代理
* 自我诊断

有关完整详细信息，请参阅 [配置选项](./java-standalone-config.md) 。

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>自动收集的请求、依赖项、日志和指标

### <a name="requests"></a>请求

* JMS 使用者
* Kafka 使用者
* Netty/WebFlux
* Servlet
* Spring 计划

### <a name="dependencies-with-distributed-trace-propagation"></a>与分布式跟踪传播的依赖关系

* Apache HttpClient 和 HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Netty 客户端
* OkHttp

### <a name="other-dependencies"></a>其他依赖项

* Cassandra
* JDBC
* MongoDB（异步和同步）
* Redis（Lettuce 和 Jedis）

### <a name="logs"></a>日志

* java.util.logging
* Log4j（包含 MDC 属性）
* SLF4J/Logback（包含 MDC 属性）

### <a name="metrics"></a>指标

* Micrometer（包括 Spring Boot Actuator 指标）
* JMX 指标

## <a name="sending-custom-telemetry-from-your-application"></a>从应用程序发送自定义遥测

我们在 3.0+ 版本中的目标是让你能够使用标准 API 发送自定义遥测。

我们支持 Micrometer、OpenTelemetry API 和常用日志记录框架。 Application Insights Java 3.0 将自动捕获遥测数据，并将其与所有自动收集的遥测相关联。

### <a name="supported-custom-telemetry"></a>支持的自定义遥测

下表显示了当前支持的自定义遥测类型，你可以使用它们来对 Java 3.0 代理进行补充。 总而言之，通过 Micrometer 可以支持自定义指标，通过记录框架可以启用自定义异常和跟踪，通过 [Application Insights Java 2.x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x) 可以支持任何类型的自定义遥测。 

|                     | Micrometer | Log4j、logback、JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **自定义事件**   |            |                     |  是    |
| **自定义指标**  |  是       |                     |  是    |
| **依赖项**    |            |                     |  是    |
| **异常**      |            |  是                |  是    |
| **页面视图**      |            |                     |  是    |
| **请求**        |            |                     |  是    |
| **跟踪**          |            |  是                |  是    |

我们目前不打算发布带有 Application Insights 3.0 的 SDK。

Application Insights Java 3.0 已在侦听发送到 Application Insights Java SDK 2.x 的遥测。 对于现有 2.x 用户来说，此功能是升级过程中的一个重要部分。在 OpenTelemetry API 正式发布之前，它填补了我们在自定义遥测支持方面的一个重要空白。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>使用 Application Insights Java SDK 2.x 发送自定义遥测

将 `applicationinsights-core-2.6.0.jar` 添加到应用程序（Application Insights Java 3.0 支持所有 2.x 版本，但如果你可以选择，最好使用最新版本）：

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

创建 TelemetryClient：

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

并使用它发送自定义遥测。

### <a name="events"></a>事件

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>指标

可以通过 [Micrometer](https://micrometer.io) 发送指标遥测：

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

也可以使用 Application Insights Java SDK 2.x：

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>依赖项

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>日志
可以通过常用的日志记录框架发送自定义日志遥测。

也可以使用 Application Insights Java SDK 2.x：

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>异常
可以通过常用的日志记录框架发送自定义异常遥测。

也可以使用 Application Insights Java SDK 2.x：

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```
