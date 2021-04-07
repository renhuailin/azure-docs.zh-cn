---
title: 正常停止应用服务器。
description: 本文提供有关正常关闭 SignalR 应用服务器的信息
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201665"
---
# <a name="server-graceful-shutdown"></a>服务器正常关闭
Microsoft Azure SignalR 服务提供正常关闭服务器的两种模式。 

使用此功能的主要优点是可以防止客户遇到意外的连接中断。 

可以等待客户端连接根据业务逻辑自行关闭，甚至可以将客户端连接迁移到另一台服务器而不会丢失数据。 

## <a name="how-it-works"></a>工作原理

通常，正常关闭过程将分为四个阶段：

1. **将服务器设置为脱机**

    这意味着不再有客户端连接将路由到该服务器。

2. 触发器 `OnShutdown` 挂钩

    可以为你在服务器中拥有的每个中心注册关闭挂钩。
    在我们从 Azure SignalR 服务收到 FINACK 响应后，系统将立即根据注册的订单调用它们，这意味着该服务器已在 Azure SignalR 服务中设置为脱机状态。

    可以在此阶段广播消息或执行一些清理作业，一旦执行完所有关闭挂钩，我们将进入下一阶段。

3. 等待所有客户端连接完成，具体取决于你选择的模式，可能是：

    **模式设置为 WaitForClientsToClose**

    Azure SignalR 服务将保留现有客户端。

    你需要设计一种方法，例如向所有客户端广播关闭消息，然后让你的客户端决定何时自行关闭/重新连接。

    阅读 [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) 了解示例用法，我们会广播“退出”消息来触发关闭挂钩中的客户端关闭。

    **模式设置为 MigrateClients**

    Azure SignalR 服务将尝试将此服务器上的客户端连接重新路由到另一台有效服务器。 
    
    在这种情况下，`OnConnectedAsync` 和 `OnDisconnectedAsync` 将分别在新服务器和旧服务器上触发，并在 `HttpContext` 中设置一个 `IConnectionMigrationFeature`，它可用于标识客户端连接正在迁入还是迁出。对于有状态场景，它尤其有用。

    传递当前消息后，客户端连接将立即迁移，这意味着下一条消息将被路由到新服务器。

4. **停止服务器连接**

    在关闭/迁移所有客户端连接或超过超时（默认为 30 秒）之后，

    SignalR Server SDK 将继续关闭过程到此阶段，并关闭所有服务器连接。

    如果未能关闭/迁移，客户端连接仍将被删除。 例如，没有合适的目标服务器/当前客户端到服务器的消息尚未完成。

## <a name="sample-codes"></a>示例代码。

在 `AddAzureSignalR` 时，添加以下选项：

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>在将正常关闭模式设置为 `MigrateClients` 时，配置 `OnConnected` 和 `OnDisconnected`。

我们引入了“IConnectionMigrationFeature”以指示连接正在迁入/迁出。

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```