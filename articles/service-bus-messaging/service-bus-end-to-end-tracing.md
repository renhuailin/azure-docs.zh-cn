---
title: Azure 服务总线端到端跟踪和诊断 | Microsoft Docs
description: 服务总线客户端诊断和端到端跟踪概述（涉及处理的所有服务均经由的客户端）。
ms.topic: article
ms.date: 01/17/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: edfd789f8803acf9fc8d76202805dec0187d220e
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601248"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>通过服务总线消息传递进行分布式跟踪和关联

微服务开发中的常见问题之一是能够通过处理中涉及的所有服务从客户端跟踪操作。 此能力对于进行调试、性能分析、A/B 测试和其他典型诊断方案相当有用。
此问题的一个组成部分是跟踪工作的逻辑片段。 这包括消息处理结果和延迟，以及外部依赖项调用。 另一个组成部分是这些诊断事件在进程边界外部的关联。

当生成者通过队列发送消息时，此活动通常发生在其他某个逻辑操作的范围内，并由其他客户端或服务启动。 当使用者收到消息时，会继续相同的操作。 生成者与使用者（以及其他处理该操作的服务）也许会发出遥测事件，以跟踪操作流和结果。 若要将此类事件相关联并以端到端的方式跟踪操作，报告遥测数据的每个服务必须为每个事件提供跟踪上下文戳记。

Microsoft Azure 服务总线消息传递已定义生成者与使用者应该用来传递此类跟踪上下文的有效负载属性。
该协议基于 [HTTP 关联协议](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)。

# <a name="azuremessagingservicebus-sdk-latest"></a>[ (最新) 的 Azure 消息传送。 ](#tab/net-standard-sdk-2)
| 属性名        | 说明                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | 生成者针对队列发出的外部调用的唯一标识符。 请参阅 [HTTP 协议中的 Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) 了解事实依据、注意事项和格式 |

## <a name="service-bus-net-client-autotracing"></a>服务总线 .NET 客户端自动跟踪
`ServiceBusProcessor`[适用于 .Net 的 Azure 消息服务总线客户端](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)的类提供跟踪系统或客户端代码的一部分可以挂钩的跟踪检测点。 使用检测可以从客户端跟踪对服务总线消息传递服务发出的所有调用。 如果消息处理[ `ProcessMessageAsync` `ServiceBusProcessor` 是使用 (](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync)消息处理程序模式) 完成的，则还会检测消息处理。

### <a name="tracking-with-azure-application-insights"></a>使用 Azure Application Insights 进行跟踪

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) 提供丰富的性能监视功能，包括自动请求和依赖项跟踪。

请根据项目类型安装 Application Insights SDK：
- [ASP.NET](../azure-monitor/app/asp-net.md) - 安装版本 2.5-beta2 或更高版本
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - 安装版本 2.2.0-beta2 或更高版本。
这些链接提供了有关安装 SDK、创建资源和配置 SDK（如果需要）的详细信息。 针对非 ASP.NET 应用程序，请参阅[适用于控制台应用程序的 Azure Application Insights](../azure-monitor/app/console.md) 一文。

如果[ `ProcessMessageAsync` `ServiceBusProcessor` 使用 (](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync)消息处理程序模式) 来处理消息，则还会检测消息处理。 服务完成的所有服务总线调用会自动进行跟踪，并与其他遥测项关联。 否则，请参考以下示例手动进行消息处理跟踪。

#### <a name="trace-message-processing"></a>跟踪消息处理

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

在此示例中，将为每个已处理的消息报告请求遥测，时间戳、持续时间和结果 (成功) 。 遥测功能也会提供一组关联属性。 在消息处理期间报告的嵌套跟踪和异常也带有关联属性的戳记，代表它们是 `RequestTelemetry` 的“子级”。

如果在消息处理过程中对支持的外部组件进行调用，则它们也会自动进行跟踪和关联。 请参阅[使用 Application Insights .NET SDK 跟踪自定义操作](../azure-monitor/app/custom-operations-tracking.md)来了解手动跟踪和关联。

如果除 Application Insights SDK 外，还在运行任何外部代码，则查看 Application Insights 日志时应会看到较长的 **持续时间** 。 

