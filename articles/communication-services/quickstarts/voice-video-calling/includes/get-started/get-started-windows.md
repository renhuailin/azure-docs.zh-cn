---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: 59c39c7d71946ee93a0d72d5b71e1b96333d2c15
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970219"
---
本快速入门介绍如何使用适用于 Windows 的 Azure 通信服务通话 SDK 开始通话。

## <a name="sample-code"></a>代码示例

可以从 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/VoiceCalling) 下载示例应用。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要具备以下先决条件：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安装带有通用 Windows 平台开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- Azure 通信服务的[用户访问令牌](../../../access-tokens.md)。

## <a name="setting-up"></a>设置

### <a name="creating-the-project"></a>创建项目

在 Visual Studio 中，使用“空白应用（通用 Windows）”模板创建一个新项目，设置单页通用 Windows 平台 (UWP) 应用。

:::image type="content" source="../../media/windows/create-a-new-project.png" alt-text="显示 Visual Studio 中“新建项目”窗口的屏幕截图。":::

### <a name="install-the-package"></a>安装包

右键单击你的项目，转到 `Manage Nuget Packages` 来安装 `Azure.Communication.Calling`。 

### <a name="request-access"></a>请求访问权限

转到 `Package.appxmanifest` 并单击 `Capabilities`。
勾选 `Internet (Client & Server)`，获取 Internet 的入站和出站访问权限。 勾选 `Microphone`，以访问麦克风的音频源。 

:::image type="content" source="../../media/windows/request-access.png" alt-text="显示请求在 Visual Studio 中访问互联网和麦克风的屏幕截图。":::

### <a name="set-up-the-app-framework"></a>设置应用框架

我们需要配置基本布局来附加逻辑。 为了发起出站呼叫，需要通过 `TextBox` 提供被呼叫方的用户 ID。 还需要一个 `Start Call` 按钮和一个 `Hang Up` 按钮。 打开你的项目的 `MainPage.xaml`，然后将 `StackPanel` 节点添加到 `Page`： 

```C#
<Page
    x:Class="CallingQuickstart.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CallingQuickstart"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
        <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
        <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
    </StackPanel>
</Page>
```

打开 `MainPage.xaml.cs` ，并将内容替换为以下实现： 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.InitCallAgent();
        }
        
        private async void InitCallAgent()
        {
            // Create Call Client and initialize Call Agent
        }
        
        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Start call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient call_client_;
        CallAgent call_agent_;
        Call call_;
    }
}
```

## <a name="object-model"></a>对象模型

以下类和接口用于处理 Azure 通信服务通话 SDK 的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient 是通话 SDK 的主入口点。|
| CallAgent | CallAgent 用于启动和管理呼叫。 |
| CommunicationTokenCredential | CommunicationTokenCredential 用作实例化 CallAgent 的令牌凭据。| 
| CommunicationUserIdentifier | CommunicationUserIdentifier 用于表示用户的标识，可以是以下项之一：CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication。 |

## <a name="authenticate-the-client"></a>验证客户端

通过用户访问令牌初始化 `CallAgent` 实例，这使我们可以发出和接收呼叫。 

在以下代码中，将 `<USER_ACCESS_TOKEN>` 替换为用户访问令牌。 如果还没有可用的令牌，请参阅[用户访问令牌](../../../access-tokens.md)文档。

向 `InitCallAgent` 函数添加以下代码。 

```C#
CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
call_client_ = new CallClient();

CallAgentOptions callAgentOptions = new CallAgentOptions()
{
    DisplayName = "<YOUR_DISPLAY_NAME>"
};
call_agent_ = await call_client_.CreateCallAgent(token_credential, callAgentOptions);
```

## <a name="start-a-call"></a>开始呼叫

将实现添加到 `CallButton_ClickAsync`，以使用我们创建的 `call_agent` 开始呼叫。 

```C#
StartCallOptions startCallOptions = new StartCallOptions();
ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
{
    new CommunicationUserIdentifier(CalleeTextBox.Text)
};
call_ = await call_agent_.StartCallAsync(callees, startCallOptions);
```

## <a name="end-a-call"></a>结束呼叫

在单击 `Hang Up` 按钮时结束当前通话。 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    await call_.HangUpAsync(new HangUpOptions());
}
```

## <a name="run-the-code"></a>运行代码

可在 Visual Studio 中生成并运行代码。 请注意，对于解决方案平台，我们支持 `ARM64`、`x64` 和 `x86`。 

可以通过在“文本”字段中提供用户 ID 并单击 `Start Call` 按钮，启动出站呼叫。 呼叫 `8:echo123` 会将你连接到回显机器人，这对于入门和验证音频设备是否正常运行非常有用。

:::image type="content" source="../../media/windows/run-the-app.png" alt-text="显示正在运行快速入门应用程序的屏幕截图":::
