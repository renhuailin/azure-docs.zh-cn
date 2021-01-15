---
title: 配置选项-Azure Monitor Java Application Insights
description: 如何为 Java 配置 Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 953a9cfeed558291fba1cb517039f26860444904
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233655"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>配置选项-Azure Monitor Java Application Insights

> [!WARNING]
> **如果要从 3.0 预览版升级**
>
> 请仔细查看下面的所有配置选项，因为除了文件名本身全部变为小写外，json 结构也已完全改变。

## <a name="connection-string-and-role-name"></a>连接字符串和角色名称

连接字符串和角色名称是入门所需的最常见设置：

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

将数据从不同的应用程序发送到同一个 Application Insights 资源时，必须使用连接字符串和角色名称。

下面提供了更多详细信息和其他配置选项。

## <a name="configuration-file-path"></a>配置文件路径

默认情况下，Application Insights Java 3.0 要求将配置文件命名为 `applicationinsights.json` 并置于 `applicationinsights-agent-3.0.1.jar` 所在的目录中。

可以使用以下任一方法指定你自己的配置文件路径：

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 环境变量，或者
* `applicationinsights.configuration.file` Java 系统属性

如果你指定相对路径，系统会相对于 `applicationinsights-agent-3.0.1.jar` 所在的目录对其进行解析。

## <a name="connection-string"></a>连接字符串

连接字符串是必需的。 可以在 Application Insights 资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

还可以使用环境变量 `APPLICATIONINSIGHTS_CONNECTION_STRING` 设置连接字符串。

如果未设置连接字符串，则将禁用 Java 代理。

## <a name="cloud-role-name"></a>云角色名称

云角色名称用于标记应用程序映射上的组件。

如果要设置云角色名称，请执行以下代码：

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

如果你未设置云角色名称，系统会使用 Application Insights 资源的名称在应用程序映射上标记该组件。

还可以使用环境变量 `APPLICATIONINSIGHTS_ROLE_NAME` 设置云角色名称。

## <a name="cloud-role-instance"></a>云角色实例

云角色实例默认为计算机名称。

若要将云角色实例设置为不同于计算机名称的名称，请执行以下代码：

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

还可以使用环境变量 `APPLICATIONINSIGHTS_ROLE_INSTANCE` 设置云角色实例。

## <a name="sampling"></a>采样

如果需要降低成本，则可进行采样。
采样将作为一个函数来执行，该函数基于操作 ID（也称跟踪 ID）。因此，相同的操作 ID 始终会产生相同的采样决定。 这可确保你不会在采样过程中采用分布式事务的某些部分而排除其他部分。

例如，如果将采样率设置为 10%，则只会看到 10% 的事务，但在这 10% 的事务中，每个事务都有完整的端到端事务详细信息。

下面是一个示例，演示了如何将采样设置为捕获大约 1/3 的事务 - 请确保你设置的采样率适合自己的用例：

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

还可以使用环境变量 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` 设置采样百分比。

> [!NOTE]
> 对于采样百分比，请选择一个接近于 100/N 的百分比，其中 N 是整数。 当前采样不支持其他值。

## <a name="jmx-metrics"></a>JMX 指标

如果要收集一些其他 JMX 指标，请运行以下命令：

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` 是将分配给此 JMX 指标的指标名称（可以是任何名称）。

`objectName` 是要收集的 JMX MBean 的[对象名称](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html)。

`attribute` 是要收集的 JMX MBean 内部的属性名称。

支持数值和布尔 JMX 指标值。 False 表示布尔 JMX 指标映射到 `0`，true 表示映射到 `1`。