![Application Insights 日志中更长的持续时间](./media/service-bus-end-to-end-tracing/longer-duration.png)

这并不意味着接收消息时存在延迟。 在这种情况下，消息已经被接收，因为消息以参数的形式传递给 SDK 代码。 而且，App Insights 日志（进程）中的“名称”标记指示消息正在由外部事件处理代码处理 。 此问题不与 Azure 相关。 相反，这些指标指示的是外部代码的效率，前提是已从服务总线接收到消息。 

### <a name="tracking-without-tracing-system"></a>在没有跟踪系统的情况下进行跟踪
如果跟踪系统不支持自动服务总线调用跟踪，则可能要考虑将此类支持添加到跟踪系统或应用程序中。 本部分介绍服务总线 .NET 客户端发送的诊断事件。  

使用 .NET 跟踪基元 [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) 和 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 检测服务总线 .NET 客户端。

`Activity` 充当跟踪上下文，而 `DiagnosticSource` 是通知机制。 

如果 DiagnosticSource 事件没有侦听器，则检测将关闭，从而使检测成本保持为零。 DiagnosticSource 将所有控制权授予侦听器：
- 侦听器控制要侦听的源和事件
- 侦听器控制事件速率和采样
- 事件连同某个有效负载一起发送，该有效负载可提供完整上下文，使你能够在事件发生期间访问和修改 Message 对象

在继续实现之前，请先通过 [DiagnosticSource 用户指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)熟悉相关流程。

让我们在 ASP.NET Core 应用中针对服务总线事件创建一个侦听器，以通过 Microsoft.Extension.Logger 写入日志。
该侦听器使用 [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) 库来订阅 DiagnosticSource（不使用它也能轻松订阅 DiagnosticSource）

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

在此示例中，侦听器记录每个服务总线操作的持续时间、结果、唯一标识符和开始时间。

### <a name="events"></a>事件
对于每个操作，将发送两个事件：“Start”和“Stop”。 大多数情况下，你只对 "停止" 事件感兴趣。 它们提供操作的结果，并将开始时间和持续时间作为活动属性。

每个事件有效负载为侦听器提供操作上下文，并复制 API 传入参数和返回值。 “Stop”事件有效负载具有“Start”事件有效负载的所有属性，因此可以完全忽略“Start”事件。

每个“Stop”事件具有表示 `TaskStatus` 异步操作已完成的 `Status` 属性，为便于阅读，下表中也省略了该属性。

所有事件都具有以下属性，这些属性符合打开的遥测规范： https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md 。

- `message_bus.destination` –队列/主题/订阅路径
- `peer.address` –完全限定的命名空间
- `kind` –制造者、使用者或客户端。 生成消息时，在接收时使用制造者，并在进行结算时使用客户端。
- `component` – `servicebus`

所有事件还具有 "实体" 和 "终结点" 属性，下表中省略了这些属性
  * `string Entity` --实体 (队列和主题的名称，等等 ) 
  * `Uri Endpoint` - 服务总线终结点 URL

### <a name="instrumented-operations"></a>检测操作
下面是已检测操作的完整列表：

| 操作名称 | 跟踪的 API |
| -------------- | ----------- | 
| ServiceBusSender | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender | ServiceBusSender. ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver | ServiceBusReceiver. PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver 完成 | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. 死信 | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor. ProcessMessage | ServiceBusProcessor 上的处理器回调集。 ProcessMessageAsync 属性 |
| ServiceBusSessionProcessor.ProcessSessionMessage | ServiceBusSessionProcessor 上的处理器回调集。 ProcessMessageAsync 属性 |

### <a name="filtering-and-sampling"></a>筛选和采样

在某些情况下，我们可能只需记录一部分事件，以减少性能开销或存储耗用量。 可以只记录“Stop”事件（如上述示例所示），或者对特定百分比的事件采样。 
使用 `DiagnosticSource` 可以通过 `IsEnabled` 谓词实现此目的。 有关详细信息，请参阅 [DiagnosticSource 中基于上下文的筛选](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)。

