---
title: Azure Spring Cloud 的指标
description: 了解如何查看 Azure Spring Cloud 中的指标
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 26d95493042d259029bc6a9428b016bbceb5681b
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154235"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure Spring Cloud 的指标

Azure 指标资源管理器是 Microsoft Azure 门户的一个组件，可用于绘制图表、以直观的形式关联趋势以及研究指标的上升和下降。 使用指标资源管理器调查资源的运行状况和利用率。

Azure Spring Cloud 中有两个指标视点。
* 每个应用程序概览页中的图表
* 常用指标页

 ![指标图表](media/metrics/metrics-1.png)

应用程序“概览”中的图表为每个应用程序提供快速的状态检查。 常用“指标”页包含可供参考的所有指标。 可以在常用指标页中构建你自己的图表，然后在“仪表板”中固定它们。

## <a name="application-overview-page"></a>应用程序概览页

在“应用”中选择一个应用，以在概览页中查找图表。

![应用程序指标管理](media/metrics/metrics-2.png)

每个应用程序的“应用程序概览”页都会提供一个指标图表，用于对应用程序进行快速状态检查。

![应用程序指标概览](media/metrics/metrics-3.png)

Azure Spring Cloud 提供以下五个图表，其中的指标每分钟更新一次：

* **Http 服务器错误**：向应用发出的 HTTP 请求的错误计数
* **数据输入**：应用接收的字节数
* **数据输出**：应用发送的字节数
* **请求**：应用接收的请求数
* **平均响应时间**：应用的平均响应时间

对于图表，你可以选择一小时到七天的时间范围。

## <a name="common-metrics-page"></a>常用指标页

左侧导航窗格中的“指标”链接到常用指标页。

首先，选择要查看的指标：

![选择指标视图](media/metrics/metrics-4.png)

