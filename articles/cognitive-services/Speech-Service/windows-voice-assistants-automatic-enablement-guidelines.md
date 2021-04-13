---
title: Windows 上的语音助理的隐私准则
titleSuffix: Azure Cognitive Services
description: 为语音代理启用语音激活的默认说明。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "90987400"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows 上的语音助理的隐私准则

向用户提供有关其语音数据的收集和使用方式的明确信息，这一点非常重要，另外，让用户能够控制这种收集是否发生以及如何发生，这一点也很重要。 这些隐私核心方面（公开和许可）对于集成到 Windows 中的语音助理尤其重要，因为它们的使用具有始终聆听的性质。

在 Windows 上创建语音助理的开发人员必须在其应用程序中包含清晰的用户界面元素，以反映助理的聆听功能。

> [!NOTE]
> 如果未能对助理应用程序提供适当的公开和同意（包括在应用程序更新之后），可能会导致在隐私问题得到解决之前，助理无法进行语音激活。

## <a name="minimum-requirements-for-feature-inclusion"></a>包含功能的最低要求

Windows 用户可以在 `Settings > Privacy > Voice activation` 中查看和控制其助理应用程序的可用性。

 > [!div class="mx-imgBorder"]
 > [![屏幕截图显示了控制 Cortana 可用性的选项。](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "助理应用程序的 Windows 语音激活隐私设置条目")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

要想有资格包含在此列表中，请通过 winvoiceassistants@microsoft.com 联系 Microsoft 以开始。 默认情况下，用户需要在 `Settings > Privacy > Voice Activation` 中为新助理显式启用语音激活，应用程序可以通过 `ms-settings:privacy-voiceactivation` 进行协议链接。 允许的应用程序在运行并使用 `Windows.ApplicationModel.ConversationalAgent` API 后会出现在列表中。 当应用程序获取了用户的麦克风同意后，其语音激活设置将可以进行修改。

由于 Windows 隐私设置包含有关语音激活的工作方式的信息，并且具有用于控制权限的标准 UI，因此公开和同意均可实现。 助理将保留在此允许列表中，但前提是它不会：

* 在助理的语音激活或语音数据处理方面误导用户或向用户提供错误信息
* 过度干扰另一个助理
* 违反任何其他相关的 Microsoft 策略

如果发现任何上述情形，Microsoft 可能会从允许列表中删除助理，直到问题得到解决。

> [!NOTE]
> 在所有情况下，语音激活权限都需要具有麦克风权限。 如果助理应用程序没有麦克风访问权限，它将不能进行语音激活，并将在语音激活隐私设置中显示为禁用状态。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>获得麦克风许可的其他要求

如果助理作者希望使用户能够更轻松、更流畅地选择使用语音激活，可以通过满足更多要求来满足公开和同意的要求，而无需额外进入设置页面。 一旦获得批准，在用户向助理应用程序授予麦克风权限后，语音激活就会立即可用。 为了满足此要求，助理应用程序必须先执行以下操作，然后再提示提供麦克风许可（例如，通过使用 `AppCapability.RequestAccessAsync` API）：

1. 为用户提供清晰且明显的指示：应用程序想要聆听用户对关键字的声音（即使应用程序未在运行），需要获得用户同意
1. 包括数据使用情况和隐私策略的相关信息，例如官方隐私声明的链接
1. 避免在公开音频捕获行为的体验流程中使用任何指令性或引导性的措辞（例如，“在下面的提示消息上单击‘是’”）

如果应用程序符合上述所有条件，则有资格启用语音激活功能以及获得麦克风同意。 请联系 winvoiceassistants@microsoft.com 以了解详细信息并了解首次使用体验。

> [!NOTE]
> 锁定时的语音激活不符合麦克风访问的自动启用要求，仍需要用户访问“语音激活隐私”页，以便对助理启用锁定时的访问权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Windows 上的语音助理的最佳做法](windows-voice-assistants-best-practices.md)