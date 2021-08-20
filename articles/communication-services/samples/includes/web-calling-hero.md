---
title: 包含文件
description: include 文件
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: a6fc7a8419c3c4b6173771c898a1a4331d3c5874
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201320"
---
Azure 通信服务的群组通话主图示例演示了如何使用通信服务通话 Web SDK 构建群组通话体验。

在本地计算机上运行该示例之前，我们将通过本示例快速入门了解其工作原理。 然后，使用你自己的 Azure 通信服务资源将此示例部署到 Azure。

## <a name="download-code"></a>下载代码

在 [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero) 上查找此示例项目。 示例的一个版本可在单独的[分支](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview)上找到，其中包含当前处于公共预览版状态的功能，例如 [Teams 互操作](../../concepts/teams-interop.md)和[通话记录](../../concepts/voice-video-calling/call-recording.md)功能。

## <a name="overview"></a>概述

该示例同时包含了客户端应用程序和服务器端应用程序。 客户端应用程序是使用 Microsoft Fluent UI 框架的 React/Redux Web 应用程序。 此应用程序会将请求发送到 ASP.NET Core 服务器端应用程序，该服务器端应用程序会帮助客户端应用程序连接到 Azure。

该示例如下所示：

:::image type="content" source="../media/calling/landing-page.png" alt-text="显示示例应用程序登陆页面的屏幕截图。":::

当你按下“开始呼叫”按钮时，Web 应用程序从服务器端应用程序获取用户访问令牌。 然后使用该令牌将客户端应用连接到 Azure 通信服务。 检索到令牌后，系统会提示你指定要使用的摄像头和麦克风。 你可以使用切换控件来禁用/启用设备：

:::image type="content" source="../media/calling/pre-call.png" alt-text="显示示例应用程序呼叫前屏幕的屏幕截图。":::

配置显示名称和设备后，即可加入呼叫会话。 然后，你将看到核心通话体验所在的主要通话画布。

:::image type="content" source="../media/calling/main-app.png" alt-text="显示示例应用程序主屏幕的屏幕截图。":::

主要呼叫屏幕的各个组件：

- **媒体库**：显示参与者的主要阶段。 如果参与者启用了摄像头，则会在此处显示其视频源。 每个参与者都有一个单独的磁贴，上面显示了该参与者的显示名称和视频流（如果有）
- **标头**：这是主要呼叫控件所在的位置，这些控件可用来切换设置和参与者侧边栏、打开/关闭视频和混音、共享屏幕以及退出呼叫。
- **侧边栏**：使用标题上的控件进行切换时，会在此处显示参与者和设置信息。 可以使用右上角的“X”关闭该组件。 “参与者”侧边栏将显示参与者列表和邀请更多用户聊天的链接。 “设置”侧边栏可用于配置麦克风和摄像头设置。

> [!NOTE]
> 基于对 Web 通话 SDK 的限制，系统只呈现一个远程视频流。 有关更多详细信息，请参阅[通话 SDK 流支持](../../concepts/voice-video-calling/calling-sdk-features.md#calling-sdk-streaming-support)。

下面你将找到有关设置该示例的先决条件和步骤的详细信息。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js（12.18.4 及更高版本）](https://nodejs.org/en/download/)
- [Visual Studio（2019 及更高版本）](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)（请确保安装与 Visual Studio 实例相对应的版本：32 位或 64 位）
- 一个 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../quickstarts/create-communication-resource.md)。 需要为此快速入门记录资源连接字符串。

## <a name="locally-deploy-the-service--client-applications"></a>在本地部署服务和客户端应用程序

群组呼叫示例实质上是两个应用程序：ClientApp 和 Service.NET 应用。

若要在本地进行部署，需同时启动这两个应用程序。 从浏览器访问服务器应用时，它将使用本地部署的 ClientApp 来提供用户体验。

你可以在本地测试该示例，方法是使用呼叫 URL 打开多个浏览器会话来模拟多用户呼叫。

### <a name="before-running-the-sample-for-the-first-time"></a>首次运行示例之前

1. 打开 PowerShell、Windows 终端、命令提示符或等效项的实例，然后导航到要将示例克隆到的目录。
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. 从 Azure 门户获取 `Connection String`。 有关连接字符串的详细信息，请参阅[创建 Azure 通信服务资源](../../quickstarts/create-communication-resource.md)。
4. 获取 `Connection String` 后，将连接字符串添加到 Service .NET 文件夹下的 Calling/appsetting.json 文件中。 在变量 `ResourceConnectionString` 中输入连接字符串。

### <a name="local-run"></a>本地运行

1. 转到“Calling”文件夹，在 Visual Studio 中打开 `Calling.csproj` 解决方案。
2. 运行 `Calling` 项目。 系统会在 `localhost:5001` 处打开浏览器。

## <a name="publish-the-sample-to-azure"></a>将示例发布到 Azure

1. 右键单击 `Calling` 项目并选择“发布”。
2. 创建新的发布配置文件，并选择你的 Azure 订阅。
3. 发布之前，请使用 `Edit App Service Settings` 添加连接字符串，将 `ResourceConnectionString` 作为键填写，并提供连接字符串（从 appsettings.json 复制）作为值。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../../quickstarts/create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[从 GitHub 下载示例](https://github.com/Azure-Samples/communication-services-web-calling-hero)

有关详细信息，请参阅以下文章：

- 自行熟悉如何[使用通话 SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- 详细了解[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>其他阅读材料

- [示例](./../overview.md) - 在示例概述页上查找更多示例。
- [Redux](https://redux.js.org/) - 客户端状态管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft 支持的 UI 库
- [React](https://reactjs.org/) - 用于构建用户界面的库
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - 用于构建 Web 应用程序的框架
