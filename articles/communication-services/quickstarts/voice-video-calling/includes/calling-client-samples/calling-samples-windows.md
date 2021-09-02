---
title: 快速入门 - 使用 Azure 通信服务向 Windows 应用添加通话功能
description: 本快速入门介绍如何使用适用于 Windows 的 Azure 通信服务通话 SDK。
author: tophpalmer
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5959cb809e9537c5de9ed2dce771a234f742dc08
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078185"
---
本快速入门介绍如何使用适用于 Windows 的 Azure 通信服务通话 SDK 开始通话。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../../access-tokens.md)
- 安装任意版本的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 和 `Universal Windows Platform development` 工作负载以及Windows SDK `10.0.17134` 或更高版本。 此外，还应安装 `NuGet package manager` 和 `NuGet targets and build tasks` 组件。
- 可选：完成[开始向应用程序添加通话功能](../../getting-started-with-calling.md)快速入门

## <a name="setting-up"></a>设置

### <a name="creating-the-visual-studio-project"></a>创建 Visual Studio 项目

在 Visual Studio 2019 中创建一个新的 `Blank App (Universal Windows)` 项目。 输入项目名称后，可随意选取版本高于 `10.0.17134` 的 Windows SDK。 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>使用 NuGet 包管理器安装包和依赖项

可以通过 NuGet 包公开提供通话 SDK API 和库。
以下步骤将举例说明如何查找、下载和安装通话 SDK NuGet 包。

1. 打开 NuGet 包管理器 (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)
2. 单击 `Browse`，然后在搜索框中键入 `Azure.Communication.Calling`。
3. 确保选中 `Include prerelease` 复选框。
4. 单击 `Azure.Communication.Calling` 包。
5. 选中与右侧选项卡上的 CS 项目对应的复选框。
6. 单击“”`Install`按钮。


### <a name="request-access-to-the-microphone"></a>请求访问麦克风

应用需要访问麦克风才能正常运行。 在 UWP 应用中，应在应用清单文件中声明麦克风功能。 以下步骤举例说明了如何实现此操作。

1. 在 `Solution Explorer` 面板中，双击扩展名为 `.appxmanifest` 的文件。
2. 单击 `Capabilities` 选项卡。
3. 从功能列表中选中 `Microphone` 复选框。


### <a name="create-ui-buttons-to-place-and-hang-up-the-call"></a>创建用于发起和挂起通话的 UI 按钮

这个简单的示例应用将包含两个按钮。 一个用于发起通话，另一个用于挂起已发起的通话。
以下步骤将举例说明如何将这些按钮添加到应用。

1. 在 `Solution Explorer` 面板中，双击名为 `MainPage.xaml` 的文件。
2. 在中央面板中，在 UI 预览下查找 XMAL 代码。
3. 将 `<Grid>` 替换为 `</Grid>` XAML 代码，摘录如下：
```xml
<StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
    <Button x:Name="callButton" Click="CallHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Call</Button>
    <Button x:Name="hangupButton" Click="HangupHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Hang up</Button>
</StackPanel>
```

### <a name="setting-up-the-app-with-calling-sdk-apis"></a>使用通话 SDK API 设置应用

通话 SDK API 在两个不同的命名空间中。
以下步骤告知 C# 编译器关于这些命名空间的信息，以便 Visual Studio Intellisense 帮助进行代码开发。

1. 在 `Solution Explorer` 面板中，单击名为 `MainPage.xaml` 的文件左侧的箭头。
2. 双击显示的名为 `MainPage.xaml.cs` 的文件。
3. 在当前 `using` 语句的底部添加以下命令。

```csharp
using Azure.Communication;
using Azure.Communication.Calling;
```

请使 `MainPage.xaml.cs` 保持打开状态。 接下来的步骤将添加更多代码。

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 UWP 的 Azure 通信服务通话客户端库的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient 是呼叫客户端库的主入口点。 |
| CallAgent | CallAgent 用于启动和加入通话。 |
| 调用 | Call 用于管理已发起或已加入的通话。 |
| CommunicationTokenCredential | CommunicationTokenCredential 用作实例化 CallAgent 的令牌凭据。|
| CallAgentOptions | CallAgentOptions 包含用于标识主叫方的信息。 |
| HangupOptions | HangupOptions 告知是否应终止其所有参与者的通话。 |

## <a name="allow-app-interactions"></a>允许应用交互

先前添加的 UI 按钮需基于发起的 `Call` 才能正常工作。 这意味着，`Call` 数据成员应被添加到 `MainPage` 类。
此外，若要使创建 `CallAgent` 的异步操作成功，还应将 `CallAgent` 数据成员添加到同一类。

请将以下数据成员添加到 `MainPage` 类：
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>创建按钮处理程序

之前向 XAML 代码添加了两个 UI 按钮。 以下代码添加了在用户单击按钮时要执行的处理程序。
应在上一部分的数据成员之后添加以下代码。

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="initialize-the-callagent"></a>初始化 CallAgent

若要从 `CallClient` 创建 `CallAgent` 实例，必须使用 `CallClient.CreateCallAgent` 方法，该方法在初始化后异步返回 `CallAgent` 对象。

若要创建 `CallAgent`，必须传递 `CommunicationTokenCredential` 对象和 `CallAgentOptions` 对象。 请记住，如果传递了格式错误的令牌，则会引发 `CommunicationTokenCredential`。

应在 `CallHandler` 中添加以下代码。

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential creds;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    creds = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
}
catch (Exception)
{
    throw new Exception("Invalid credential token");
}
```

必须将 `<USER ACCESS TOKEN>` 替换为资源的有效凭据令牌。 如果必须获取凭据令牌，请参阅[用户访问令牌](../../../../quickstarts/access-tokens.md)文档。

## <a name="create-callagent-and-place-a-call"></a>创建 CallAgent 并发起通话

创建 `CallAgent` 所需的对象现已准备就绪。 现在，可以异步创建 `CallAgent` 并发起通话。

处理完上一步骤中的异常后，应添加以下代码。

```csharp
client.CreateCallAgent(creds, callAgentOptions).Completed +=
async (IAsyncOperation<CallAgent> asyncInfo, AsyncStatus asyncStatus) =>
{
    agent_ = asyncInfo.GetResults();

    CommunicationUserIdentifier target = new CommunicationUserIdentifier("<CALLEE>");

    StartCallOptions startCallOptions = new StartCallOptions();
    call_ = await agent_.StartCallAsync(new List<ICommunicationIdentifier>() { target }, startCallOptions);
};
```

将 `<CALLEE>` 替换为租户中的其他任何标识。 或者，可以随时使用 `8:echo123` 与 ACS 回显机器人对话。

## <a name="end-a-call"></a>结束呼叫

发起通话后，应使用 `Call` 对象的 `Hangup` 方法挂起通话。

还应使用 `HangupOptions` 的实例来通知是否必须终止所有参与者的通话。

应在 `HangupHandler` 中添加以下代码。

```csharp
HangUpOptions hangupOptions = new HangUpOptions();
call_.HangUpAsync(hangupOptions).Completed +=
(IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{
};
```

## <a name="run-the-code"></a>运行代码

请确保 Visual Studio 生成适用于 `x64`、`x86` 或 `ARM64` 的应用，然后按 `F5` 开始运行应用。 然后，单击 `Call` 按钮，向定义的被呼叫者发起通话。

请注意，应用首次运行时，系统将提示用户授予对麦克风的访问权限。
