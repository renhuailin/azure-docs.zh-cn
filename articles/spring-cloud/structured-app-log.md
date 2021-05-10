---
title: Azure Spring Cloud 的结构化应用程序日志 | Microsoft Docs
description: 本文介绍了如何在 Azure Spring Cloud 中生成和收集结构化应用程序日志数据。
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6899edc25a55beff45d2058975008f7fe2c2bb9d
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886708"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Azure Spring Cloud 的结构化应用程序日志

本文介绍了如何在 Azure Spring Cloud 中生成和收集结构化应用程序日志数据。 Azure Spring Cloud 使用正确的配置通过 Log Analytics 提供有用的应用程序日志查询和分析。

## <a name="log-schema-requirements"></a>日志架构要求

若要改进日志查询体验，应用程序日志必须采用 JSON 格式，并遵循某个架构。 Azure Spring Cloud 使用此架构分析你的应用程序并流式传输到 Log Analytics。 

**JSON 架构要求：**

| Json 键      | Json 值类型|  必需 | Log Analytics 中的列| 说明 |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     是   | AppTimestamp    | UTC 格式的时间戳  |
| logger        | string      |     否    | 记录器          | logger                   |
| 级别         | string      |     否    | CustomLevel     | 日志级别                |
| 线程 (thread)        | string      |     否    | 线程          | 线程 (thread)                   |
| message       | string      |     否    | Message         | 日志消息              |
| stackTrace    | string      |     否    | StackTrace      | 异常堆栈跟踪    |
| exceptionClass| string      |     否    | ExceptionClass  | 异常类名称     |
| mdc           | 嵌套的 JSON |     否    |                 | 映射的诊断上下文|
| mdc.traceId   | string      |     否    | TraceId         |分布式跟踪的跟踪 ID|
| mdc.spanId    | string      |     否    | SpanId          |分布式跟踪的范围 ID |
|               |             |           |                 |                          |

* “timestamp”字段是必需的，并且应当采用 UTC 格式，所有其他字段都是可选的。
* “mdc”字段中的“traceId”和“spanId”用于跟踪。
* 在单个行中记录每条 JSON 记录。 

**日志记录示例** 

 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>生成遵循架构的 JSON 日志  

对于 Spring 应用程序，你可以使用常用的[日志记录框架](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)（例如 [logback](http://logback.qos.ch/) 和 [log4j2](https://logging.apache.org/log4j/2.x/)）生成预期的 JSON 日志格式。 

### <a name="log-with-logback"></a>使用 logback 进行记录 

使用 Spring Boot 入门版时，默认情况下将使用 logback。 对于 logback 应用，请使用 [logstash-encoder](https://github.com/logstash/logstash-logback-encoder) 生成 JSON 格式的日志。 Spring Boot 2.1+ 版支持此方法。 

此过程：

1. 在 `pom.xml` 文件中添加 logstash 依赖项。 

    ```xml
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. 更新 `logback-spring.xml` 配置文件以设置 JSON 格式。
    ```xml
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
                <providers>
                    <timestamp>
                        <fieldName>timestamp</fieldName>
                        <timeZone>UTC</timeZone>
                    </timestamp>
                    <loggerName>
                        <fieldName>logger</fieldName>
                    </loggerName>
                    <logLevel>
                        <fieldName>level</fieldName>
                    </logLevel>
                    <threadName>
                        <fieldName>thread</fieldName>
                    </threadName>
                    <nestedField>
                        <fieldName>mdc</fieldName>
                        <providers>
                            <mdc />
                        </providers>
                    </nestedField>
                    <stackTrace>
                        <fieldName>stackTrace</fieldName>
                    </stackTrace>
                    <message />
                    <throwableClassName>
                        <fieldName>exceptionClass</fieldName>
                    </throwableClassName>
                </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout" />
        </root>
    </configuration>
    ```
1. 使用带 `-spring` 后缀（如 `logback-spring.xml`）的日志记录配置文件时，可基于 Spring 活动配置文件设置日志记录配置。

    ```xml
    <configuration>
        <springProfile name="dev">
            <!-- JSON appender definitions for local development, in human readable format -->
            <include resource="org/springframework/boot/logging/logback/defaults.xml" />
            <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
            <root level="info">
                <appender-ref ref="CONSOLE" />
            </root>
        </springProfile>

        <springProfile name="!dev">
            <!-- JSON appender configuration from previous step, used for staging / production -->
            ...
        </springProfile>
    </configuration>
    ```
    
    对于本地开发，请使用 JVM 参数 `-Dspring.profiles.active=dev` 运行 Spring Cloud 应用程序，然后可看到人工可读的日志，而不是 JSON 格式的行。

### <a name="log-with-log4j2"></a>使用 log4j2 进行记录 

对于 log4j2 应用，请使用 [json-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) 生成 JSON 格式的日志。 Spring Boot 2.1+ 版支持此方法。

此过程：

1. 从 `spring-boot-starter` 中排除 `spring-boot-starter-logging`，在 `pom.xml` 文件中添加依赖项 `spring-boot-starter-log4j2`、`log4j-layout-template-json`。

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-layout-template-json</artifactId>
        <version>2.14.0</version>
    </dependency>
    ```

2. 在类路径中准备一个 JSON 布局模板文件 `jsonTemplate.json`。

    ```json
    {
        "mdc": {
            "$resolver": "mdc"
        },
        "exceptionClass": {
            "$resolver": "exception",
            "field": "className"
        },
        "stackTrace": {
            "$resolver": "exception",
            "field": "stackTrace",
            "stringified": true
        },
        "message": {
            "$resolver": "message",
            "stringified": true
        },
        "thread": {
            "$resolver": "thread",
            "field": "name"
        },
        "timestamp": {
            "$resolver": "timestamp",
            "pattern": {
                "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                "timeZone": "UTC"
            }
        },
        "level": {
            "$resolver": "level",
            "field": "name"
        },
        "logger": {
            "$resolver": "logger",
            "field": "name"
        }
    }
    ```

3. 在 `log4j2-spring.xml` 配置文件中使用此 JSON 布局模板。 

    ```xml
    <configuration>
        <appenders>
            <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json" />
            </console>
        </appenders>
        <loggers>
            <root level="info">
                <appender-ref ref="Console" />
            </root>
        </loggers>
    </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>在 Log Analytics 中分析日志

正确设置应用程序后，应用程序控制台日志将流式传输到 Log Analytics。 此结构可在 Log Analytics 中实现高效查询。

### <a name="check-log-structure-in-log-analytics"></a>在 Log Analytics 中查看日志结构

请按以下过程操作：

1. 转到你的服务实例的服务概述页。
2. 单击 `Monitoring` 部分下的 `Logs` 条目。
3. 运行此查询。

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. 将返回应用程序日志，如下图所示：

   ![Json 日志显示](media/spring-cloud-structured-app-log/json-log-query.png)


### <a name="show-log-entries-containing-errors"></a>显示包含错误的日志条目

若要查看包含错误的日志条目，请运行以下查询：

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

使用此查询查找错误，或修改查询词以查找特定的异常类或错误代码。 

### <a name="show-log-entries-for-a-specific-traceid"></a>显示特定 traceId 的日志条目

若要查看特定跟踪 ID“trace_id”的日志条目，请运行以下查询：

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解日志查询，请参阅 [Azure Monitor 中的日志查询入门](../azure-monitor/logs/get-started-queries.md)。