有关所有指标选项的详细信息，可参阅以下[部分](#user-metrics-options)。

接下来，为每个指标选择聚合类型：

![指标聚合](media/metrics/metrics-5.png)

聚合类型指示如何按时间对图表中的指标点进行聚合。 每分钟有一个原始指标点，一分钟内的预聚合类型由指标类型预定义。

* 总和：将所有值的和显示为目标输出。
* 平均值：将时段中的“平均”值用作目标输出。
* 最大值/最小值：将时段中的“最大/最小”值用作目标输出。

还可以将时间范围从过去 30 分钟调整为过去 30 天或某个自定义时间范围。

![指标修改](media/metrics/metrics-6.png)

默认视图包括某个 Azure Spring Cloud 服务的应用程序的所有指标。 可以在显示中筛选一个应用或实例的指标。 选择“添加筛选器”，将属性设置为“应用”，然后在“值”文本框中选择要监视的目标应用程序  。

你可以使用两种类型的筛选器（属性）：

* 应用：按应用名称筛选
* 实例：按应用实例筛选

![指标筛选器](media/metrics/metrics-7.png)

你还可以使用“应用拆分”选项，该选项将为一个应用绘制多个行：

![指标拆分](media/metrics/metrics-8.png)

>[!TIP]
> 可以在指标页中构建你自己的图表，然后将其固定到“仪表板”。 一开始，为图表命名。  接下来，选择“固定到右上角的仪表板”。 现在可以在门户“仪表板”中检查应用程序了。

## <a name="user-metrics-options"></a>用户指标选项

下表显示了可用的指标和详细信息。

### <a name="error"></a>错误

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | Spring Actuator 指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat.global.error | tomcat.global.error | 计数 | 已处理的请求中发生的错误数 |

### <a name="performance"></a>性能

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | Spring Actuator 指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| system.cpu.usage | system.cpu.usage | 百分比 | 整个系统最近的 CPU 使用率（已过时，不建议使用）。 此值为 [0.0,1.0] 区间中的一个双精度值。 如果值为 0.0，则表示在所观察的最近一段时间内所有 CPU 都处于空闲状态，而如果值为 1.0，则表示在所观察的最近一段时间内所有 CPU 都一直在主动运行。|
>| process.cpu.usage | 应用 CPU 使用率百分比 | 百分比 | Java 虚拟机进程最近的 CPU 使用率（已过时，不建议使用）。 此值为 [0.0,1.0] 区间中的一个双精度值。 如果值为 0.0，则表示在所观察的最近一段时间内，任何 CPU 都未运行 JVM 进程中的线程，而如果值为 1.0，则表示在所观察的最近一段时间内，所有 CPU 都一直在主动运行 JVM 中的线程。 JVM 中的线程包括应用程序线程以及 JVM 内部线程。|
>| 应用 CPU 使用情况 | | 百分比 | 针对分配给此应用的 CPU 的 JVM 进程的最近 CPU 使用率。 此值为 [0.0,1.0] 区间中的一个双精度值。 如果值为 0.0，则表示在所观察的最近一段时间内，任何 CPU 都未运行 JVM 进程中的线程，而如果值为 1.0，则表示在所观察的最近一段时间内，所有 CPU 都一直在主动运行 JVM 中的线程。 JVM 中的线程包括应用程序线程以及 JVM 内部线程。|
>| 应用 CPU 使用率（已弃用） | | 百分比 | 已弃用的应用 CPU 使用率指标。 请改为使用新的应用 CPU 使用率指标。|
>| 应用内存使用情况 | | 百分比 | 针对分配给此应用的内存的 JVM 进程的最近内存使用率。 此值为 [0.0,1.0] 区间中的一个双精度值。 如果值为 0.0，则表示在所观察的最近一段时间内，JVM 进程中的线程未分配任何内存；如果值为 1.0，则表示在所观察的最近一段时间内，所有内存都一直由 JVM 进程中的线程分配。 JVM 中的线程包括应用程序线程以及 JVM 内部线程。|
>| jvm.memory.committed | jvm.memory.committed | 字节 | 表示保证可供 JVM 使用的内存量。 JVM 可能会向系统释放内存，其提交的值可能小于初始值。 提交的值将始终大于或等于使用的值。 |
>| jvm.memory.used | jvm.memory.used | 字节 | 表示当前使用的内存量（字节）。 |
>| jvm.memory.max | jvm.memory.max | 字节 | 表示可用于内存管理的最大内存量。 使用的和提交的内存量将始终少于或等于最大内存量（如果已定义最大内存量）。 如果在进行内存分配时尝试提高使用的内存，使得使用的内存 > 提交的内存，则即使使用的内存 <= 最大内存（例如，当系统的虚拟内存较低时），内存分配也可能会失败。 |
>| jvm.gc.max.data.size | jvm.gc.max.data.size | 字节 | 自 Java 虚拟机启动以来旧代内存池的峰值内存使用量。 |
>| jvm.gc.live.data.size | jvm.gc.live.data.size | 字节 | 完全 GC 后的旧代内存池大小。 |
>| jvm.gc.memory.promoted | jvm.gc.memory.promoted | 字节 | 从 GC 之前到 GC 之后这段时间，旧代内存池大小正增加的计数。 |
>| jvm.gc.memory.allocated | jvm.gc.memory.allocated | 字节 | 运行一个 GC 之后，在运行下一个 GC 之前，新代内存池大小增加的递增量。 |
>| jvm.gc.pause.total.count | jvm.gc.pause (total-count) | 计数 | 此 JMV 启动后的总 GC 计数，包括新代和旧代 GC。 |
>| jvm.gc.pause.total.time | jvm.gc.pause (total-time) | 毫秒 | 此 JMV 启动后消耗的总 GC 时间，包括新代和旧代 GC。 |

### <a name="performance-net"></a>性能 (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | Spring Actuator 指标名称 | 计价单位 | 详细信息 |
>|------|-----------------------------|------|---------|
>| CPU 使用率       | cpu-usage      | 百分比      | 进程的 CPU 使用率相对于所有系统 CPU 资源的百分比 [0-100]。 |
>| 工作集     | working-set    | 兆字节    | 进程所使用的工作集数量。 |
>| GC 堆大小    | gc-heap-size   | 兆字节    | 垃圾回收器报告的堆总大小。 |
>| 第 0 代 GC 计数  | gen-0-gc-count | 计数        | 每秒第 0 代垃圾回收数。 |
>| 第 1 代 GC 计数  | gen-1-gc-count | 计数        | 每秒第 1 代垃圾回收数。 |
>| 第 2 代 GC 计数  | gen-2-gc-count | 计数        | 每秒第 2 代垃圾回收数。 |
>| GC 所占时间      | timein-gc      | 百分比      | 自上次垃圾回收后垃圾回收中的时间百分比。 |
>| Gen 0 heap size（第 0 代堆大小） | gen-0-size     | 字节        | 第 0 代堆大小。 |
>| Gen 1 heap size（第 1 代堆大小） | gen-1-size     | 字节        | 第 1 代堆大小。 |
>| Gen 2 heap size（第 2 代堆大小） | gen-2-size     | 字节        | 第 2 代堆大小。 |
>| LOH 堆大小   | loh-size       | 字节        | 大型对象堆堆大小。 |
>| 分配速率 | alloc-rate     | 字节        | 每秒分配的字节数。 |
>| 程序集计数  | assembly-count | 计数        | 已加载的程序集数。 |
>| 异常计数 | exception-count | 计数       | 每秒异常数。 |
>| 线程池线程计数      | threadpool-thread-count              | 计数 | 线程池线程数。 |
>| 监视器锁争用计数 | monitor-lock-contention-count        | 计数 | 尝试获取监视器锁时每秒发生争用的次数。 |
>| 线程池队列长度      | threadpool-queue-length              | 计数 | 线程池工作项队列长度。 |
>| 线程池已完成项计数 | threadpool-completed-items-count | 计数 | 线程池已完成工作项计数。 |
>| 活动计时器计数               | active-timer-count               | 计数 | 当前处于活动状态的计时器数。 活动计时器是注册为在将来某个时间点计时且尚未取消的计时器。 |

有关详细信息，请参阅 [dotnet 计数器](/dotnet/core/diagnostics/dotnet-counters)。

### <a name="request"></a>请求

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | Spring Actuator 指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat.global.sent | tomcat.global.sent | 字节 | Tomcat Web 服务器发送的数据量 |
>| tomcat.global.received | tomcat.global.received | 字节 | Tomcat Web 服务器接收的数据量 |
>| tomcat.global.request.total.count | tomcat.global.request (total-count) | 计数 | Tomcat Web 服务器处理的请求总数 |
>| tomcat.global.request.max | tomcat.global.request.max | 毫秒 | Tomcat Web 服务器用于处理请求的最长时间 |

### <a name="request-net"></a>请求 (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | Spring Actuator 指标名称 | 计价单位 | 详细信息 |
>|------|-----------------------------|------|---------|
>| 每秒请求数 | requests-per-second | 计数 | 请求速率。 |
>| 请求总数 | total-requests | 计数 | 总请求数。 |
>| 当前请求数 | current-requests | 计数 | 当前请求的数目。 |
>| 失败的请求 | failed-requests | 计数 | 失败的请求数。 |

有关详细信息，请参阅 [dotnet 计数器](/dotnet/core/diagnostics/dotnet-counters)。

### <a name="session"></a>会话

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | Spring Actuator 指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat.sessions.active.max | tomcat.sessions.active.max | 计数 | 同时处于活动状态的会话的最大数量 |
>| tomcat.sessions.alive.max | tomcat.sessions.alive.max | 毫秒 | 过期会话处于活动状态的最长时间（秒） |
>| tomcat.sessions.created | tomcat.sessions.created | 计数 | 已创建的会话数 |
>| tomcat.sessions.expired | tomcat.sessions.expired | 计数 | 已过期的会话数 |
>| tomcat.sessions.rejected | tomcat.sessions.rejected | 计数 | 由于达到了最大活动会话数而未创建的会话数。 |
>| tomcat.sessions.active.current | tomcat.sessions.active.current | 计数 | Tomcat 会话活动计数 |

### <a name="ingress"></a>流入量

>[!div class="mx-tdCol2BreakAll"]
>| 显示名称             | Azure 指标名称        | 计价单位           | 详细信息                                                                                                                                                                          |
>|--------------------------|--------------------------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
>| 接收的字节数           | IngressBytesReceived     | 字节          | 由 Azure Spring Cloud 从客户端接收的字节的计数                                                                                                                   |
>| 发送的字节数               | IngressBytesSent         | 字节          | 由 Azure Spring Cloud 发送到客户端的字节的计数                                                                                                                         |
>| 请求                 | IngressRequests          | 计数          | 由 Azure Spring Cloud 从客户端发出的请求的计数                                                                                                                         |
>| 失败的请求数          | IngressFailedRequests    | 计数          | 由 Azure Spring Cloud 从客户端发出的失败请求的计数                                                                                                                  |
>| 响应状态          | IngressResponseStatus    | 计数          | 由 Azure Spring Cloud 返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应 |
>| 响应时间            | IngressResponseTime      | 秒        | 由 Azure Spring Cloud 返回的 http 响应时间                                                                                                                                  |
>| 入口吞吐量(字节/秒)  | IngressBytesReceivedRate | 每秒字节数 | 每秒由 Azure Spring Cloud 从客户端接收的字节数                                                                                                                 |
>| 出口吞吐量(字节/秒) | IngressBytesSentRate     | 每秒字节数 | 每秒由 Azure Spring Cloud 发送到客户端的字节数                                                                                                                       |

## <a name="next-steps"></a>后续步骤

* [快速入门：通过日志、指标和跟踪来监视 Azure Spring Cloud 应用](./quickstart-logs-metrics-tracing.md)
* [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)
* [通过诊断设置分析日志和指标](./diagnostic-services.md)
* [教程：使用警报和操作组监视 Spring Cloud 资源](./tutorial-alerts-action-groups.md)
* [Azure Spring Cloud 的配额和服务计划](./quotas.md)
