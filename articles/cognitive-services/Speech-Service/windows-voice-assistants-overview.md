---
title: Windows 上的语音助手 - 概述
titleSuffix: Azure Cognitive Services
description: Windows 上的语音助手概述，包括可用的功能和开发资源。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 1d961831094bf377acef910572be459ed18db4cb
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113652424"
---
# <a name="voice-assistants-on-windows"></a>Windows 上的语音助手

在 Windows 10 2004 版本和更高版本上，语音助手应用程序可以利用 Windows ConversationalAgent API 实现全面的语音助手体验。

## <a name="voice-assistant-features"></a>语音助手功能

语音代理应用程序可以通过读出关键字激活，获得免手动的语音驱动体验。 当应用程序关闭并且屏幕被锁定时，语音激活可发挥作用。

此外，Windows 还提供了一组语音激活隐私设置，这些设置使用户可以基于每个应用控制语音激活和上述锁定激活。

语音激活后，Windows 将正确管理多个活动代理，并在出现中断或停用时通知各个语音助手。 这样，应用程序便可以正确管理中断和其他代理间事件。

## <a name="how-does-voice-activation-work"></a>语音激活的原理是什么？

代理激活运行时 (AAR) 是在 Windows 中通过说出关键字或按下按钮来管理应用程序激活的持续进程。 只要系统上至少有一个应用程序注册到系统中，它就会通过 Windows 开始运行。 应用程序通过 Windows SDK 中的 ConversationalAgent API 与 AAR 进行交互。

当用户说出关键字时，系统上的软件或硬件关键字检测工具将通知 AAR 检测到了关键字，同时提供关键字 ID。 继而，AAR 会向 BackgroundService 发送请求，通过相应的应用程序 ID 启动该应用程序。

### <a name="registration"></a>注册

首次运行语音激活的应用程序时，它会通过 ConversationalAgent API 注册其应用 ID 和关键字信息。 AAR 会将全局映射中的所有配置都注册到系统上的硬件或软件关键字检测工具，使其能够检测到应用程序的关键字。 应用程序还会[向后台服务注册](/windows/uwp/launch-resume/register-a-background-task)。

请注意，这意味着应用程序只有运行一次且完成注册之后，才能通过语音激活。

### <a name="receiving-an-activation"></a>接收激活

收到来自 AAR 的请求后，后台服务将启动该应用程序。 应用程序通过 `App.xaml.cs` 中的 OnBackgroundActivated 生命周期方法接收信号，其中包含唯一的事件参数。 此参数将通知应用程序它已被 AAR 激活，并应启动关键字验证。

如果应用程序成功验证关键字，则可以发出在前台显示的请求。 如果此请求成功，该应用程序则会显示 UI，并继续与用户交互。

在有人说出活动应用程序的关键字时，AAR 仍会发出信号。 不过，它不是通过 `App.xaml.cs` 中的生命周期方法发出信号，而是通过 ConversationalAgent API 中的事件发出信号。

### <a name="keyword-verification"></a>关键字验证

触发应用程序启动的关键字检测工具已通过简化关键字模型实现低功耗。 这使关键字检测工具能够“始终在线“，而不会造成高功耗影响，但这也意味着关键字检测工具可能会收到很多“误报”，即在没有说出关键字时也会检测到关键字。 这就是语音激活系统在后台启动应用程序的原因：使应用程序有机会在中断用户的当前会话之前验证关键字是否被说出。 AAR 会保存音频，几秒之后便会检测到关键字，并使其可被应用程序访问。 应用程序可借此机会对同一音频运行更可靠的关键字检测工具。

## <a name="next-steps"></a>后续步骤

- **查看设计准则：** 我们的 [设计指南](windows-voice-assistants-best-practices.md)列出了在 Windows 10 上提供语音激活最佳体验所需的关键工作。
- **访问入门页面：** 请参阅 [此处](how-to-windows-voice-assistants-get-started.md)了解开始在 Windows 上实施语音助手的步骤，包括设置开发环境到实施指南介绍。
- **试用示例应用**：若要亲自体验这些功能，请访问 [UWP 语音助手示例](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample)页，然后按照步骤运行示例客户端。