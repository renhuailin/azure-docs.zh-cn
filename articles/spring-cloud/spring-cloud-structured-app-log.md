---
title: 适用于 Azure 春季 Cloud 的结构化应用程序日志 |Microsoft Docs
description: 本文介绍如何在 Azure 春季云中生成和收集结构化应用程序日志数据。
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124459"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>适用于 Azure 春季云的结构化应用程序日志

本文介绍如何在 Azure 春季云中生成和收集结构化应用程序日志数据。 使用正确的配置，Azure 春季云通过 Log Analytics 提供有用的应用程序日志查询和分析。

## <a name="log-schema-requirements"></a>日志架构要求
若要改进日志查询体验，应用程序日志必须采用 JSON 格式，并符合架构。 Azure 春季云使用此架构分析你的应用程序，并将流传输到 Log Analytics。 

**JSON 架构要求：**

| Json 密钥      | Json 值类型|  必须 | 列 Log Analytics| 说明 |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     是   | AppTimestamp    | UTC 格式的时间戳  |
| logger        | string      |     否    | 记录器          | logger                   |
| 级别         | string      |     否    | CustomLevel     | 日志级别                |
| 线程 (thread)        | string      |     否    | 线程          | 线程 (thread)                   |
| message       | string      |     否    | Message         | 日志消息              |
| stackTrace    | string      |     否    | StackTrace      | 异常堆栈跟踪    |
| exceptionClass| string      |     否    | ExceptionClass  | 异常类名     |
| mdc           | 嵌套 JSON |     否    |                 | 映射的诊断上下文|
| mdc. traceId   | string      |     否    | TraceId         |分布式跟踪的跟踪 Id|
| mdc. spanId    | string      |     否    | SpanId          |分布式跟踪的 span Id |
|               |             |           |                 |                          |

* "时间戳" 字段是必需的，并且应采用 UTC 格式，所有其他字段都是可选的。
* "mdc" 字段中的 "traceId" 和 "spanId" 用于跟踪目的。
* 在一行记录每个 JSON 记录。 

**日志记录示例** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>生成符合架构的 JSON 日志  
对于春季应用程序，可以使用常用 [日志记录框架](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)（如 [logback](http://logback.qos.ch/) 和 [LOG4J2.XML](https://logging.apache.org/log4j/2.x/)）生成预期的 JSON 日志格式。 

### <a name="log-with-logback"></a>记录 logback 
使用春季 Boot 初学者时，默认情况下使用 logback。 对于 logback 应用，请使用 logstash 生成 JSON 格式 [的](https://github.com/logstash/logstash-logback-encoder) 日志。 在春季 Boot 2.1 版 + 中支持此方法。 

过程：

1. 在 pom.xml 文件中添加 logstash 依赖项。 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. 更新 logback.xml 配置文件以设置 JSON 格式。
    ```json
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
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>记录 log4j2.xml 

对于 log4j2.xml 应用，请使用 [json 模板布局](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) 生成 json 格式的日志。 在春季 Boot 2.1 版 + 中支持此方法。

过程：

1. `spring-boot-starter-logging` `spring-boot-starter` `spring-boot-starter-log4j2` 在 pom.xml 文件中排除、添加依赖项 `log4j-layout-template-json` 。

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

2. 准备在类路径中 jsonTemplate.js的 JSON 布局模板文件。

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

3. 在 log4j2.xml 配置文件中使用此 JSON 布局模板。 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>分析中的日志 Log Analytics

正确设置应用程序后，应用程序控制台日志将流式传输到 Log Analytics。 结构可在 Log Analytics 中实现高效查询。

### <a name="check-log-structure-in-log-analytics"></a>查看 Log Analytics 中的日志结构
请按以下过程操作：
1. 转到你的服务实例的服务概述页。
2. 单击 `Logs` 部分下的 "项" `Monitoring` 。
3. 运行此查询。

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. 应用程序日志返回如下图所示：

![Json 日志显示](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>显示包含错误的日志条目

若要查看有错误的日志条目，请运行以下查询：

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

使用此查询可查找错误，或修改查询字词以查找特定异常类或错误代码。 

### <a name="show-log-entries-for-a-specific-traceid"></a>显示特定 traceId 的日志条目
若要查看特定跟踪 ID "trace_id" 的日志条目，请运行以下查询：

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>后续步骤
* 若要了解有关日志查询的详细信息，请参阅 [Azure Monitor 中的日志查询入门](../azure-monitor/logs/get-started-queries.md)