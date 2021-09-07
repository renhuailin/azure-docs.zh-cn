---
ms.openlocfilehash: 91ed308ecbeeb839dfddb78c0151e2c8aafd0d3b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123123605"
---
本快速入门介绍如何使用适用于 Windows 的 Azure 通信服务通话 SDK 开始 1:1 视频通话。

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

右键单击你的项目，转到 `Manage Nuget Packages` 来安装 `[Azure.Communication.Calling](https://www.nuget.org/packages/Azure.Communication.Calling)`。 请确保选中“包括预发行版”，并且程序包是来自 https://www.nuget.org/api/v2/ 。 

### <a name="request-access"></a>请求访问权限

转到 `Package.appxmanifest` 并单击 `Capabilities`。
勾选 `Internet (Client & Server)`，获取 Internet 的入站和出站访问权限。 勾选 `Microphone` 以访问麦克风的音频源。 勾选 `WebCam` 以访问设备的摄像头。 

通过右键单击并选择“查看代码”，将以下代码添加到 `Package.appxmanifest`。 
```XML
<Extensions>
<Extension Category="windows.activatableClass.inProcessServer">
<InProcessServer>
<Path>RtmMvrUap.dll</Path>
<ActivatableClass ActivatableClassId="VideoN.VideoSchemeHandler" ThreadingModel="both" />
</InProcessServer>
</Extension>
</Extensions>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

我们需要配置基本布局来附加逻辑。 为了发起出站呼叫，需要通过 `TextBox` 提供被呼叫方的用户 ID。 还需要一个 `Start Call` 按钮和一个 `Hang Up` 按钮。 我们还需要预览本地视频，并呈现其他参与者的远程视频。 因此，我们需要两个元素来显示视频流。

打开项目的 `MainPage.xaml`，并将内容替换为以下实现。 

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
        <StackPanel>
            <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
            <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
            <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
        </StackPanel>
        <StackPanel Orientation="Vertical" HorizontalAlignment="Center">
            <MediaElement x:Name="RemoteVideo" AutoPlay="True" Stretch="UniformToFill"/>
            <MediaElement x:Name="LocalVideo" AutoPlay="True"  Stretch="UniformToFill" HorizontalAlignment="Right"  VerticalAlignment="Bottom"/>
        </StackPanel>
    </StackPanel>
</Page>
```

打开 `App.xaml.cs`（右键单击并选择“查看代码”），并将此行添加到顶部：
```C#
using CallingQuickstart;
```

