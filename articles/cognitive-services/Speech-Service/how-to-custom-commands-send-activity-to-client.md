---
title: 向客户端应用程序发送自定义命令活动
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将活动从自定义命令应用程序发送到运行语音 SDK 的客户端应用程序。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566089"
---
# <a name="send-custom-commands-activity-to-client-application"></a>向客户端应用程序发送自定义命令活动

本文介绍如何将活动从自定义命令应用程序发送到运行语音 SDK 的客户端应用程序。

你将完成以下任务：

- 从自定义命令应用程序定义并发送自定义 JSON 有效负载
- 从 C# UWP 语音 SDK 客户端应用程序接收并可视化自定义 JSON 有效负载内容

## <a name="prerequisites"></a>必备条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 或更高版本。 本指南使用 Visual Studio 2019
> * 语音服务的 Azure 订阅密钥：[免费获取一个](overview.md#try-the-speech-service-for-free)或在 [Azure 门户](https://portal.azure.com)上创建它
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)
> * 启用了语音 SDK 的客户端应用：[如何：与使用语音 SDK 的客户端应用程序集成](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>设置“向客户端发送活动” 
1. 打开此前创建的自定义命令应用程序
1. 依次选择“TurnOnOff”命令、完成规则下的“ConfirmationResponse”、“添加操作”  
1. 在“新建操作类型”下，选择“向客户端发送活动” 
1. 将下面的 JSON 复制到“活动内容”
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. 单击“保存”，以创建具有“发送活动”操作的新规则，**训练** 并 **发布** 更改

   > [!div class="mx-imgBorder"]
   > ![“发送活动”完成规则](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>与客户端应用程序集成

在[如何：设置使用语音 SDK 的客户端应用程序（预览版）](./how-to-custom-commands-setup-speech-sdk.md)中，你创建了一个使用语音 SDK 的 UWP 客户端应用程序，该应用程序处理了 `turn on the tv`、`turn off the fan` 等命令。 在添加了一些视觉对象后，可以看到这些命令的结果。

若要添加带有指示“on”或“off”的标签的框，请将 StackPanel 的以下 XML 块添加到 `MainPage.xaml` 。

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>添加引用库

由于已经创建了 JSON 有效负载，因此需要添加对 [JSON.NET](https://www.newtonsoft.com/json) 库的引用来处理反序列化。

1. 右键单击解决方案。
1. 选择“管理解决方案的 NuGet 包”，然后选择“浏览”  
1. 如果已安装 Newtonsoft.json，请确保其版本最低为 12.0.3。 如果不是，请转到“管理解决方案的 NuGet 包 - 更新”，搜索“Newtonsoft.json”并对它进行更新 。 本指南使用版本 12.0.3。

    > [!div class="mx-imgBorder"]
    > ![“发送活动”有效负载](media/custom-commands/send-activity-to-client-json-nuget.png)

1. 此外，请确保 NuGet 包 Microsoft.NETCore.UniversalWindowsPlatform 最低为 6.2.10。 本指南使用版本 6.2.10。

在“MainPage.xaml.cs”中，添加

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>处理收到的有效负载

在 `InitializeDialogServiceConnector` 中，将 `ActivityReceived` 事件处理程序替换为以下代码。 修改后的 `ActivityReceived` 事件处理程序会从活动中提取有效负载，并分别更改 tv 或 fan 的视觉状态。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>试试看

1. 启动应用程序
1. 选择“启用麦克风”
1. 选择“对话”按钮
1. 说出 `turn on the tv`
1. tv 的视觉状态应该会更改为“on”
   > [!div class="mx-imgBorder"]
   > ![屏幕截图，显示 TV 的视觉状态现在为“on”。](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：设置 Web 终结点（预览版）](./how-to-custom-commands-setup-web-endpoints.md)
