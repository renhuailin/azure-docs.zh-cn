---
title: Java Web 应用性能监视 - Azure Application Insights
description: 使用 Application Insights 监视 Java 网站的扩展性能和使用情况。
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3489591a39901a79323faf307b8700863f71522a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103756"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>监视 Java Web 应用中的依赖项、捕获的异常和方法执行时间

> [!CAUTION]
> 本文档适用于不再推荐使用的 Application Insights Java 2.x。
>
> 有关最新版本的文档，可参阅 [Application Insights Java 3.x](./java-in-process-agent.md)。

如果已[使用 Application Insights SDK 检测了 Java Web 应用][java]，则无需更改任何代码，就能使用 Java 代理来获取更深入的见解：

* **依赖项：** 有关应用程序对其他组件的调用的数据，包括：
  * 捕获通过 Apache HttpClient、OkHttp 和 `java.net.HttpURLConnection` 进行的 **传出 HTTP 调用**。
  * 捕获通过 Jedis 客户端进行的 **Redis 调用**。
  * **JDBC 查询** - 对于 MySQL 和 PostgreSQL，如果调用花费的时间长于 10 秒，代理将报告查询计划。

* **应用程序日志记录：** 捕获应用程序日志并将其与 HTTP 请求和其他遥测数据相关联
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **更好的操作命名：**（用于在门户中对请求进行聚合）
  * **Spring** - 基于 `@RequestMapping`。
  * **JAX-RS** - 基于 `@Path`。 

若要使用 Java 代理，请在服务器上安装该代理。 必须使用 [Application Insights Java SDK][java] 检测 Web 应用。 

## <a name="install-the-application-insights-agent-for-java"></a>安装适用于 Java 的 Application Insights 代理
1. 在运行 Java 服务器的计算机上[下载 2.x 代理](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2)。 请确保所使用的 2.x Java 代理版本与所使用的 2.x Application Insights Java SDK 版本相匹配。
2. 编辑应用程序服务器启动脚本，并添加以下 JVM 参数：
   
    `-javaagent:<full path to the agent JAR file>`
   
    例如，在 Linux 计算机上的 Tomcat 中：
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 重新启动应用程序服务器。

## <a name="configure-the-agent"></a>配置代理
创建名为 `AI-Agent.xml` 的文件，并将它放在代理 JAR 文件所在的同一个文件夹中。

设置 xml 文件的内容。 编辑以下示例，根据需要包含或省略功能。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>其他配置 (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

对于 Azure 应用服务，请执行以下操作：

* 选择“设置”>“应用程序设置”
* 在“应用设置”下添加新的键/值对：

注册表项：`JAVA_OPTS` 值：`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

代理必须打包为项目中的资源，以便它最终位于 D:/home/site/wwwroot/ 目录中。 可以通过转到“开发工具” > “高级工具” > “调试控制台”并查看站点目录的内容，确认你的代理处于正确的应用服务目录中。    

* 保存设置并重启应用。 （这些步骤仅适用于 Windows 上运行的应用程序服务。）

> [!NOTE]
> AI-Agent.xml 和代理 jar 文件应位于同一文件夹中。 它们通常一起放在该项目的 `/resources` 文件夹中。  

#### <a name="enable-w3c-distributed-tracing"></a>启用 W3C 分布式跟踪

将以下代码添加到 AI-Agent.xml：

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> 默认情况下启用向后兼容性模式，并且 enableW3CBackCompat 参数是可选的，且仅在要将其关闭时使用。 

理想情况下，所有服务都已更新为支持 W3C 协议的较新版 SDK 时，就会出现这种情况。 强烈建议尽快迁移到提供 W3C 支持的新版 SDK。

请确保[传入](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps)和传出（代理）配置完全相同。

## <a name="view-the-data"></a>查看数据
在 Application Insights 资源中，聚合的远程依赖项和方法执行时间显示在[“性能”磁贴下][metrics]。

若要搜索依赖项、异常和方法报告的单个实例，请打开[搜索][diagnostic]。

[诊断依赖项问题 - 了解详细信息](./asp-net-dependencies.md#diagnosis)。

## <a name="questions-problems"></a>存在疑问？ 遇到问题？
* 没有数据？ [设置防火墙异常](./ip-addresses.md)
* [Java 故障排除](java-2x-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-in-process-agent.md
[javalogs]: java-2x-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

