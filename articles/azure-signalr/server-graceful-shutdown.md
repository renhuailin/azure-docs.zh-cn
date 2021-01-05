---
title: 正常停止应用服务器。
description: 本文提供了有关正常关闭 SignalR 应用服务器的信息
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 06aa91ff414e5575f7b1a743d2cc17765437ef72
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797458"
---
# <a name="server-graceful-shutdown"></a>服务器正常关闭
Microsoft Azure SignalR 服务提供了两种模式，用于正常关闭服务器。 

使用此功能的主要优点是防止客户意外出现断开连接的情况。 

相反，你可以等待你的客户端连接关闭自己的业务逻辑，甚至可以在不丢失数据的情况下将客户端连接迁移到另一台服务器。 

## <a name="how-it-works"></a>工作原理

通常，正常关机过程中将有四个阶段：

1. **将服务器设置为脱机**

    这意味着将不会有更多的客户端连接路由到此服务器。

2. **触发器 `OnShutdown` 挂钩**

    你可以为你在服务器中拥有的每个中心注册关闭挂钩。
    当我们从 Azure SignalR 服务收到 **FINACK** 响应后，就会立即按注册顺序调用这些服务器，这意味着此服务器已在 Azure SignalR 服务中进行了脱机设置。

    你可以在此阶段广播消息或执行一些清理作业，一旦执行了所有关闭挂钩，我们将继续执行下一阶段。

3. **等到所有客户端连接完成**，具体取决于所选模式，可以是：

    **模式设置为 WaitForClientsToClose**

    Azure SignalR 服务将保留现有的客户端。

    你可能需要设计一种方法，例如将一条关闭消息广播给所有客户端，然后让客户端决定何时关闭/重新连接自身。

    阅读 [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample) 以了解示例用法，我们广播 "exit" 消息以触发关闭挂钩中的客户端关闭。

    **模式设置为 MigrateClients**

    Azure SignalR 服务将尝试将此服务器上的客户端连接重新路由到另一个有效服务器。 
    
    在此方案中， `OnConnectedAsync` 并 `OnDisconnectedAsync` 将在新服务器和旧服务器上分别触发 `IConnectionMigrationFeature` `HttpContext` ，后者可用于确定是否迁移客户端连接-在我们的已迁移中。它对于有状态方案特别有用。

    在传递当前消息之后，将立即迁移客户端连接，这意味着下一条消息将路由到新服务器。

4. **停止服务器连接**

    关闭/迁移所有客户端连接后，或者默认情况下)  (30 秒时，

    SignalR Server SDK 将继续执行此阶段的关闭过程，并关闭所有服务器连接。

    如果无法关闭/迁移客户端连接，仍然会将其删除。 例如，没有合适的目标服务器/当前客户端到服务器消息未完成。

## <a name="sample-codes"></a>示例代码。

在以下情况下添加下列选项 `AddAzureSignalR` ：

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

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>配置 `OnConnected` 并将 `OnDisconnected` 正常关机模式设置为 `MigrateClients` 。

我们引入了一个 "IConnectionMigrationFeature"，用于指示是否正在迁移连接。

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