可以针对单个操作调用 `IsEnabled` 多次，以将性能影响降到最低。

按以下顺序调用 `IsEnabled`：

1. `IsEnabled(<OperationName>, string entity, null)`，例如 `IsEnabled("ServiceBusSender.Send", "MyQueue1")`。 请注意，末尾没有 "开始" 或 "停止"。 使用此语句可以筛选出特定的操作或队列。 如果回调方法返回 `false` ，则不会发送操作的事件。

   * 对于“Process”和“ProcessSession”操作，还会收到 `IsEnabled(<OperationName>, string entity, Activity activity)` 回调。 使用此回调可根据 `activity.Id` 或 Tags 属性筛选事件。
  
2. `IsEnabled(<OperationName>.Start)`，例如 `IsEnabled("ServiceBusSender.Send.Start")`。 检查是否应激发“Start”事件。 结果只影响 "开始" 事件，但进一步检测并不依赖于它。

没有 `IsEnabled` "停止" 事件。

如果某个操作结果为异常，则会调用 `IsEnabled("ServiceBusSender.Send.Exception")`。 只能订阅“Exception”事件并阻止剩余的检测。 在此情况下，仍需处理此类异常。 由于其他检测已禁用，因此，不应期望跟踪上下文与来自使用者的消息一起流动。

也可以使用 `IsEnabled` 来实现采样策略。 基于或的采样 `Activity.Id` `Activity.RootId` 可确保所有轮 (的采样一致，只要它通过跟踪系统或您自己的代码) 传播即可。

对于同一源存在多个 `DiagnosticSource` 侦听器，只需一个侦听器即可接受该事件，因此无法保证 `IsEnabled` 调用。



# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

| 属性名        | 说明                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | 生成者针对队列发出的外部调用的唯一标识符。 请参阅 [HTTP 协议中的 Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) 了解事实依据、注意事项和格式 |
|  Correlation-Context | 操作上下文，将传播到操作处理流程涉及到的所有服务。 有关详细信息，请参阅 [HTTP 协议中的 Correlation-Context](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>服务总线 .NET 客户端自动跟踪

从版本 3.0.0 开始，[适用于 .NET 的 Microsoft Azure 服务总线客户端](/dotnet/api/microsoft.azure.servicebus.queueclient)提供可由跟踪系统或客户端代码片段挂接的跟踪检测点。
使用检测可以从客户端跟踪对服务总线消息传递服务发出的所有调用。 如果消息处理是通过[消息处理程序模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)完成的，则还会检测消息处理

### <a name="tracking-with-azure-application-insights"></a>使用 Azure Application Insights 进行跟踪

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) 提供丰富的性能监视功能，包括自动请求和依赖项跟踪。

请根据项目类型安装 Application Insights SDK：
- [ASP.NET](../azure-monitor/app/asp-net.md) - 安装版本 2.5-beta2 或更高版本
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - 安装版本 2.2.0-beta2 或更高版本。
这些链接提供了有关安装 SDK、创建资源和配置 SDK（如果需要）的详细信息。 针对非 ASP.NET 应用程序，请参阅[适用于控制台应用程序的 Azure Application Insights](../azure-monitor/app/console.md) 一文。

如果使用 [消息处理程序模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) 来处理消息，则已完成：会自动跟踪服务完成的所有服务总线调用，并将其与其他遥测项相关联。 否则，请参考以下示例手动进行消息处理跟踪。

#### <a name="trace-message-processing"></a>跟踪消息处理

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

在本示例中，系统针对每个已处理的消息报告 `RequestTelemetry`，并提供时间戳、持续时间和结果（成功）。 遥测功能也会提供一组关联属性。
在消息处理期间报告的嵌套跟踪和异常也带有关联属性的戳记，代表它们是 `RequestTelemetry` 的“子级”。

如果在消息处理过程中对支持的外部组件进行调用，则它们也会自动进行跟踪和关联。 请参阅[使用 Application Insights .NET SDK 跟踪自定义操作](../azure-monitor/app/custom-operations-tracking.md)来了解手动跟踪和关联。

如果除 Application Insights SDK 外，还在运行任何外部代码，则查看 Application Insights 日志时应会看到较长的 **持续时间** 。 

