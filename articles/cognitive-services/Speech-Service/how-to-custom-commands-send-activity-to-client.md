---
title: 将自定义命令活动发送到客户端应用程序
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将自定义命令应用程序的活动发送到运行语音 SDK 的客户端应用程序。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "94566089"
---
# <a name="send-custom-commands-activity-to-client-application"></a>将自定义命令活动发送到客户端应用程序

本文介绍如何将自定义命令应用程序的活动发送到运行语音 SDK 的客户端应用程序。

你将完成以下任务：

- 在自定义命令应用程序中定义并发送一个自定义 JSON 有效负载
- 从 C# UWP 语音 SDK 客户端应用程序接收并可视化自定义 JSON 有效负载的内容

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 或更高版本。 本指南使用 Visual Studio 2019
> * 语音服务的 Azure 订阅密钥：[免费获取一个](overview.md#try-the-speech-service-for-free)或在 [Azure 门户](https://portal.azure.com)上创建它
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)
> * 已启用语音 SDK 的客户端应用：[如何：使用语音 SDK 来与客户端应用程序集成](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>设置“将活动发送到客户端” 
1. 打开先前创建的自定义命令应用程序
1. 选择“TurnOnOff”命令，选择完成规则下的“ConfirmationResponse”，然后选择“添加操作”。  
1. 在“新建操作 - 类型”下，选择“将活动发送到客户端” 
1. 将以下 JSON 复制到“活动内容”
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
1. 单击“保存”创建包含“发送活动”操作的新规则，然后 **训练** 并 **发布** 更改

   > [!div class="mx-imgBorder"]
   > ![“发送活动”完成规则](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>与客户端应用程序集成

在[如何：使用语音 SDK 设置客户端应用程序（预览）](./how-to-custom-commands-setup-speech-sdk.md)中，你已创建一个包含语音 SDK 的 UWP 客户端应用程序，该应用程序可处理 `turn on the tv`、`turn off the fan` 等命令。 添加一些视觉对象后，可以看到这些命令的结果。

若要添加带标签的框并在其中包含指示“打开”或“关闭”的文本，请将以下 StackPanel XML 块添加到 `MainPage.xaml`。 

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

由于你创建了 JSON 有效负载，因此需要添加对 [JSON.NET](https://www.newtonsoft.com/json) 库的引用来处理反序列化。

1. 为解决方案指定正确的客户端。
1. 选择“管理解决方案的 NuGet 包”，然后选择“浏览”  
1. 如果已安装 **Newtonsoft.json**，请确保其版本至少为 12.0.3。 如果低于此版本，请转到“管理解决方案的 NuGet 包 - 更新”，搜索 **Newtonsoft.json** 以更新该包。 本指南使用版本 12.0.3。

    > [!div class="mx-imgBorder"]
    > ![“发送活动”有效负载](media/custom-commands/send-activity-to-client-json-nuget.png)

1. 此外，请确保 NuGet 包 **Microsoft.NETCore.UniversalWindowsPlatform** 的版本至少为 6.2.10。 本指南使用版本 6.2.10。

在“MainPage.xaml.cs”中，添加

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>处理收到的有效负载

在 `InitializeDialogServiceConnector` 中，将 `ActivityReceived` 事件处理程序替换为以下代码。 修改后的 `ActivityReceived` 事件处理程序将从活动中提取有效负载，并分别更改电视或风扇的可视状态。

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
1. 电视的可视状态应更改为“打开”
   > [!div class="mx-imgBorder"]
   > ![显示电视可视状态现在为“打开”的屏幕截图。](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：设置 Web 终结点（预览）](./how-to-custom-commands-setup-web-endpoints.md)
