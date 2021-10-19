---
title: 配置选项 - 适用于 Java 的 Azure Monitor Application Insights
description: 如何配置适用于 Java 的 Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3ca38fbefccaf6529d78d1c5acce30c85d88bf7c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616980"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>配置选项 - 适用于 Java 的 Azure Monitor Application Insights

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

默认情况下，Application Insights Java 3.X 要求将配置文件命名为 `applicationinsights.json`，并置于 `applicationinsights-agent-3.2.0.jar` 所在的目录中。

可以使用以下任一方法指定你自己的配置文件路径：

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 环境变量，或者
* `applicationinsights.configuration.file` Java 系统属性

如果你指定相对路径，系统会相对于 `applicationinsights-agent-3.2.0.jar` 所在的目录对其进行解析。

## <a name="connection-string"></a>连接字符串

连接字符串是必需的。 可以在 Application Insights 资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

你还可以使用环境变量设置连接字符串 `APPLICATIONINSIGHTS_CONNECTION_STRING`（优先级将高于 json 配置中指定的连接字符串）。

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

你还可以使用环境变量设置云角色名 `APPLICATIONINSIGHTS_ROLE_NAME`（优先级将高于 json 配置中指定的云角色名）。

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

你还可以使用环境变量设置云角色实例，`APPLICATIONINSIGHTS_ROLE_INSTANCE`（优先级将高于 json 配置中指定的云角色实例）。

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

你还可以使用环境变量设置采样百分比 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE`（优先级将高于 json 配置中指定的采样百分比）。

> [!NOTE]
> 对于采样百分比，请选择一个接近于 100/N 的百分比，其中 N 是整数。 当前采样不支持其他值。

## <a name="sampling-overrides-preview"></a>采样替代（预览版）

此功能为预览版，从 3.0.3 开始。

采样替代允许你替代[默认采样百分比](#sampling)，例如：
* 将采样百分比设置为 0（或某个小值）以检查干扰运行状况。
* 将采样百分比设置为 0（或一些小值）以调用干扰依赖项。
* 针对某个重要请求类型（例如，`/login`），将采样百分比设置为 100，即使你将默认采样配置为低于此值也是如此。

更多信息，请查看[采样替代](./java-standalone-sampling-overrides.md)文档。

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
> 从 3.0.2 版开始，如果你添加名为 `service.version` 的自定义维度，该值将存储在 Application Insights 日志表的 `application_Version` 列中，而不是作为自定义维度。

## <a name="inherited-attribute-preview"></a>继承的属性（预览版）

从版本 3.2.0 开始，可按如下所示以编程方式对请求遥测设置自定义维度，并让依赖项遥测继承该维度：

```json
{
  "inheritedAttributes": [
    {
      "key": "mycustomer",
      "type": "string"
    }
  ]
}
```


## <a name="telemetry-processors-preview"></a>遥测处理器（预览版）

它可用于配置将应用于请求、依赖项和跟踪遥测的规则，例如：
 * 屏蔽敏感数据
 * 有条件地添加自定义维度
 * 更新用于在 Azure 门户中聚合类似遥测数据的范围名称。
 * 删除范围属性以控制数据引入成本。

有关详细信息，请查看[遥测处理器](./java-standalone-telemetry-processors.md)文档。

> [!NOTE]
> 如果希望删除特定（整体）范围来控制数据引入成本，请参阅[采样替代](./java-standalone-sampling-overrides.md)。

## <a name="auto-collected-logging"></a>自动收集的日志记录

系统自动检测 Log4j、Logback 和 java.util.logging，并自动收集通过这些记录框架执行的日志记录。

日志记录首先要符合为日志记录框架配置的级别，其次还要符合为 Application Insights 配置的级别，只有在这种情况下才会捕获日志记录。

例如，如果将日志记录框架配置为从包 `com.example` 记录 `WARN`（和更高版本），并将 Application Insights 配置为捕获 `INFO`（和更高版本），则 Application Insights 将只从包 `com.example` 捕获 `WARN`（和更高版本）。

为 Application Insights 配置的默认级别为 `INFO`。 若要更改此级别，请执行以下代码：

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

你还可以使用环境变量设置级别 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL`（优先级将高于 json 配置中指定的级别）。

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

