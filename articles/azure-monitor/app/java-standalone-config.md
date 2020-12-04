---
title: 配置选项-Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 的配置选项
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: e9240253b385a0b53868c5a8a79c751c2f7c09c0
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601078"
---
# <a name="configuration-options-for-azure-monitor-application-insights-java"></a>Azure Monitor Application Insights Java 的配置选项

> [!WARNING]
> **如果要从3.0 预览版升级**
>
> 请仔细查看下面的所有配置选项，因为 json 结构已完全更改，而文件名本身则以全部小写。

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

你将在下面找到更多详细信息和其他配置选项。

## <a name="configuration-file-path"></a>配置文件路径

默认情况下，Application Insights Java 3.0 需要将配置文件命名为，并将其置于与 `applicationinsights.json` 相同的目录中 `applicationinsights-agent-3.0.0.jar` 。

可以使用以下任一方法指定你自己的配置文件路径：

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 环境变量，或者
* `applicationinsights.configuration.file` Java 系统属性

如果你指定相对路径，系统会相对于 `applicationinsights-agent-3.0.0.jar` 所在的目录对其进行解析。

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

下面是一个示例，说明如何将采样设置为捕获大约 **1/3 的所有事务** ，确保为用例设置了正确的采样率：

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

如果要收集一些其他 JMX 指标：

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

`name` 将分配给此 JMX 指标 (的度量值名称可以是任何) 。

`objectName` 要收集的 JMX MBean 的 [对象名称](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) 。

`attribute` 要收集的 JMX MBean 中的属性名称。

支持数值和布尔 JMX 指标值。 False 表示布尔 JMX 指标映射到 `0`，true 表示映射到 `1`。

[//]: # "注意：此处未记录 APPLICATIONINSIGHTS_JMX_METRICS"
[//]: # "env var 中嵌入的 json 是比较凌乱的，应仅在无代码附加方案中进行记录"

## <a name="custom-dimensions"></a>自定义维度

如果要将自定义维度添加到所有遥测：

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` 可用于在启动时从指定的环境变量中读取值。

## <a name="telemetry-processors-preview"></a> (预览版的遥测处理器) 

此功能为预览版。

它允许你配置将应用于请求、依赖项和跟踪遥测的规则，例如：
 * 屏蔽敏感数据
 * 有条件地添加自定义维度
 * 更新用于聚合和显示的遥测名称

有关详细信息，请查看 [遥测处理器](./java-standalone-telemetry-processors.md) 文档。

## <a name="auto-collected-logging"></a>自动收集的日志记录

Log4j、Logback 和 util。日志记录是自动检测的，将自动收集通过这些日志记录框架执行的日志记录。

默认情况下，仅当在 `INFO` 级别或更高级别执行日志记录时，才会收集日志记录。

如果要更改此集合级别：

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

还可以使用环境变量设置阈值 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` 。

这些是 `level` 可以在文件中指定的有效值 `applicationinsights.json` ，以及这些值如何与不同日志记录框架中的日志记录级别相对应：

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

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>自动收集的 Micrometer 指标 (包括弹簧 Boot 制动器指标) 

如果你的应用程序使用 [Micrometer](https://micrometer.io)，则会自动收集发送到 Micrometer 全局注册表的指标。

此外，如果应用程序使用 [春季 Boot 传动装置](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)，则也会自动收集弹簧 boot 制动器配置的指标。

若要禁用 Micrometer 指标的自动收集 (包括弹簧 Boot 传动指标) ：

> [!NOTE]
> 自定义指标单独计费，并可能产生额外费用。 请确保查看详细 [定价信息](https://azure.microsoft.com/pricing/details/monitor/)。 若要禁用 Micrometer 和弹簧制动器指标，请将以下配置添加到配置文件。

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>检测信号

默认情况下，Application Insights Java 3.0 每15分钟发送一次检测信号指标。 如果使用检测信号指标来触发警报，则可增加此检测信号的频率：

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> 不能降低检测信号的频率，因为检测信号数据也用于跟踪 Application Insights 使用情况。

## <a name="http-proxy"></a>HTTP 代理

如果你的应用程序位于防火墙之后，并且无法直接连接到 Application Insights (请参阅 [Application Insights) 使用的 IP 地址](./ip-addresses.md) ，你可以将 Application Insights Java 3.0 配置为使用 HTTP 代理：

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "请注意，在我们支持0.10.0 之前，不会公布 OpenTelemetry 支持，这对0.9.0 有重大重大更改"

[//]: # "# # 支持 OpenTelemetry API 1.0 版"

[//]: # "由于 OpenTelemetry API 尚不稳定，因此支持 OpenTelemetry API 的预1.0 版本"
[//]: # "因此，代理的每个版本仅支持 OpenTelemetry API 的特定1.0 版"
[//]: # " (一旦发布 OpenTelemetry API 1.0，此限制将不适用) 。"

[//]: # """ "json"
[//]: # "{"
[//]: # "  \"预览 \" ： {"
[//]: # "    \"openTelemetryApiSupport \" ： true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>自我诊断

"自诊断" 指的是 Application Insights Java 3.0 的内部日志记录。

此功能有助于发现和诊断 Application Insights 本身的问题。

默认情况下，与 `INFO` `applicationinsights.log` 此配置相对应，在文件和控制台级别 Application Insights Java 3.0 日志：

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

`destination` 可以是 `file` 、或之一 `console` `file+console` 。

`level` 可以是、、 `OFF` 、 `ERROR` `WARN` `INFO` 、或之一 `DEBUG` `TRACE` 。

`path` 可以是绝对路径或相对路径。 相对路径是根据所在的目录解析的 `applicationinsights-agent-3.0.0.jar` 。

`maxSizeMb` 滚动之前日志文件的最大大小。

`maxHistory` (除了当前日志文件) 之外保留的日志文件的数目。

## <a name="an-example"></a>示例

这只是一个示例，用于显示具有多个组件的配置文件。
请根据你的需要配置特定的选项。

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