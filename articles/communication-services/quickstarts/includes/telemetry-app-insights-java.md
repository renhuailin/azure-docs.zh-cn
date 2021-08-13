---
title: 包含文件
description: include 文件
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: 8de03cce67b5eba27d771fbc95d538366724481b
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292431"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](/azure/developer/java/fundamentals/java-jdk-install) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。
- 在 Azure 门户中创建 [Application Insights 资源](../../../azure-monitor/app/create-new-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

打开终端或命令窗口。 导航到要在其中创建 Java 应用程序的目录。 运行以下命令以从 maven-archetype-quickstart 模板生成 Java 项目。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

你会注意到，“生成”任务创建了与 `artifactId` 名称相同的目录。 在此目录下，src/main/java 目录包含项目源代码，`src/test/java directory` 包含测试源，`pom.xml` 文件是项目的项目对象模型 (POM)。

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-monitor-opentelemetry-exporter</artifactId>
      <version>1.0.0-beta.4</version>
    </dependency>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 导航到 /src/main/java/com/communication/quickstart 目录
1. 在编辑器中打开 App.java 文件
1. 替换 `System.out.println("Hello world!");` 语句
1. 添加 `import` 指令

使用以下代码以开始执行以下操作：

```java
package com.communication.quickstart;

import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.Tracer;
import io.opentelemetry.context.Scope;
import io.opentelemetry.sdk.OpenTelemetrySdk;
import io.opentelemetry.sdk.trace.SdkTracerProvider;
import io.opentelemetry.sdk.trace.export.SimpleSpanProcessor;
import com.azure.monitor.opentelemetry.exporter.*;
import com.azure.communication.identity.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Export Telemetry to Application Insights");
        // Quickstart code goes here
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>通过调用通信标识 SDK 设置遥测跟踪器

使用连接字符串初始化 `CommunicationIdentityClient`。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

```java

    CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<COMMUNICATION-RESOURCE-CONNECTION-STRING>")
        .buildClient();
```

首先，若要创建用于在 Azure Monitor 中跟踪请求的 span，必须创建一个 `AzureMonitorTraceExporter` 对象实例。 你需要提供 Application Insights 资源的连接字符串。

```java
    AzureMonitorTraceExporter exporter = new AzureMonitorExporterBuilder()
        .connectionString("<APPLICATION-INSIGHTS-CONNECTION-STRING>")
        .buildTraceExporter();
```

然后，此导出程序会允许你创建以下实例，以便进行请求跟踪。 创建 `AzureMonitorTraceExporter` 之后添加以下代码：

```java

    SdkTracerProvider tracerProvider = SdkTracerProvider.builder()
        .addSpanProcessor(SimpleSpanProcessor.create(exporter))
        .build();

    OpenTelemetrySdk openTelemetrySdk = OpenTelemetrySdk.builder()
        .setTracerProvider(tracerProvider)
        .buildAndRegisterGlobal();

    Tracer tracer = openTelemetrySdk.getTracer("Sample");
```
初始化跟踪器后，便可以创建将负责跟踪请求的 span。

```java
    Span span = tracer.spanBuilder("sample-span").startSpan();
    final Scope scope = span.makeCurrent();
    try {
        // Thread bound (sync) calls will automatically pick up the parent span and you don't need to pass it explicitly.
        client.createUser();
    } finally {
        span.end();
        scope.close();
    }
    Thread.sleep(10000);
```

## <a name="run-the-code"></a>运行代码

导航到包含 pom.xml 文件的目录，并使用以下 `mvn` 命令编译该项目。

```console
mvn compile
```

然后，生成包。

```console
mvn package
```

运行以下 `mvn` 命令以执行应用。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```