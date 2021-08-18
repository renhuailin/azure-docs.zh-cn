---
title: Windows 上的语音助理 - 在锁定时的实施指导原则
titleSuffix: Azure Cognitive Services
description: 有关为语音代理应用程序实施语音激活和锁定时功能的说明。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: df35b537e3bcaa2fb7d876b55cea08e5dcf65586
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113652441"
---
# <a name="implementing-voice-assistants-on-windows"></a>在 Windows 上实施语音助理

本指南逐步说明在 Windows 上创建语音助理的重要实施细节。

## <a name="implementing-voice-activation"></a>实施语音激活

[设置环境](how-to-windows-voice-assistants-get-started.md)并了解[语音激活的工作原理](windows-voice-assistants-overview.md#how-does-voice-activation-work)后，你可以开始对自己的语音助理应用程序实施语音激活。

### <a name="registration"></a>注册

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>确保麦克风可用且可访问，然后监视其状态

MVA 需要有一个麦克风存在且可访问，以便能够检测语音激活。 使用 [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability)、[DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher) 和 [MediaCapture](/uwp/api/windows.media.capture.mediacapture) 类分别检查是否可以私密访问麦克风、设备是否存在以及设备的状态（例如音量和静音）。

### <a name="register-the-application-with-the-background-service"></a>将应用程序注册到后台服务

要使 MVA 能够在后台启动应用程序，需将该应用程序注册到后台服务。 请参阅[此处](/windows/uwp/launch-resume/register-a-background-task)有关后台服务注册的完整指南。

### <a name="unlock-the-limited-access-feature"></a>解锁“受限访问功能”

使用 Microsoft 提供的“受限访问功能”密钥解锁语音助理功能。 请使用 Windows SDK 中的 [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) 类执行此操作。

### <a name="register-the-keyword-for-the-application"></a>注册应用程序的关键字

应用程序需要将自身、其关键字模型及其语言注册到 Windows。

首先检索关键字检测器。 在此示例代码中，我们检索第一个探测器，但你也可以从 `configurableDetectors` 中选择特定的检测器。

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

检索 ActivationSignalDetector 对象后，使用信号 ID、模型 ID 和显示名称调用此对象的 `ActivationSignalDetector.CreateConfigurationAsync` 方法，以注册关键字并检索应用程序的 `ActivationSignalDetectionConfiguration`。 信号 ID 和模型 ID 应是开发人员确定的 GUID，对于同一个关键字应保持一致。

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>验证是否已启用语音激活设置

若要使用语音激活，用户需要为其系统和应用程序启用语音激活。 可以在 Windows 设置中的“语音激活隐私设置”下找到此设置。 若要检查应用程序中语音激活设置的状态，请使用在注册关键字后检索到的 `ActivationSignalDetectionConfiguration` 实例。 `ActivationSignalDetectionConfiguration` 中的 [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) 字段包含一个用于描述语音激活设置状态的枚举值。

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>检索 ConversationalAgentSession 以将应用注册到 MVA 系统

`ConversationalAgentSession` 是 Windows SDK 中的一个类，应用可以通过此类使用应用状态（“空闲”、“正在检测”、“正在侦听”、“正在工作”、“正在讲话”）更新 Windows，并接收事件（例如激活检测，以及锁屏等系统状态更改）。 检索 AgentSession 实例也是将可通过语音激活的应用程序注册到 Windows 的一种方式。 最佳做法是保留对 `ConversationalAgentSession` 的一个引用。 若要检索会话，请使用 `ConversationalAgentSession.GetCurrentSessionAsync` API。

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>侦听两个激活信号：OnBackgroundActivated 和 OnSignalDetected

当 Windows 通过两种方式之一检测到关键字时，会向应用发出信号。 如果应用不是处于活动状态（即，你未引用 `ConversationalAgentSession` 的未释放实例），则 Windows 会启动你的应用，并调用应用程序的 App.xaml.cs 文件中的 OnBackgroundActivated 方法。 如果事件参数的 `BackgroundActivatedEventArgs.TaskInstance.Task.Name` 字段与字符串“AgentBackgroundTrigger”相匹配，则表示语音激活已触发应用程序启动。 应用程序需要替代此方法，并检索 ConversationalAgentSession 的实例以向现已处于活动状态的 Windows 发出信号。 当应用程序处于活动状态时，Windows 将使用 `ConversationalAgentSession.OnSignalDetected` 事件发出已发生语音激活的信号。 在你检索到 `ConversationalAgentSession` 后，立即向此事件添加一个事件处理程序。

## <a name="keyword-verification"></a>关键字验证

通过语音激活语音代理应用程序后，下一步是验证关键字检测是否有效。 Windows 不提供关键字验证解决方案，但确实允许语音助理从假设的激活访问音频并自行完成验证。

### <a name="retrieve-activation-audio"></a>检索激活音频

创建 [AudioGraph](/uwp/api/windows.media.audio.audiograph) 并将其传递给 `ConversationalAgentSession` 的 `CreateAudioDeviceInputNodeAsync`。 这会加载图的音频缓冲区，其中的音频 *从检测到关键字之前的大约 3 秒处开始*。 包含这段附加的前导音频是为了适应各种关键字长度和说话人语速。 然后，处理音频图中的 [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) 事件以检索音频数据。

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

注意：音频缓冲区中包含的前导音频可能导致关键字验证失败。 若要解决此问题，请在发送用于关键字验证的音频之前，剪裁掉音频缓冲区的开头部分。 应该根据每个助理定制此初始部分剪裁操作。

### <a name="launch-in-the-foreground"></a>在前台启动

关键字验证成功后，应用程序需要移到前台以显示 UI。 调用 `ConversationalAgentSession.RequestForegroundActivationAsync` API 将应用程序移到前台。

### <a name="transition-from-compact-view-to-full-view"></a>从精简视图到完整视图的转换

首次通过语音激活应用程序时，应用程序会在精简视图中启动。 请阅读[语音激活预览版的设计指南](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)，获取有关 Windows 上的语音助理的不同视图以及在这些视图之间进行转换的指导。

若要从精简视图转换到完整应用视图，请使用 ApplicationView API `TryEnterViewModeAsync`：

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>实施在锁定时激活

以下步骤说明了使 Windows 上的语音助理能够在锁定时运行所要满足的要求，其中包括可供参考的示例代码，以及有关管理应用程序生命周期的指导原则。

有关设计在锁定时的体验的指导，请访问[最佳做法指南](windows-voice-assistants-best-practices.md)。

如果应用在锁定时显示视图，我们认为该应用处于“展台模式”。 有关实现使用展台模式的应用的详细信息，请参阅[展台模式文档](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)。

### <a name="transitioning-above-lock"></a>在锁定时转换

在锁定时激活类似于在未锁定时激活。 如果没有处于活动状态的应用程序实例，将在后台启动一个新实例，并调用 App.xaml.cs 中的 `OnBackgroundActivated`。 如果有一个应用程序实例，该实例将通过 `ConversationalAgentSession.SignalDetected` 事件收到通知。

如果在锁定时未显示应用程序，则它必须调用 `ConversationalAgentSession.RequestForegroundActivationAsync`。 这会触发 App.xaml.cs 中的 `OnLaunched` 方法，然后应会导航到将在锁定时显示的视图。

### <a name="detecting-lock-screen-transitions"></a>检测锁屏转换

Windows SDK 中的 ConversationalAgent 库提供了一个 API 用于方便访问锁屏状态以及对锁屏状态所做的更改。 若要检测当前锁屏状态，请检查 `ConversationalAgentSession.IsUserAuthenticated` 字段。 若要检测锁定状态的更改，请向 `ConversationalAgentSession` 对象的 `SystemStateChanged` 事件添加一个事件处理程序。 每当屏幕状态从已解锁更改为已锁定或者从已锁定更改为已解锁时，将触发此事件处理程序。 如果事件参数的值为 `ConversationalAgentSystemStateChangeType.UserAuthentication`，则表示锁屏状态已更改。

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>检测“在锁定时激活”用户首选项

语音激活隐私设置页中的应用程序条目针对锁定时功能提供了一个切换开关。 要使应用能够在锁定时启动，用户需要打开此设置。 锁定时权限的状态还会存储在 ActivationSignalDetectionConfiguration 对象中。 AvailabilityInfo.HasLockScreenPermission 状态反映是否为用户授予了锁定时权限。 如果已禁用此设置，语音应用程序可以提示用户通过链接“ms-settings:privacy-voiceactivation”导航到相应的设置页，并提供有关如何为应用程序启用“在锁定时激活”的说明。

## <a name="closing-the-application"></a>关闭应用程序

若要在锁定时或在未锁定时以编程方式正确关闭应用程序，请使用 `WindowService.CloseWindow()` API。 这会触发所有 UWP 生命周期方法（包括 OnSuspend），使应用程序能够在关闭之前释放其 `ConversationalAgentSession` 实例。

> [!NOTE]
> 可以在不关闭[未锁定时实例](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)的情况下关闭应用程序。 在这种情况下，该锁定时视图需要“清理”，确保在解锁屏幕后，不存在任何会尝试操作该锁定时视图的事件处理程序或任务。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [访问 UWP 语音助理示例应用以进行示例和代码演练](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)