![Application Insights 日志中更长的持续时间](./media/service-bus-end-to-end-tracing/longer-duration.png)

这并不意味着接收消息时存在延迟。 在这种情况下，消息已经被接收，因为消息以参数的形式传递给 SDK 代码。 而且，App Insights 日志（进程）中的“名称”标记指示消息正在由外部事件处理代码处理 。 此问题不与 Azure 相关。 相反，这些指标指示的是外部代码的效率，前提是已从服务总线接收到消息。 请参阅 [GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs)，了解从服务总线接收到消息后，生成和分配“进程”标记的位置。 

### <a name="tracking-without-tracing-system"></a>在没有跟踪系统的情况下进行跟踪
如果跟踪系统不支持自动服务总线调用跟踪，则可能要考虑将此类支持添加到跟踪系统或应用程序中。 本部分介绍服务总线 .NET 客户端发送的诊断事件。  

使用 .NET 跟踪基元 [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) 和 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 检测服务总线 .NET 客户端。

`Activity` 充当跟踪上下文，而 `DiagnosticSource` 是通知机制。 

如果 DiagnosticSource 事件没有侦听器，则检测将关闭，从而使检测成本保持为零。 DiagnosticSource 将所有控制权授予侦听器：
- 侦听器控制要侦听的源和事件
- 侦听器控制事件速率和采样
- 事件连同某个有效负载一起发送，该有效负载可提供完整上下文，使你能够在事件发生期间访问和修改 Message 对象

在继续实现之前，请先通过 [DiagnosticSource 用户指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)熟悉相关流程。

让我们在 ASP.NET Core 应用中针对服务总线事件创建一个侦听器，以通过 Microsoft.Extension.Logger 写入日志。
该侦听器使用 [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) 库来订阅 DiagnosticSource（不使用它也能轻松订阅 DiagnosticSource）

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

在此示例中，侦听器记录每个服务总线操作的持续时间、结果、唯一标识符和开始时间。

#### <a name="events"></a>事件

对于每个操作，将发送两个事件：“Start”和“Stop”。 大多数情况下，你只对 "停止" 事件感兴趣。 它们提供操作的结果，并将开始时间和持续时间作为活动属性。

每个事件有效负载为侦听器提供操作上下文，并复制 API 传入参数和返回值。 “Stop”事件有效负载具有“Start”事件有效负载的所有属性，因此可以完全忽略“Start”事件。

所有事件还具有 "实体" 和 "终结点" 属性，下表中省略了这些属性
  * `string Entity` - 实体（队列、主题等）的名称
  * `Uri Endpoint` - 服务总线终结点 URL

每个“Stop”事件具有表示 `TaskStatus` 异步操作已完成的 `Status` 属性，为便于阅读，下表中也省略了该属性。

下面是已检测操作的完整列表：

| 操作名称 | 跟踪的 API | 特定的有效负载属性|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` - 正在发送的消息的列表 |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` - 正在处理的消息<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` - 计划消息偏移<br/>`long SequenceNumber` - 计划消息的序号（“Stop”事件负载） |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` - 要取消的消息序号 | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - 可接收的消息数上限。<br/>`IList<Message> Messages` - 已接收的消息列表（“Stop”事件负载） |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - 浏览消息批的起点。<br/>`int RequestedMessageCount` - 要检索的消息数目。<br/>`IList<Message> Messages` - 已接收的消息列表（“Stop”事件负载） |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - 包含要接收的序号的列表。<br/>`IList<Message> Messages` - 已接收的消息列表（“Stop”事件负载） |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - 包含要完成的相应消息的锁定标记的列表。|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - 要丢弃的相应消息的锁定标记。 |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - 要延迟的相应消息的锁定标记。 | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - 要加入死信队列的相应消息的锁定标记。 | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - 要续订锁定的相应消息的锁定标记。<br/>`DateTime LockedUntilUtc` - 以 UTC 格式显示的新锁定标记过期日期和时间。 （“Stop”事件有效负载）|
| Microsoft.Azure.ServiceBus.Process | 在 [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) 中提供的消息处理程序 Lambda 函数 | `Message Message` - 正在处理的消息。 |
| Microsoft.Azure.ServiceBus.ProcessSession | 在 [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) 中提供的消息会话处理程序 lambda 函数 | `Message Message` - 正在处理的消息。<br/>`IMessageSession Session` - 正在处理的会话 |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - 提供要添加的规则的规则说明。 |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - 要删除的规则的名称。 |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - 与订阅关联的所有规则。 （仅限“Stop”有效负载） |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - 消息中显示的 sessionId。 |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - 消息中显示的 sessionId。<br/>`byte [] State` - 会话状态（“Stop”事件负载） |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - 消息中显示的 sessionId。<br/>`byte [] State` - 会话状态 |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - 消息中显示的 sessionId。 |
| Microsoft.Azure.ServiceBus.Exception | 任何已检测的 API| `Exception Exception` - 异常实例 |