打开 `MainPage.xaml.cs`（右键单击并选择“查看代码”），并将内容替换为以下实现： 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.WinRT.Communication;
using Azure.Communication.Calling;
using System.Diagnostics;
using System.Threading.Tasks;
using System.Collections.Generic;

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
            this.InitCallAgentAndDeviceManager();
        }
        
        private async void InitCallAgentAndDeviceManager()
        {
            // Initialize call agent and Device Manager
        }

        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Authenticate the client and start call
        }
        
        private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
        {
            // Accept an incoming call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient callClient;
        CallAgent callAgent;
        Call call;
        DeviceManager deviceManager;
        LocalVideoStream[] localVideoStream;
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

若要初始化 `CallAgent`，需要有用户访问令牌。 通常，此令牌从具有特定于应用程序的身份验证的服务生成。 有关用户访问令牌的详细信息，请查看[用户访问令牌](../../../access-tokens.md)指南。 

对于快速入门，请使用为你的 Azure 通信服务资源生成的用户访问令牌替换 `<USER_ACCESS_TOKEN>`。

有了令牌后，使用该令牌来初始化 `CallAgent` 实例，以便发出和接收呼叫。 为了访问设备上的摄像头，我们还需要获取设备管理器实例。 

```C#
private async void InitCallAgentAndDeviceManager()
{
    CallClient callClient = new CallClient();
    deviceManager = await callClient.GetDeviceManager();

    CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
    
    CallAgentOptions callAgentOptions = new CallAgentOptions()
    {
        DisplayName = "<DISPLAY_NAME>"
    };
    callAgent = await callClient.CreateCallAgent(token_credential, callAgentOptions);
    callAgent.OnCallsUpdated += Agent_OnCallsUpdated;
    callAgent.OnIncomingCall += Agent_OnIncomingCall;
}
```

## <a name="start-a-call-with-video"></a>开启视频通话

开启视频通话。 我们需要使用设备管理器实例枚举摄像头并构造 `LocalVideoStream`。 我们需要使用 `LocalVideoStream` 设置 `VideoOptions`，并随 `startCallOptions` 一起传递以设置通话的初始选项。 通过将 `LocalVideoStream` 附加到 `MediaElement`，可以预览本地视频。 

```C#
private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }

    StartCallOptions startCallOptions = new StartCallOptions();
    startCallOptions.VideoOptions = new VideoOptions(localVideoStream);
    ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
    {
        new CommunicationUserIdentifier(CalleeTextBox.Text)
    };

    call = await callAgent.StartCallAsync(callees, startCallOptions);
}
```

## <a name="accept-an-incoming-call"></a>接听来电

若要应答传入的视频通话，请将 `LocalVideoStream` 传递至 `acceptCallOptions`。 

```C#
private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    acceptCallOptions.VideoOptions = new VideoOptions(localVideoStream);

    call = await incomingcall.AcceptAsync(acceptCallOptions);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>远程参与者和远程视频流

通过呼叫实例上的 `RemoteParticipants` 集合提供所有远程参与者。 建立通话后，我们可以访问通话的远程参与者并处理远程视频流。 

```C#
private async void Agent_OnCallsUpdated(object sender, CallsUpdatedEventArgs args)
{
    foreach (var call in args.AddedCalls)
    {
        foreach (var remoteParticipant in call.RemoteParticipants)
        {
            await AddVideoStreams(remoteParticipant.VideoStreams);
            remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
        }
        call.OnRemoteParticipantsUpdated += Call_OnRemoteParticipantsUpdated;
        call.OnStateChanged += Call_OnStateChanged;
    }
}

private async void Call_OnRemoteParticipantsUpdated(object sender, ParticipantsUpdatedEventArgs args)
{
    foreach (var remoteParticipant in args.AddedParticipants)
    {
        await AddVideoStreams(remoteParticipant.VideoStreams);
        remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
    }
}
```

### <a name="render-remote-videos"></a>呈现远程视频

对于每个远程视频流，请将其附加到 `MediaElement`。 

```C#
private async Task AddVideoStreams(IReadOnlyList<RemoteVideoStream> streams)
{

    foreach (var remoteVideoStream in streams)
    {
        var remoteUri = await remoteVideoStream.CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            RemoteVideo.Source = remoteUri;
            RemoteVideo.Play();
        });
        remoteVideoStream.Start();
    }
}
```

## <a name="call-state-update"></a>通话状态更新
通话断开连接后，我们需要清理视频呈现器。 

```C#
private async void Call_OnStateChanged(object sender, PropertyChangedEventArgs args)
{
    switch (((Call)sender).State)
    {
        case CallState.Disconnected:
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                LocalVideo.Source = null;
                RemoteVideo = null;
            });
            break;
        default:
            Debug.WriteLine(((Call)sender).State);
            break;
    }
}
```

## <a name="end-a-call"></a>结束呼叫

在单击 `Hang Up` 按钮时结束当前通话。 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    var hangUpOptions = new HangUpOptions();
    await call.HangUpAsync(hangUpOptions);
}
```

## <a name="run-the-code"></a>运行代码

可在 Visual Studio 中生成并运行代码。 请注意，对于解决方案平台，我们支持 `ARM64`、`x64` 和 `x86`。 

通过在文本字段中提供用户 ID 并单击 `Start Call` 按钮，可以启动出站视频呼叫。 

注意：呼叫 `8:echo123` 将停止视频流，因为回显机器人不支持视频流。 

有关用户 ID（标识）的详细信息，请查看[用户访问令牌](../../../access-tokens.md)指南。 
