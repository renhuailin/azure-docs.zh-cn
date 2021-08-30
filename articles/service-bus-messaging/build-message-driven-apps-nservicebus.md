---
title: 使用 NServiceBus 和 Azure 服务总线生成消息驱动的应用程序
description: 了解如何使用 NServiceBus 框架解决 Azure 服务总线上分布式系统的复杂问题。
author: kbaley
ms.author: spelluru
ms.service: service-bus-messaging
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: template-how-to
ms.openlocfilehash: c9f503cb600c87e1dac590dcbbe7edf46bc7be76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777795"
---
# <a name="build-message-driven-business-applications-with-nservicebus-and-azure-service-bus"></a>使用 NServiceBus 和 Azure 服务总线生成消息驱动的商业应用程序
NServiceBus 是 Particular Software 提供的商务消息传递框架。 它基于 Azure 服务总线而构建，可帮助开发人员将注意力放在业务逻辑上，而无需考虑基础结构方面的问题。 在本指南中，我们将生成一个解决方案，用于在两个服务之间交换消息。 我们还将演示如何自动重试失败的消息，并了解用于在 Azure 中托管这些服务的选项。

> [!NOTE]
> [Particular Software 文档网站](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/)上提供了本教程的代码。 

## <a name="prerequisites"></a>先决条件

示例假设已[创建 Azure 服务总线命名空间](service-bus-create-namespace-portal.md)。

> [!IMPORTANT]
> NServiceBus 至少需要标准层。 不能使用基本层。

