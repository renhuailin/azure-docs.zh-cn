---
title: 教程：构建 Blazor Server 聊天应用 - Azure SignalR
description: 本教程将介绍如何使用 Azure SignalR 服务构建并修改 Blazor Server 应用。
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 5ed5107cadcfbf247b79c18a6a2e391ab3043565
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331819"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>教程：构建 Blazor Server 聊天应用

本教程演示如何构建和修改 Blazor Server 应用。 将了解如何执行以下操作：
> [!div class="checklist"] 
> * 使用 Blazor Server 应用模板构建简单的聊天室。
> * 使用 Razor 组件。
> * 使用 Razor 组件中的事件处理和数据绑定。
> * 使用 Visual Studio 快速部署到 Azure 应用服务。
> * 从本地 SignalR 迁移到 Azure SignalR 服务。

## <a name="prerequisites"></a>必备条件

* 安装 [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)（版本 >= 3.0.100）
* 安装 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)（版本 >= 16.3）


[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>在 Blazor Server 应用中构建本地聊天室

从 Visual Studio 2019 版本 16.2.0 开始，Azure SignalR 服务内置于 Web 应用程序的发布过程中，以更加方便地管理 Web 应用和 SignalR 服务之间的依赖项。 你无需更改任何代码，即可在开发本地环境中使用本地 SignalR 实例，同时针对 Azure 应用服务使用 Azure SignalR 服务。

1. 创建 Blazor 聊天应用：
   1. 在 Visual Studio 中，选择“创建新项目”。 
   1. 选择“Blazor 应用”  。 
   1. 命名应用程序并选择一个文件夹。 
   1. 选择“Blazor Server 应用”模板  。
    
       > [!NOTE]
       > 确保已安装 .NET Core SDK 3.0+，使 Visual Studio 可正确识别目标框架。
   
       [ ![在“创建新项目”中，选择 Blazor 应用模板。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   5. 还可通过在 .NET CLI 中运行 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令来创建项目：
   
       ```dotnetcli
       dotnet new blazorserver -o BlazorChat
       ```
   
1. 添加一个名为 `BlazorChatSampleHub.cs` 的新 C# 文件，并创建一个派生于聊天应用的 `Hub` 类的新类 `BlazorSampleHub`。 有关创建中心的详细信息，请参阅[创建并使用中心](/aspnet/core/signalr/hubs#create-and-use-hubs)。 
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. 在 `Startup.Configure()` 方法中为中心添加一个终结点。
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. 安装 `Microsoft.AspNetCore.SignalR.Client` 包以使用 SignalR 客户端。

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. 在 `Pages` 文件夹下创建一个名为 `ChatRoom.razor` 的新 [Razor 组件](/aspnet/core/blazor/components/)，以实现 SignalR 客户端。 请遵循以下步骤或使用 [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor) 文件。

   1. 添加 [`@page`](/aspnet/core/mvc/views/razor#page) 指令和 using 语句。 使用 [`@inject`](/aspnet/core/mvc/views/razor#inject) 指令插入 [`NavigationManager`](/aspnet/core/blazor/fundamentals/routing#uri-and-navigation-state-helpers) 服务。
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. 在 `@code` 部分中，将以下成员添加到新的 SignalR 客户端，以发送和接收消息。
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. 在 `@code` 部分前添加 UI 标记，以与 SignalR 客户端进行交互。
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. 更新 `NavMenu.razor` 组件以插入新的 `NavLink` 组件，链接到 `NavMenuCssClass` 下的聊天室。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. 向 `site.css` 文件添加一些 CSS 类，对聊天页中的 UI 元素设置样式。

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. 按 <kbd>F5</kbd> 运行该应用。 现在可以开始聊天：

   [ ![显示了 Bob 和 Alice 之间的动态聊天。Alice 说“你好”，Bob 回复“你好”。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>发布到 Azure

将 Blazor 应用部署到 Azure 应用服务时，建议使用 [Azure SignalR 服务](/aspnet/core/signalr/scale#azure-signalr-service)。 Azure SingalR 服务允许将 Blazor Server 应用扩展到大量并发 SignalR 连接。 此外，SignalR 服务的全球覆盖和高性能数据中心可帮助显著减少由于地理位置造成的延迟。

> [!IMPORTANT]
> 在 Blazor Server 应用中，用户界面状态在服务器端进行维护，这意味着需要一个粘性服务器会话来保持状态。 如果存在单个应用服务器，就可通过设计确保粘性会话。 但如果有多个应用服务器，则客户端协商和连接可能会转到不同的服务器，这可能会导致 Blazor 应用中出现不一致的 UI 状态管理。 因此建议启用粘性服务器会话，如以下 appsettings.json 所示：
>
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. 右键单击项目，转到“发布”。 使用以下设置：
   * **目标**：Azure
   * **特定目标**：支持所有类型的 Azure 应用服务。
   * **应用服务**：创建或选择应用服务实例。

   [ ![动画显示选择 Azure 作为目标，然后选择 Azure 应用服务作为特定目标。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. 添加 Azure SignalR 服务依赖项。

   创建发布配置文件后，可以在“服务依赖项”下看到添加 Azure SignalR 服务的建议消息。 选择“配置”以在面板中新建或选择现有的 Azure SignalR 服务。

   [ ![在“发布”中，突出显示了“配置”链接。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   服务依赖项将执行以下活动，使应用能够在 Azure 上时自动切换到 Azure SignalR 服务：

   * 更新 [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration) 以使用 Azure SignalR 服务。
   * 添加 Azure SignalR 服务 NuGet 包引用。
   * 更新配置文件属性以保存依赖项设置。
   * 根据你的选择配置机密存储。
   * 在 appsettings.json 中添加配置，使应用面向 Azure SignalR 服务。

   [ ![在“更改摘要”上，复选框用于选中所有依赖项。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. 发布应用。

   现在即可发布此应用。 完成发布过程后，应用会自动在浏览器中启动。
 
   > [!NOTE]
   > 由于 Azure 应用服务的启动延迟，应用可能需要一些时间才能启动。 可使用浏览器调试工具（通常是按 <kbd>F12</kbd>）来确保流量已重定向到 Azure SignalR 服务。

   [ ![Blazor SignalR 聊天示例包含一个用于填写姓名的文本框和一个用于开始聊天的 Chat! 按钮。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="enable-azure-signalr-service-for-local-development"></a>启用 Azure SignalR 服务进行本地开发

1. 使用以下命令添加对 Azure SignalR SDK 的引用。

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. 在 `Startup.ConfigureServices()` 中添加对 `AddAzureSingalR()` 的调用，如下所示。

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. 在 appsettings.json 中或使用[机密管理器](/aspnet/core/security/app-secrets?tabs=visual-studio#secret-manager)工具配置 Azure SignalR 服务连接字符串。

> [!NOTE]
> 第 2 步可以替换为将[托管启动程序集](/aspnet/core/fundamentals/host/platform-specific-configuration)配置为使用 SignalR SDK。
>
> 1. 添加配置以启用 appsettings.json 中的 Azure SignalR 服务：
>
>     ```json
>     "Azure": {
>       "SignalR": {
>         "Enabled": true,
>         "ConnectionString": <your-connection-string>       
>       }
>     }
>    
>    ```
>
> 1. 将托管启动程序集配置为使用 Azure SignalR SDK。 编辑 launchSettings.json，并在 `environmentVariables` 中添加以下示例：
>
>     ```json
>    "environmentVariables": {
>         ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>  
>     ```
>

[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，请使用 Azure 门户删除相应的资源组。

## <a name="additional-resources"></a>其他资源

* [ASP.NET Core Blazor](/aspnet/core/blazor)

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 使用 Blazor Server 应用模板构建简单的聊天室。
> * 使用 Razor 组件。
> * 使用 Razor 组件中的事件处理和数据绑定。
> * 使用 Visual Studio 快速部署到 Azure 应用服务。
> * 从本地 SignalR 迁移到 Azure SignalR 服务。

深入了解高可用性：
> [!div class="nextstepaction"]
> [复原能力和灾难恢复](signalr-concept-disaster-recovery.md)
