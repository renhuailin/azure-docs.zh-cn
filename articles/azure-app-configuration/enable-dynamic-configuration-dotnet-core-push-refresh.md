---
title: Tutorial:在 .NET Core 应用中通过推送刷新使用动态配置
titleSuffix: Azure App Configuration
description: 本教程介绍如何通过推送刷新动态更新 .NET Core 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: jken
ms.openlocfilehash: 4ad0f2c20c0d704b2c101f975d1b62fc7dc71aca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750352"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Tutorial:在 .NET Core 应用中通过推送刷新使用动态配置

应用配置 .NET Core 客户端库支持按需更新配置，不需让应用程序重启。 应用程序可以配置为使用以下两种方法中的一种或两种来检测应用配置更改。

1. 轮询模型：这是使用轮询检测配置更改的默认行为。 设置的缓存值到期后，对 `TryRefreshAsync` 或 `RefreshAsync` 的下一次调用将向服务器发送请求，以检查配置是否已更改，并根据需要拉取已更新的配置。

1. 推送模型：这将使用[应用配置事件](./concept-app-configuration-event.md)检测配置更改。 应用配置设置为向 Azure 事件网格发送键值更改事件后，应用程序就可以使用这些事件来优化保持配置更新所需的请求总数。 应用程序可以选择直接从事件网格订阅这些，也可以选择通过某一[受支持的事件处理程序](../event-grid/event-handlers.md)（如 Webhook、Azure 函数或服务总线主题）订阅这些。

应用程序可以选择直接从事件网格或通过 Webhook 或通过将事件转发到 Azure 服务总线来订阅这些事件。 Azure 服务总线 SDK 提供了一个 API 来注册消息处理程序，从而为没有 HTTP 终结点或不希望持续轮询事件网格以获取更改的应用程序简化了这个过程。

本教程演示如何在代码中使用推送刷新实现动态配置更新。 它建立在快速入门中介绍的应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 .NET Core 应用](./quickstart-dotnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置订阅以将配置更改事件从“应用配置”发送到“服务总线”主题
> * 设置 .NET Core 应用，使其能够更新其配置以响应应用配置更改。
> * 在应用程序中使用最新配置。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>设置 Azure 服务总线主题和订阅

本教程使用事件网格的服务总线集成来简化不希望持续轮询应用配置更改的应用程序的配置更改检测。 Azure 服务总线 SDK 提供了一个用于注册消息处理程序的 API，该消息处理程序可用于在应用配置中检测到更改时更新配置。 遵循[快速入门：使用 Azure 门户创建服务总线主题和订阅](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)中的步骤，创建服务总线命名空间主题和订阅。

创建资源后，添加以下环境变量。 这些环境变量将用于注册事件处理程序，以便获取应用程序代码中的配置更改。

| 密钥 | 值 |
|---|---|
| ServiceBusConnectionString | 服务总线命名空间的连接字符串 |
| ServiceBusTopic | 服务总线主题的名称 |
| ServiceBusSubscription | 服务总线订阅的名称 |

## <a name="set-up-event-subscription"></a>设置事件订阅

1. 在 Azure 门户中打开“应用配置”资源，然后在 `Events` 窗格中单击 `+ Event Subscription`。

    ![应用配置事件](./media/events-pane.png)

1. 输入 `Event Subscription` 和 `System Topic` 的名称。

    ![创建事件订阅](./media/create-event-subscription.png)

1. 选择 `Endpoint Type` 作为 `Service Bus Topic`，选择“服务总线”主题，然后单击 `Confirm Selection`。

    ![事件订阅服务总线终结点](./media/event-subscription-servicebus-endpoint.png)

1. 单击 `Create` 以添加事件订阅。

1. 单击 `Events` 窗格中的 `Event Subscriptions` 以验证订阅是否已成功创建。

    ![应用配置事件订阅](./media/event-subscription-view.png)

> [!NOTE]
> 订阅配置更改时，可以使用一个或多个筛选器来减少发送到应用程序的事件数。 这些筛选器可以配置为[事件网格订阅筛选器](../event-grid/event-filtering.md)或[服务总线订阅筛选器](../service-bus-messaging/topic-filters.md)。 例如，订阅筛选器可用于仅订阅以特定字符串开头的键的更改的事件。

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>注册事件处理程序以从应用配置重新加载数据

打开 Program.cs，使用以下代码更新该文件。

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

[SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) 方法用于将为刷新而注册的键值的缓存值设置为“脏”。 这样可以确保对 `RefreshAsync` 或 `TryRefreshAsync` 的下一次调用会使用“应用配置”重新验证缓存的值，并在需要时对其进行更新。

在将缓存值标记为“脏”之前会添加一个随机延迟，以减少在多个实例同时刷新时可能存在的限制。 缓存值被标记为“脏”之前的默认最大延迟是 30 秒，但可以通过向 `SetDirty` 方法传递可选的 `TimeSpan` 参数来重写该值。

> [!NOTE]
> 若要减少在使用推送刷新时对应用配置的请求数，请务必使用适当的 `cacheExpiration` 参数值调用 `SetCacheExpiration(TimeSpan cacheExpiration)`。 这可控制拉取刷新的缓存过期时间，并可在事件订阅或服务总线订阅出现问题时用作安全保证。 建议值为 `TimeSpan.FromDays(30)`。

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 设置名为“AppConfigurationConnectionString”的环境变量，并将其设置为应用配置存储的访问密钥。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果使用 macOS 或 Linux，则请运行以下命令：

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 运行以下命令以生成控制台应用：

    ```console
     dotnet build
    ```

1. 生成成功完成后，请运行以下命令以在本地运行应用：

    ```console
     dotnet run
    ```

    ![在执行更新之前的推送刷新运行](./media/dotnet-core-app-pushrefresh-initial.png)

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

1. 选择“配置资源管理器”  并更新以下键的值：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 - 已更新 |

1. 等待 30 秒钟，以使事件可被处理且配置可被更新。

    ![在执行更新之后的推送刷新运行](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用 .NET Core 应用，以通过应用程序配置动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