在每个事件中，可以访问保存当前操作上下文的 `Activity.Current`。

#### <a name="logging-more-properties"></a>记录更多属性

`Activity.Current` 提供当前操作及其父级的详细上下文。 有关详细信息，请参阅 [活动文档](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)。
Service Bus 检测功能提供了中的详细信息 `Activity.Current.Tags` `MessageId` `SessionId` 。

跟踪“Receive”、“Peek”和“ReceiveDeferred”事件的活动还可能带有 `RelatedTo` 标记。 该标记包含作为结果收到的消息的 `Diagnostic-Id` 相异列表。
此类操作可能导致收到多个不相关的消息。 此外， `Diagnostic-Id` 操作开始时未知，因此只能使用此标记将 "接收" 操作与 "进程" 操作相关联。 此标记在分析性能问题时相当有用，可以检查接收消息所花费的时间。

记录标记的有效方法是循环访问它们，将标记添加到上述示例后，结果如下所示： 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>筛选和采样

在某些情况下，我们可能只需记录一部分事件，以减少性能开销或存储耗用量。 可以只记录“Stop”事件（如上述示例所示），或者对特定百分比的事件采样。 
使用 `DiagnosticSource` 可以通过 `IsEnabled` 谓词实现此目的。 有关详细信息，请参阅 [DiagnosticSource 中基于上下文的筛选](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)。

可以针对单个操作调用 `IsEnabled` 多次，以将性能影响降到最低。

按以下顺序调用 `IsEnabled`：

1. `IsEnabled(<OperationName>, string entity, null)`，例如 `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`。 请注意，末尾没有 "开始" 或 "停止"。 使用此语句可以筛选出特定的操作或队列。 如果回调方法返回 `false` ，则不会发送操作的事件

   * 对于“Process”和“ProcessSession”操作，还会收到 `IsEnabled(<OperationName>, string entity, Activity activity)` 回调。 使用此回调可根据 `activity.Id` 或 Tags 属性筛选事件。
  
2. `IsEnabled(<OperationName>.Start)`，例如 `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`。 检查是否应激发“Start”事件。 结果只影响 "开始" 事件，但进一步检测并不依赖于它。

没有 `IsEnabled` "停止" 事件。

如果某个操作结果为异常，则会调用 `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`。 只能订阅“Exception”事件并阻止剩余的检测。 在此情况下，仍需处理此类异常。 由于其他检测已禁用，因此，不应期望跟踪上下文与来自使用者的消息一起流动。

也可以使用 `IsEnabled` 来实现采样策略。 基于或的采样 `Activity.Id` `Activity.RootId` 可确保所有轮 (的采样一致，只要它通过跟踪系统或您自己的代码) 传播即可。

对于同一源存在多个 `DiagnosticSource` 侦听器，只需一个侦听器即可接受该事件，因此无法保证 `IsEnabled` 调用。

---

## <a name="next-steps"></a>后续步骤

* [Application Insights 关联](../azure-monitor/app/correlation.md)
* 参阅 [Application Insights 监视依赖项](../azure-monitor/app/asp-net-dependencies.md)，了解 REST、SQL 或其他外部资源是否会降低性能。
* [使用 Application Insights .NET SDK 跟踪自定义操作](../azure-monitor/app/custom-operations-tracking.md)