[//]: # "注意：此处未记录 APPLICATIONINSIGHTS_JMX_METRICS"
[//]: # "env var 中嵌入的 json 是比较凌乱的，应仅在无代码附加方案中进行记录"

## <a name="custom-dimensions"></a>自定义维度

如果要向所有遥测数据添加自定义维度：

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` 可用于在启动时从指定的环境变量中读取值。

> [!NOTE]
> 从版本3.0.1 开始，如果添加一个名为的自定义维度 `service.version` ，该值将存储在 `application_Version` Application Insights 日志表的列中，而不是作为自定义维度。

## <a name="telemetry-processors-preview"></a>遥测处理器（预览版）

此功能为预览版。

它可用于配置将应用于请求、依赖项和跟踪遥测的规则，例如：
 * 屏蔽敏感数据
 * 有条件地添加自定义维度
 * 更新用于聚合和显示的遥测名称

有关详细信息，请查看[遥测处理器](./java-standalone-telemetry-processors.md)文档。

## <a name="auto-collected-logging"></a>自动收集的日志记录

系统自动检测 Log4j、Logback 和 java.util.logging，并自动收集通过这些记录框架执行的日志记录。

仅在第一次满足日志记录框架配置的阈值时才捕获日志记录，另一种方法还满足 Application Insights 配置的阈值。

默认 Application Insights 阈值为 `INFO` 。 如果要更改此级别，请执行以下操作：

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

还可以使用环境变量 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` 设置阈值。

下面介绍了你可以在 `applicationinsights.json` 文件中指定的有效 `level` 值，以及这些值如何对应于不同记录框架中的日志记录级别：

| 级别             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| ERROR（或 SEVERE） | ERROR  | ERROR   | SEVERE  |
| WARN（或 WARNING） | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG（或 FINE）   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE（或 FINEST） | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>自动收集的 Micrometer 指标（包括 Spring Boot Actuator 指标）

如果应用程序使用 [Micrometer](https://micrometer.io)，系统会自动收集发送到 Micrometer 全局注册表的指标。

此外，如果应用程序使用 [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)，系统也会自动收集 Spring Boot Actuator 配置的指标。

若要禁止自动收集 Micrometer 指标（包括 Spring Boot Actuator 指标），请运行以下命令：

> [!NOTE]
> 自定义指标单独计费，并可能产生额外费用。 请确保查看详细的[定价信息](https://azure.microsoft.com/pricing/details/monitor/)。 若要禁用 Micrometer 和 Spring Actuator 指标，请将以下配置添加到配置文件中。

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>抑制特定的自动收集遥测

从版本3.0.1 开始，可以使用以下配置选项禁止显示特定的自动收集遥测数据：

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>检测信号

默认情况下，Application Insights Java 3.0 每 15 分钟发送一个检测信号指标。 如果使用检测信号指标来触发警报，则可增加此检测信号的频率：

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> 不能降低此检测信号的频率，因为检测信号数据也用于跟踪 Application Insights 使用情况。

## <a name="http-proxy"></a>HTTP 代理

如果应用程序位于防火墙后面，无法直接连接到 Application Insights（请参阅 [Application Insights 使用的 IP 地址](./ip-addresses.md)），则可将 Application Insights Java 3.0 配置为使用 HTTP 代理：

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "请注意，在我们支持 0.10.0 之前，不会播发 OpenTelemetry 支持，0.10.0 与 0.9.0 相比有巨大的突破性变化"

[//]: # "## 对 OpenTelemetry API 1.0 之前的版本的支持"

[//]: # "可选择启用对 OpenTelemetry API 1.0 之前的版本的支持，因为 OpenTelemetry API 尚不稳定"
[//]: # "因此，该代理的各个版本仅支持 OpenTelemetry API 1.0 之前的特定版本"
[//]: # "（OpenTelemetry API 1.0 发布后，此限制将不再适用）。"

[//]: # "```json"
[//]: # "{"
[//]: # "  \"preview\": {"
[//]: # "    \"openTelemetryApiSupport\": true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>自我诊断

“自我诊断”指的是 Application Insights Java 3.0 的内部日志记录。

此功能可用于发现和诊断 Application Insights 本身的问题。

默认情况下，Application Insights Java 3.0 在 `INFO` 级别将日志记录到文件 `applicationinsights.log` 和控制台，对应于以下配置：

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` 可以为 `file`、`console` 或 `file+console` 中的一个。

`level` 可以为 `OFF`、`ERROR`、`WARN`、`INFO`、`DEBUG` 或 `TRACE` 中的一个。

`path` 可以是绝对或相对路径。 相对路径根据 `applicationinsights-agent-3.0.1.jar` 所在的目录进行解析。

`maxSizeMb` 是日志文件滚动更新之前的最大大小。

`maxHistory` 是保留的滚动更新日志文件的数目（除当前日志文件外）。

## <a name="an-example"></a>示例

这只是一个示例，用来展示包含多个组件的配置文件的外观。
请根据你的需求配置特定选项。

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```