> [!NOTE]
> 如果将异常对象传递到记录器，则日志消息（和异常对象详细信息）会显示在 Azure 门户中的 `exceptions` 表（而不是 `traces` 表）下。

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

## <a name="suppressing-specific-auto-collected-telemetry"></a>取消特定的自动收集遥测

从 3.0.3 版本开始，可使用以下配置选项取消特定的自动收集遥测：

```json
{
  "instrumentation": {
    "azureSdk": {
      "enabled": false
    },
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
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
    "rabbitmq": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

还可将以下环境变量设置为 `false`，以取消这些检测：

* `APPLICATIONINSIGHTS_INSTRUMENTATION_AZURE_SDK_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_RABBITMQ_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

（优先级将高于 json 配置中指定的已启用功能）。

> [!NOTE]
> 如果要查找更精细的控件，例如，要取消某些 redis 调用，而不是所有 redis 调用，请参阅[采样替代](./java-standalone-sampling-overrides.md)。

## <a name="preview-instrumentations"></a>预览版检测

从版本3.2.0 开始，可以启用以下预览版检测：

```
{
  "preview": {
    "instrumentation": {
      "apacheCamel": {
        "enabled": true
      },
      "grizzly": {
        "enabled": true
      },
      "quartz": {
        "enabled": true
      },
      "springIntegration": {
        "enabled": true
      }
    }
  }
}
```

## <a name="heartbeat"></a>检测信号

默认情况下，Application Insights Java 3.X 每 15 分钟发送一个检测信号指标。
如果使用检测信号指标来触发警报，则可增加此检测信号的频率：

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> 不可将间隔增加到超过 15 分钟，因为检测信号数据也用于跟踪 Application Insights 使用情况。

## <a name="http-proxy"></a>HTTP 代理

如果应用程序位于防火墙后面，无法直接连接到 Application Insights（请参阅[ Application Insights 使用的 IP 地址](./ip-addresses.md)），则可将 Application Insights Java 3.X 配置为使用 HTTP 代理：

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.X 还沿用全局 `https.proxyHost` 和 `https.proxyPort` 系统属性，前提是这些属性已经设置完成（如有需要还可沿用 `http.nonProxyHosts`）。

## <a name="metric-interval"></a>指标间隔

此功能为预览版。

默认情况下，每 60 秒捕获一次指标。

从 3.0.3 版本开始，可以更改此间隔：

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

该设置适用于所有这些指标：

* 默认性能计数器，例如 CPU 和内存
* 默认自定义指标，例如垃圾回收计时
* 已配置的 JMX 指标（[见上](#jmx-metrics)）
* Micrometer 指标（[见上](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics)）


[//]: # "请注意，在 OpenTelemetry API 达到 1.0 之前，OpenTelemetry 支持以个人预览版提供"

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

## <a name="authentication-preview"></a>身份验证（预览版）
> [!NOTE]
> 从版本 3.2.0-BETA 开始提供身份验证功能

它允许你配置代理以生成 Azure Active Directory 身份验证所需的[令牌凭据](/java/api/overview/azure/identity-readme#credentials)。
有关详细信息，请参阅[身份验证](./azure-ad-authentication.md)文档。

## <a name="self-diagnostics"></a>自我诊断

“自我诊断”指的是 Application Insights Java 3.X 的内部日志记录。

此功能可用于发现和诊断 Application Insights 本身的问题。

默认情况下，Application Insights Java 3.X 将对照以下配置，在 `INFO` 级别将日志记录到文件 `applicationinsights.log` 和控制台：

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

`path` 可以是绝对或相对路径。 相对路径根据 `applicationinsights-agent-3.2.0.jar` 所在的目录进行解析。

`maxSizeMb` 是日志文件滚动更新之前的最大大小。

`maxHistory` 是保留的滚动更新日志文件的数目（除当前日志文件外）。

从 3.0.2 版本开始，你还可以使用环境变量 `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL`（优先级将高于 json 配置中指定的自我诊断级别）设置自我诊断 `level`。

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