---
title: 使用 Azure Monitor 监视 Python 应用程序 | Microsoft Docs
description: 提供有关使用 Azure Monitor 连接 OpenCensus Python 的说明
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
author: lzchen
ms.author: lechen
ms.openlocfilehash: 988f32cae16a026ddef0294815ffd21ba0d81760
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991732"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>为 Python 应用程序设置 Azure Monitor

Azure Monitor 支持对 Python 应用程序进行分布式跟踪、指标收集和日志记录。

Microsoft 支持的用于跟踪和导出 Python 应用程序数据的解决方案是通过 [Opencensus Python SDK](#introducing-opencensus-python-sdk) 借助 Azure Monitor [导出程序](#instrument-with-opencensus-python-sdk-with-azure-monitor-exporters)完成的。

适用于 Python 的其他任何遥测 SDK 都不受支持，Microsoft 不建议将这些 SDK 用作遥测解决方案。

你可能已注意到 OpenCensus 正在聚合到 [OpenTelemetry](https://opentelemetry.io/) 中。 但是我们继续推荐使用 OpenCensus，虽然 OpenTelemetry 不断变得成熟。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- Python 安装。 本文使用 [Python 3.7.0](https://www.python.org/downloads/release/python-370/)，但其他版本在经过轻微的更改后也可能适用。 Opencensus Python SDK 仅支持 Python v2.7 和 v3.4-v3.7。
- 创建 Application Insights [资源](./create-new-resource.md)。 系统将针对你的资源为你分配自己的检测密钥 (ikey)。

## <a name="introducing-opencensus-python-sdk"></a>Opencensus Python SDK 简介

[OpenCensus](https://opencensus.io) 是一组开放源代码库，用于收集分布式跟踪、指标和日志记录遥测数据。 通过使用 [Azure Monitor 导出程序](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)，你可以将收集的遥测数据发送到 Application Insights。 本文分步介绍设置为 Python 设置 OpenCensus for Python 和 Azure Monitor 导出程序并将监视数据发送到 Azure Monitor 的过程。

## <a name="instrument-with-opencensus-python-sdk-with-azure-monitor-exporters"></a>使用 Azure Monitor 导出程序检测 OpenCensus Python SDK

安装 OpenCensus Azure Monitor 导出程序：

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 命令假定你已为 Python 安装设置了 `PATH` 环境变量。 如果尚未配置此变量，则需要提供 Python 可执行文件所在位置的完整目录路径。 结果为如下所示的命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 使用三个 Azure Monitor 导出程序将不同类型的遥测数据发送到 Azure Monitor。 分别是跟踪、指标和日志。 有关这些遥测类型的详细信息，请参阅[数据平台概述](../data-platform.md)。 按照以下说明通过三个导出程序发送这些遥测类型。

## <a name="telemetry-type-mappings"></a>遥测类型映射

下面是 OpenCensus 提供的导出程序，它映射到会在 Azure Monitor 中出现的遥测类型。

| 可观测性的支柱 | Azure Monitor 中的遥测类型    | 说明                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| 日志                    | Traces、exceptions、customEvents   | 日志遥测、异常遥测、事件遥测 |
| 指标                 | customMetrics、performanceCounters | 自定义指标、性能计数器                |
| 跟踪                 | requests dependencies             | 传入的请求数、传出的请求数                |

### <a name="logs"></a>日志

1. 首先，让我们生成一些本地日志数据。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 代码会持续请求输入值。 对于输入的每个值，将发出一个日志条目。

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出日志数据。 将连接字符串直接传递到导出程序。 也可以在环境变量 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中指定它。 根据以下代码示例，修改上一步中的代码：

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 导出程序会将日志数据发送到 Azure Monitor。 可在 `traces` 下找到数据。 

    > [!NOTE]
    > 在此上下文中，`traces` 与 `tracing` 不同。 此处，`traces` 是指使用 `AzureLogHandler` 时 Azure Monitor 中会出现的遥测类型。 但 `tracing` 是指 OpenCensus 中的一种概念，与[分布式跟踪](./distributed-tracing.md)相关。

    > [!NOTE]
    > 根记录器配置为“警告”级别。 这意味着如果所发送的任何日志的严重性低于此级别，则其将被忽略，不会发送到 Azure Monitor。 有关详细信息，请参阅这篇[文档](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)。

1. 还可以在 extra 关键字参数中通过使用 custom_dimensions 字段向日志消息添加自定义属性。 这些属性会显示为 Azure Monitor 的 `customDimensions` 中的键值对。
    > [!NOTE]
    > 若要使此功能正常运行，需要将字典传递给 custom_dimensions 字段。 如果传递任何其他类型的参数，记录器会忽略它们。

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>配置 Django 应用程序的日志记录

可以按照上文所述在应用程序代码中为 Django 应用程序显式配置日志记录，也可以在 Django 的日志记录配置中指定日志记录。 此代码可以包含在用于 Django 设置配置的任何文件中。 有关如何配置 Django 设置的信息，请参阅 [Django 设置](https://docs.djangoproject.com/en/3.0/topics/settings/)。 有关配置记录的详细信息，请参阅 [Django 设置](https://docs.djangoproject.com/en/3.0/topics/logging/)。

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

请确保所用记录器的名称与在配置中指定的名称相同。

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>发送异常

OpenCensus Python 不会自动跟踪和发送 `exception` 遥测。 借助 Python 日志记录库使用异常，可通过 `AzureLogHandler` 发送它们。 可以像使用普通日志记录时一样添加自定义属性。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
由于必须显式记录异常，这取决于用户想要如何记录未处理的异常。 只要用户显式记录异常遥测，OpenCensus 就不会对用户想要如何执行此操作施加限制。

#### <a name="send-events"></a>发送事件

可以使用与发送 `trace` 遥测完全相同的方式来发送 `customEvent` 遥测，只是后者应该使用 `AzureEventHandler`。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>采样

有关在 OpenCensus 中采样的信息，请查看 [OpenCensus 中的采样](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>日志关联

有关如何使用跟踪上下文数据扩充日志的详细信息，请参阅 OpenCensus Python [日志集成](./correlation.md#log-correlation)。

#### <a name="modify-telemetry"></a>修改遥测

有关在将跟踪的遥测发送到 Azure Monitor 之前如何对其进行修改的详细信息，请参阅 OpenCensus Python [遥测处理器](./api-filtering-sampling.md#opencensus-python-telemetry-processors)。


### <a name="metrics"></a>指标

OpenCensus.stats 支持 4 种聚合方法，但对 Azure Monitor 提供部分支持：

- **Count：** 测量点数的计数。 该值是累积值，只能增加，在重启时重置为 0。 
- **Sum：** 测量点之和。 该值是累积值，只能增加，在重启时重置为 0。 
- **LastValue：** 保留最后记录的值，丢弃所有其他值。
- **Distribution：** 测量点的直方图分布。 Azure 导出程序不支持此方法。

### <a name="count-aggregation-example"></a>计数聚合示例

1. 首先，让我们生成一些本地指标数据。 我们将创建一个简单的指标，用于跟踪用户选择 Enter 键的次数。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. 运行代码时，系统会重复提示你选择 Enter。 将创建一个指标用于跟踪选择 Enter 的次数。 每次输入都会递增值，并且指标信息将显示在控制台中。 该信息包括指标更新时的当前值和当前时间戳。

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出指标数据。 将连接字符串直接传递到导出程序。 也可以在环境变量 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中指定它。 根据以下代码示例，修改上一步中的代码：

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. 导出程序按固定的间隔将指标数据发送到 Azure Monitor。 默认值为每 15 秒。 我们正在跟踪单个指标，因此，在每个间隔将会发送此指标数据及其包含的任何值和时间戳。 该值是累积值，只能增加，在重启时重置为 0。 虽然可以在 `customMetrics` 下找到数据，但实际上并未使用 `customMetrics` 属性 valueCount、valueSum、valueMin、valueMax 和 valueStdDev。

### <a name="setting-custom-dimensions-in-metrics"></a>在指标中设置自定义维度

Opencensus Python SDK 允许通过 `tags`（实质上是键/值对的字典）向指标遥测添加自定义维度。 

1. 将想要使用的标记插入到标记映射中。 标记映射的作用就像一种“池塘”，包含所有可用的标记。

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. 对于特定 `View`，请通过标记键来指定在使用该视图记录指标时要使用的标记。

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. 在度量映射中记录时，请务必使用标记映射。 在 `View` 中指定的标记键必须能在用于记录的标记映射中找到。

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. 在 `customMetrics` 表下，使用 `prompt_view` 发出的所有指标记录都将具有自定义维度 `{"url":"http://example.com"}`。

1. 若要使用相同的键生成具有不同值的标记，请为这些标记创建新的标记映射。

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>性能计数器

默认情况下，指标导出程序会向 Azure Monitor 发送一组性能计数器。 可以通过在指标导出程序的构造函数中将 `enable_standard_metrics` 标志设为 `False` 来禁用此功能。

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

当前已发送以下性能计数器：

- 可用内存（字节）
- CPU 处理器时间（百分比）
- 传入请求速率（每秒）
- 传入请求平均执行时间（毫秒）
- 进程 CPU 使用率（百分比）
- 进程专用字节数（字节）

你应该能够在 `performanceCounters` 中看到这些指标。 有关详细信息，请参阅[性能计时器](./performance-counters.md)。

#### <a name="modify-telemetry"></a>修改遥测

如需了解将跟踪的遥测发送到 Azure Monitor 之前如何对其进行修改，请参阅 OpenCensus Python [遥测处理器](./api-filtering-sampling.md#opencensus-python-telemetry-processors)。

### <a name="tracing"></a>跟踪

> [!NOTE]
> 在 OpenCensus 中，`tracing` 指[分布式跟踪](./distributed-tracing.md)。 `AzureExporter` 将 `requests` 和 `dependency` 遥测发送到 Azure Monitor。

1. 首先，让我们在本地生成一些跟踪数据。 在 Python IDLE 或所选编辑器中，输入以下代码：

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 运行代码时，系统会重复提示你输入值。 对于每个条目，值会打印到 shell。 OpenCensus Python 模块会生成相应的 `SpanData` 部分。 OpenCensus 项目[将跟踪定义为 span 树](https://opencensus.io/core-concepts/tracing/)。
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. 虽然输入值有助于演示，但最终我们希望将 `SpanData` 发出到 Azure Monitor。 将连接字符串直接传递到导出程序。 也可以在环境变量 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中指定它。 根据以下代码示例，修改上一步中的代码：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 现在，当你运行 Python 脚本时，系统仍会提示你输入值，但只有该值输出到 shell 中。 会将创建的 `SpanData` 发送到 Azure Monitor。 可在 `dependencies` 下找到发出的 span 数据。 有关传出请求的详细信息，请参阅 OpenCensus Python [依赖项](./opencensus-python-dependency.md)。
有关传入请求的详细信息，请参阅 OpenCensus Python [请求](./opencensus-python-request.md)。

#### <a name="sampling"></a>采样

有关在 OpenCensus 中采样的信息，请查看 [OpenCensus 中的采样](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>跟踪关联

有关跟踪数据中遥测关联的详细信息，请参阅 OpenCensus Python [遥测关联](./correlation.md#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遥测

有关在将跟踪的遥测发送到 Azure Monitor 之前如何对其进行修改的详细信息，请参阅 OpenCensus Python [遥测处理器](./api-filtering-sampling.md#opencensus-python-telemetry-processors)。

## <a name="configure-azure-monitor-exporters"></a>配置 Azure Monitor 导出程序

如图所示，有三个不同的 Azure Monitor 导出程序支持 OpenCensus。 每个导出程序都将不同类型的遥测发送到 Azure Monitor。 要查看每个导出程序发送的遥测类型，请参阅以下列表。

每个导出程序都接受通过构造函数传递的相同配置参数。 可在此处查看有关每个导出程序的详细信息：

- `connection_string`：用于连接到 Azure Monitor 资源的连接字符串。 其优先级高于 `instrumentation_key`。
- `enable_standard_metrics`：用于 `AzureMetricsExporter`。 指示导出程序将[性能计数器](../essentials/app-insights-metrics.md#performance-counters)指标自动发送到 Azure Monitor。 默认为 `True`。
- `export_interval`：用于指定导出频率（秒）。
- `instrumentation_key`：用于连接到 Azure Monitor 资源的检测密钥。
- `logging_sampling_rate`：用于 `AzureLogHandler`。 为导出日志提供采样率 [0,1.0]。 默认值为 1.0。
- `max_batch_size`：指定一次性导出的最大遥测大小。
- `proxies`：指定用于将数据发送到 Azure Monitor 的代理序列。 有关详细信息，请参阅[代理](https://requests.readthedocs.io/en/master/user/advanced/#proxies)。
- `storage_path`：指向本地存储文件夹（未发送的遥测）所在位置的路径。 自 `opencensus-ext-azure` v1.0.3 起，默认路径为 OS 临时目录 + `opencensus-python` + `your-ikey`。 在 v1.0.3 之前，默认路径为 $USER + `.opencensus` + `.azure` + `python-file-name`。

## <a name="authentication-preview"></a>身份验证（预览版）
> [!NOTE]
> 从 `opencensus-ext-azure` v1.1b0 开始提供身份验证功能

每个 Azure Monitor 导出程序都支持以下配置：通过使用 Azure Active Directory (AAD) 进行的 OAuth 身份验证来安全地发送遥测有效负载。
有关详细信息，请参阅[身份验证](./azure-ad-authentication.md)文档。

## <a name="view-your-data-with-queries"></a>使用查询查看数据

可以通过“日志(分析)”选项卡查看从应用程序发送的遥测数据。

![红色框中选定“日志(分析)”的概述窗格的屏幕截图](./media/opencensus-python/0010-logs-query.png)

在“活动”下的列表中：

- 对于使用 Azure Monitor 跟踪导出程序发送的遥测，传入请求在 `requests` 下显示。 传出或进程内请求在 `dependencies` 下显示。
- 对于使用 Azure Monitor 指标导出程序发送的遥测，发送的指标在 `customMetrics` 下显示。
- 对于使用 Azure Monitor 日志导出程序发送的遥测，日志在 `traces` 下显示。 异常在 `exceptions` 下显示。

有关如何使用查询和日志的更多详细信息，请参阅 [Azure Monitor 中的日志](../logs/data-platform-logs.md)。

## <a name="learn-more-about-opencensus-for-python"></a>了解有关 OpenCensus for Python 的详细信息

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 上的 Azure Monitor 导出程序](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 集成](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor 示例应用程序](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>后续步骤

* [跟踪传入请求](./opencensus-python-dependency.md)
* [跟踪传出请求](./opencensus-python-request.md)
* [应用程序映射](./app-map.md)
* [端到端性能监视](../app/tutorial-performance.md)

### <a name="alerts"></a>警报

* [可用性测试](./monitor-web-app-availability.md)：创建测试来确保站点在 Web 上可见。
* [智能诊断](./proactive-diagnostics.md)：这些测试可自动运行，因此不需要进行任何设置。 它们会告诉你应用是否具有异常的失败请求速率。
* [指标警报](../alerts/alerts-log.md)：设置警报以在某个指标超过阈值时发出警告。 可以在编码到应用中的自定义指标中设置它们。