## <a name="download-and-prepare-the-solution"></a>下载并准备解决方案
1. 从 [Particular Software 文档网站](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/)下载代码。 解决方案 `SendReceiveWithNservicebus.sln` 包括三个项目：

    - 发送方：发送消息的控制台应用程序
    - 接收方：从发送方接收消息并给予回复的控制台应用程序
    - 共享：一个类库，包含发送方与接收方之间共享的消息协定
    
    以下示意图由 Particular Software 可视化和调试工具 [ServiceInsight](https://particular.net/serviceinsight) 生成，其中显示了消息流：
    
    :::image type="content" source="./media/nservicebus/sequence-diagram.png" alt-text="该插图显示顺序示意图":::    
1. 在你偏好的代码编辑器（例如 Visual Studio 2019）中打开 `SendReceiveWithNservicebus.sln`。 
1. 打开“接收方”和“发送方”项目中的 `appsettings.json`，将 `AzureServiceBusConnectionString` 设置为 Azure 服务总线命名空间的连接字符串。



## <a name="define-the-shared-message-contracts"></a>定义共享消息协定

在“共享”类库中定义用于发送消息的协定。 该库包含对 `NServiceBus` NuGet 包的引用，而该包包含可用于标识消息的接口。 这些接口不是必需的，但使用它们可以通过 NServiceBus 进行某种额外的验证，并使代码具有自述性。

首先让我们了解 `Ping.cs` 类

```csharp
public class Ping : NServiceBus.ICommand
{
    public int Round { get; set; }
}
```

`Ping` 类定义由发送方发送到接收方的消息。 它是一个简单的 C# 类，实现 NServiceBus 包中的接口 `NServiceBus.ICommand`。 此消息向读取器和 NServiceBus 表明它是一条命令，不过，在[不使用接口](https://docs.particular.net/nservicebus/messaging/conventions)的情况下也可以通过其他方式标识消息。

“共享”项目中的另一个消息类是 `Pong.cs`：

```csharp
public class Pong : NServiceBus.IMessage
{
    public string Acknowledgement { get; set; }
}
```

`Pong` 也是一个简单的 C# 对象，不过它实现的是 `NServiceBus.IMessage`。 `IMessage` 接口表示既不是命令也不是事件的泛型消息，通常用于回复。 在本示例中，它是接收方发送回发送方的回复，指出已收到消息。

`Ping` 和 `Pong` 是你要使用的两种消息类型。 下一步是将发送方配置为使用 Azure 服务总线并发送 `Ping` 消息。

## <a name="set-up-the-sender"></a>设置发送方

发送方是发送 `Ping` 消息的终结点。 在此处，将发送方配置为使用 Azure 服务总线作为传输机制，然后构造一个 `Ping` 实例并发送该实例。

在 `Program.cs` 的 `Main` 方法中，配置发送方终结点：

```csharp
var host = Host.CreateDefaultBuilder(args)
    // Configure a host for the endpoint
    .ConfigureLogging((context, logging) =>
    {
        logging.AddConfiguration(context.Configuration.GetSection("Logging"));

        logging.AddConsole();
    })
    .UseConsoleLifetime()
    .UseNServiceBus(context =>
    {
        // Configure the NServiceBus endpoint
        var endpointConfiguration = new EndpointConfiguration("Sender");

        var transport = endpointConfiguration.UseTransport<AzureServiceBusTransport>();
        var connectionString = context.Configuration.GetConnectionString("AzureServiceBusConnectionString");
        transport.ConnectionString(connectionString);

        transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

        endpointConfiguration.EnableInstallers();
        endpointConfiguration.AuditProcessedMessagesTo("audit");

        return endpointConfiguration;
    })
    .ConfigureServices(services => services.AddHostedService<SenderWorker>())
    .Build();

await host.RunAsync();
```

此处有许多内容有待了解，下面让我们逐步了解。

### <a name="configure-a-host-for-the-endpoint"></a>为终结点配置主机

托管和日志记录是使用标准的 [Microsoft 通用主机选项](/dotnet/core/extensions/generic-host)配置的。 目前，终结点配置为作为控制台应用程序运行，但只需进行少量的更改即可将其修改为在 Azure Functions 中运行，本文稍后将介绍相关操作。

### <a name="configure-the-nservicebus-endpoint"></a>配置 NServiceBus 终结点

接下来，使用 `.UseNServiceBus(…)` 扩展方法告知主机要使用 NServiceBus。 该方法采用一个回调函数，该函数返回当主机运行时要启动的终结点。

在终结点配置中，为传输指定 `AzureServiceBus`，并提供 `appsettings.json` 中的连接字符串。 接下来，设置路由以将 `Ping` 类型的消息发送到名为“Receiver”的终结点。 这样，无需接收方的地址，NServiceBus 便可以自动完成将消息发送到目标的过程。

当终结点启动时，`EnableInstallers` 调用将在 Azure 服务总线命名空间中设置拓扑，并在必要时创建所需的队列。 在生产环境中，[操作脚本](https://docs.particular.net/transports/azure-service-bus/operational-scripting)是用于创建拓扑的另一个选项。

### <a name="set-up-background-service-to-send-messages"></a>设置后台服务以发送消息

发送方的最后一个片段是 `SenderWorker`（配置为每秒发送一条 `Ping` 消息的后台服务）。

```csharp
public class SenderWorker : BackgroundService
{
    private readonly IMessageSession messageSession;
    private readonly ILogger<SenderWorker> logger;

    public SenderWorker(IMessageSession messageSession, ILogger<SenderWorker> logger)
    {
        this.messageSession = messageSession;
        this.logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        try
        {
            var round = 0;
            while (!stoppingToken.IsCancellationRequested)
            {
                await messageSession.Send(new Ping { Round = round++ })
                    .ConfigureAwait(false);

                logger.LogInformation($"Message #{round}");

                await Task.Delay(1_000, stoppingToken)
                    .ConfigureAwait(false);
            }
        }
        catch (OperationCanceledException)
        {
            // graceful shutdown
        }
    }
}
```

在 `ExecuteAsync` 中使用的 `IMessageSession` 将注入到 `SenderWorker`，使我们能够在消息处理程序外部使用 NServiceBus 发送消息。 在 `Sender` 中配置的路由指定 `Ping` 消息的目标。 它将系统的拓扑（哪些消息将路由到哪些地址）保持为与业务代码分开的考虑因素。

发送方应用程序还包含 `PongHandler`。 在接下来我们介绍完接收方之后，我们将回头了解此处理程序。

## <a name="set-up-the-receiver"></a>设置接收方

接收方是侦听 `Ping` 消息、在收到消息时记录日志并回复发送方的终结点。 在本部分，我们将快速了解与发送方类似的终结点配置，然后将注意力转向消息处理程序。

与发送方一样，使用 Microsoft 通用主机将接收方设置为控制台应用程序。 接收方使用相同的日志记录和终结点配置（使用 Azure 服务总线作为消息传输机制），但使用不同的名称以区别于发送方：

```csharp
var endpointConfiguration = new EndpointConfiguration("Receiver");
```

由于此终结点仅回复其发起方且不启动新对话，因此无需进行路由配置。 此外，与发送方不同，接收方不需要后台工作线程，因为它只在收到消息时才给予回复。

### <a name="the-ping-message-handler"></a>Ping 消息处理程序

“接收方”项目包含名为 `PingHandler` 的消息处理程序：

```csharp
public class PingHandler : NServiceBus.IHandleMessages<Ping>
{
    private readonly ILogger<PingHandler> logger;

    public PingHandler(ILogger<PingHandler> logger)
    {
        this.logger = logger;
    }

    public async Task Handle(Ping message, IMessageHandlerContext context)
    {
        logger.LogInformation($"Processing Ping message #{message.Round}");

        // throw new Exception("BOOM");

        var reply = new Pong { Acknowledgement = $"Ping #{message.Round} processed at {DateTimeOffset.UtcNow:s}" };

        await context.Reply(reply);
    }
}
```

让我们暂时忽略带注释的代码；稍后我们在介绍如何从故障中恢复时再回头了解这些代码。

该类实现 `IHandleMessages<Ping>`，后者定义一个方法：`Handle`。 此接口告知 NServiceBus，当终结点收到 `Ping` 类型的消息时，应由此处理程序中的 `Handle` 方法处理该消息。 `Handle` 方法将消息本身用作参数，此外，它还采用 `IMessageHandlerContext`（用于进一步执行消息操作，例如回复、发送命令或发布事件）。

`PingHandler` 非常直接：收到 `Ping` 消息时，将记录消息详细信息，并通过新的 `Pong` 消息回复发送方。

> [!NOTE]
> 在发送方的配置中，你已指定应将 `Ping` 消息路由到接收方。 NServiceBus 在消息中添加元数据，用于指示消息来源等信息。 因此，你无需为 `Pong` 回复消息指定任何路由数据；该消息会自动路由回到其来源：发送方。
>

正确配置发送方和接收方后，接下来可以运行解决方案。

## <a name="run-the-solution"></a>运行解决方案

若要启动解决方案，需要运行发送方和接收方。 如果使用的是 Visual Studio Code，请启动“全部调试”配置。 如果使用的是 Visual Studio，请将解决方案配置为启动“发送方”和“接收方”项目：

1. 在解决方案资源管理器中，右键单击该解决方案
1. 选择“设置启动项目...”
1. 选择“多个启动项目”
1. 在下拉列表中选择“发送方”和“接收方”对应的“启动”

启动解决方案。 此时将显示两个控制台应用程序，一个对应于发送方，另一个对应于接收方。

# <a name="sender"></a>[发送方](#tab/Sender)

:::image type="content" source="./media/nservicebus/sender.png" alt-text="该插图显示向接收方发送 Ping 消息的终结点":::

# <a name="receiver"></a>[接收方](#tab/Receiver)

:::image type="content" source="./media/nservicebus/receiver.png" alt-text="该插图显示从发送方接收 Ping 消息的终结点":::

---

在发送方中可以看到，由于运行了 `SenderWorker` 后台作业，已每秒发送一条 `Ping` 消息。 接收方会显示它收到的每条 `Ping` 消息的详细信息，而发送方会在回复中记录它收到的每条 `Pong` 消息的详细信息。

现在一切都在正常进行，接下来让我们搞点破坏。

## <a name="resilience-in-action"></a>复原的运作方式

出错是软件系统中司空见惯的事情。 代码会不可避免地出于各种原因而失败，例如网络故障、数据库锁定、第三方 API 的更改以及普通的代码错误。

NServiceBus 提供可靠的可恢复性功能用于处理故障。 当消息处理程序失败时，系统会根据预定义的策略自动重试消息。 有两种类型的重试策略：立即重试和延迟重试。 为了描述两者的工作原理，最好是先了解两者的运作方式。 让我们在接收方终结点中添加一个重试策略：

1. 打开“发送方”项目中的 `Program.cs`
1. 在 `.EnableInstallers` 行的后面添加以下代码：

```csharp
endpointConfiguration.SendFailedMessagesTo("error");
var recoverability = endpointConfiguration.Recoverability();
recoverability.Immediate(
    immediate =>
    {
        immediate.NumberOfRetries(3);
    });
recoverability.Delayed(
    delayed =>
    {
        delayed.NumberOfRetries(2);
        delayed.TimeIncrease(TimeSpan.FromSeconds(5));
    });
```

在介绍此策略的工作原理之前，让我们了解它的运作方式。 在测试可恢复性策略之前，需要模拟某个错误。 打开“接收方”项目中的 `PingHandler` 代码，取消注释以下行：

```csharp
throw new Exception("BOOM");
```

现在，当接收方处理 `Ping` 消息时，处理将会失败。 再次启动解决方案，看看接收方中发生了什么情况。 

由于 `PingHandler` 不够可靠，所有消息都已失败。 可以看到，已针对这些消息启动了重试策略。 当消息首次失败时，会立即重试最多 3 次：

:::image type="content" source="./media/nservicebus/immediate-retries.png" alt-text="该插图显示立即重试策略重试消息最多 3 次":::

当然，消息会继续失败，因此，在用完了三次立即重试后，将启动延迟重试策略，此时消息会延迟 5 秒：

:::image type="content" source="./media/nservicebus/delayed-retries.png" alt-text="该插图显示延迟重试策略，该策略在尝试另一轮立即重试之前以 5 秒增量延迟消息":::

 在 5 秒过后，将再次重试消息三次（即，再一次迭代立即重试策略）。 这些消息还是会失败，此时，NServiceBus 将再次延迟消息，不过这一次会延迟 10 秒，然后重试。

如果 `PingHandler` 在运行整个重试策略后仍然失败，则会将消息放入一个命名为 `error`、由 `SendFailedMessagesTo` 调用定义的集中式错误队列中。 

:::image type="content" source="./media/nservicebus/failed-message.png" alt-text="该插图显示失败的消息":::

集中式错误队列的概念不同于 Azure 服务总线中的死信机制，后者为每个处理队列提供死信队列。 使用 NServiceBus 时，Azure 服务总线中的死信队列充当真正的有害消息队列，而最终放入集中式错误队列的消息可在以后根据需要重新进行处理。

重试策略有助于解决暂时性或半暂时性的[多种类型的错误](https://particular.net/blog/but-all-my-errors-are-severe)， 即，只需在经过短暂的延迟后，重新处理消息通常就会消失的暂时性错误。 示例包括网络故障、数据库锁定和第三方 API 中断。

将消息放入错误队列后，可以在所选的工具中检查消息详细信息，然后决定如何进行处理。 例如，使用 Particular Software 的监视工具 [ServicePulse](https://particular.net/servicepulse)，可以查看消息详细信息和失败原因：

:::image type="content" source="./media/nservicebus/servicepulse.png" alt-text="该插图显示 Particular Software 的 ServicePulse":::

检查详细信息后，可将消息发回到其原始队列进行处理。 也可以在编辑该消息后再执行此操作。 如果错误队列中包含由于相同原因而失败的多个消息，可将所有这些消息以批的形式发回到其原始目标。

接下来，确定在 Azure 中的哪个位置部署我们的解决方案。

## <a name="where-to-host-the-services-in-azure"></a>在 Azure 中的哪个位置托管服务

在此示例中，发送方和接收方终结点配置为以控制台应用程序的形式运行。 它们也可以托管在 Azure Functions、Azure 应用服务、Azure 容器实例、Azure Kubernetes 服务和 Azure VM 等各种 Azure 服务中。 例如，下面演示如何将发送方终结点配置为以 Azure 函数的形式运行：

```csharp
[assembly: FunctionsStartup(typeof(Startup))]
[assembly: NServiceBusEndpointName("Sender")]

public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.UseNServiceBus(() =>
        {
            var configuration = new ServiceBusTriggeredEndpointConfiguration("Sender");
            var transport = configuration.AdvancedConfiguration.Transport;
            transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

            return configuration;
        });
    }
}
```

有关将 NServiceBus 与 Functions 配合使用的详细信息，请参阅 NServiceBus 文档中的[将 Azure Functions 与 Azure 服务总线配合使用](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)。

## <a name="next-steps"></a>后续步骤

有关将 NServiceBus 与 Azure 服务配合使用的详细信息，请参阅以下文章：

- [Azure 服务总线发送/回复示例](https://docs.particular.net/samples/azure-service-bus-netstandard/send-reply/)
- [将 NServiceBus 与 Azure Functions 配合使用](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)
- [NServiceBus 上的 Azure 服务总线传输](https://docs.particular.net/transports/azure-service-bus/)
- [NServiceBus 和 Azure](https://docs.particular.net/nservicebus/azure/)
- [NServiceBus](https://particular.net/nservicebus)
- [NServiceBus 快速入门教程](https://docs.particular.net/tutorials/quickstart)
