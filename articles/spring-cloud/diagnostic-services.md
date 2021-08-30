---
title: 在 Azure Spring Cloud 中分析日志和指标 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中分析诊断数据
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: e2d903f781e86670139347930289599bec6ee7e7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015552"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>通过诊断设置分析日志和指标

本文适用于：✔️ Java ✔️ C#

使用 Azure Spring Cloud 的诊断功能，可以通过以下任何服务分析日志和指标：

* 使用 Azure Log Analytics，其中数据将写入 Azure 存储。 将日志导出到 Log Analytics 时存在延迟。
* 将日志保存到存储帐户进行审核或手动检查。 可以指定保留时间（天）。
* 将日志流式传输到事件中心，供第三方服务或自定义分析解决方案引入。

选择要监视的日志类别和指标类别。

> [!TIP]
> 只想要流式传输日志？ 看看这个 [Azure CLI 命令](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)吧！

## <a name="logs"></a>日志

|日志 | 说明 |
|----|----|
| **ApplicationConsole** | 所有客户应用程序的控制台日志。 |
| **SystemLogs** | 当前，只有 [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) 记录在此类别中。 |

## <a name="metrics"></a>指标

有关指标的完整列表，请参阅 [Spring Cloud 指标](./concept-metrics.md#user-metrics-options)。

若要开始，请启用其中一项服务来接收数据。 若要了解如何配置 Log Analytics，请参阅[开始在 Azure Monitor 中使用 Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)。

## <a name="configure-diagnostics-settings"></a>配置诊断设置

1. 在 Azure 门户中，请转到 Azure Spring Cloud 实例。
1. 选择“诊断设置”选项，然后选择“添加诊断设置” 。
1. 输入设置的名称，然后选择要将日志发送到的位置。 可选择以下三个选项的任意组合：
    * **存档到存储帐户**
    * **流式传输到事件中心**
    * **发送到 Log Analytics**

1. 选择要监视的日志类别和指标类别，并指定保留时间（天）。 保留时间仅适用于存储帐户。
1. 选择“保存”。

> [!NOTE]
> 1. 发出日志或指标后，可能需要最多 15 分钟它们才会显示在存储帐户、事件中心或 Log Analytics 中。
> 1. 如果删除或移动了 Azure Spring Cloud 实例，则该操作将不会级联到诊断设置资源。 必须先手动删除“诊断设置”资源，然后才能对其父级（即 Azure Spring Cloud 实例）进行操作。 否则，如果为新的 Azure Spring Cloud 实例预配了与已删除实例相同的资源 ID，或者将 Azure Spring Cloud 实例移回，则先前的诊断设置资源将继续对其进行扩展。

## <a name="view-the-logs-and-metrics"></a>查看日志和指标

有多种方法可以查看日志和指标，如以下标题中所述。

### <a name="use-the-logs-blade"></a>使用“日志”边栏选项卡

1. 在 Azure 门户中，请转到 Azure Spring Cloud 实例。
1. 要打开“日志搜索”窗格，请选择“日志” 。
1. 在“表”搜索框中
   * 若要查看日志，请输入一个简单的查询，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

   * 若要查看指标，请输入一个简单的查询，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 若要查看搜索结果，请选择“运行”。

### <a name="use-log-analytics"></a>使用 Log Analytics

1. 在 Azure 门户的左窗格中选择“Log Analytics”。
1. 选择添加诊断设置时选择的 Log Analytics 工作区。
1. 要打开“日志搜索”窗格，请选择“日志” 。
1. 在“表”搜索框中，
   * 若要查看日志，请输入一个简单的查询，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

    * 若要查看指标，请输入一个简单的查询，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 若要查看搜索结果，请选择“运行”。
1. 可以通过设置筛选条件来搜索特定应用程序或实例的日志：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

    > [!NOTE]
    > `==` 区分大小写，但 `=~` 不区分大小写。

若要详细了解 Log Analytics 中使用的查询语言，请参阅 [Azure Monitor 日志查询](/azure/data-explorer/kusto/query/)。 要从一个集中的客户端查询所有 Log Analytics 日志，请查看 [Azure 数据资源管理器](/azure/data-explorer/query-monitor-data)。

### <a name="use-your-storage-account"></a>使用存储帐户

1. 在 Azure 门户中，在左侧导航面板或搜索框中查找“存储帐户”。
1. 选择添加诊断设置时选择的存储帐户。
1. 若要打开“Blob 容器”窗格，请选择“Blob” 。
1. 若要查看应用程序日志，请搜索名为 insights-logs-applicationconsole 的容器。
1. 若要查看应用程序指标，请搜索名为 insights-metrics-pt1m 的容器。

若要了解有关将诊断信息发送到存储帐户的详细信息，请参阅[在 Azure 存储中存储和查看诊断数据](../storage/common/storage-introduction.md)。

### <a name="use-your-event-hub"></a>使用事件中心

1. 在 Azure 门户中，在左侧导航面板或搜索框中查找“事件中心”。

1. 搜索并选择添加诊断设置时选择的事件中心。
1. 若要打开“事件中心列表”窗格，请选择“事件中心” 。
1. 若要查看应用程序日志，请搜索名为 insights-logs-applicationconsole 的事件中心。
1. 若要查看应用程序指标，请搜索名为 insights-metrics-pt1m 的事件中心。

若要了解有关将诊断信息发送到事件中心的详细信息，请参阅[使用事件中心流式处理热路径中的 Azure 诊断数据](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)。

## <a name="analyze-the-logs"></a>分析日志

Azure Log Analytics 与 Kusto 引擎一起运行，因此，你可以查询日志以进行分析。 有关使用 Kusto 查询日志的快速简介，请查看 [Log Analytics 教程](../azure-monitor/logs/log-analytics-tutorial.md)。

应用程序日志提供有关应用程序的运行状况、性能等的关键信息和详细日志。 下一节将介绍一些简单的查询，帮助你理解应用程序的当前和过去状态。

### <a name="show-application-logs-from-azure-spring-cloud"></a>显示 Azure Spring Cloud 中的应用程序日志

若要查看中 Azure Spring Cloud 的应用程序日志列表（按时间排序，首先显示最新日志），请运行以下查询：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>显示包含错误或异常的日志条目

若要查看提及错误或异常的未排序日志条目，请运行以下查询：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查询查找错误，或修改查询词以查找特定的错误代码或异常。

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>显示应用程序在过去一小时内报告的错误和异常数

若要创建显示应用程序在过去一小时记录的错误和异常数的饼图，请运行以下查询：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>详细了解查询应用程序日志

Azure Monitor 通过使用 Log Analytics 为查询应用程序日志提供了广泛的支持。 若要了解有关此服务的详细信息，请参阅 [Azure Monitor 中的日志查询入门](../azure-monitor/logs/get-started-queries.md)。 若要详细了解如何生成查询以分析应用程序日志，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/logs/log-query-overview.md)。

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>如何将多行 Java 堆栈跟踪转换为单行 Java 堆栈跟踪？

有一种变通方法可以将多行堆栈跟踪转换为单行堆栈跟踪。 可以修改 Java 日志输出，重新设置堆栈跟踪消息格式，从而将换行符替换为标记。 如果使用 Java Logback 库，可以通过添加 `%replace(%ex){'[\r\n]+', '\\n'}%nopex` 来重新设置堆栈跟踪消息格式，如下所示：

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```

然后可以在 Log Analytics 中再次将换行符替换为标记，如下所示：

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```

可以对其他 Java 日志库使用相同的策略。

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](./quickstart.md)
