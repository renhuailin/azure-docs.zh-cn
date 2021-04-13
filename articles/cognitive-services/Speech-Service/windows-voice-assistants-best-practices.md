---
title: Windows 语音助手 - 设计指南
titleSuffix: Azure Cognitive Services
description: 设计语音代理体验时的最佳做法指南。
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92165139"
---
# <a name="design-assistant-experiences-for-windows-10"></a>适用于 Windows 10 的设计助手体验

在 Windows 10 上开发的语音助手必须实现下面的用户体验指导原则，以便为 Windows 10 上的语音激活提供可能的最佳体验。 本文档将指导开发人员了解语音助手与 Windows 10 Shell 集成所需的关键工作。

## <a name="contents"></a>目录

- [Windows 10 中支持的语音激活视图摘要](#summary-of-voice-activation-views-supported-in-windows-10)
- [要求摘要](#requirements-summary)
- [良好聆听体验最佳做法](#best-practices-for-good-listening-experiences)
- [应用内语音激活设计指南](#design-guidance-for-in-app-voice-activation)
- [锁定时语音激活设计指南](#design-guidance-for-voice-activation-above-lock)
- [语音激活预览设计指南](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10 中支持的语音激活视图摘要

Windows 10 根据设备上下文推断客户上下文的激活体验。 以下摘要表简要概述了屏幕打开时可用的不同视图。

| 视图（可用性） | 设备上下文 | 客户目标 | 出现时间 | 设计需求 |
| --- | --- | --- | --- | --- |
| **应用内 (19H1)** | 锁定后，助手具有焦点 | 与助手应用交互 | 助手处理应用内的请求 | 应用内主视图聆听体验 |
| **锁定时 (19H2)** | 锁定时，未经身份验证 | 与助手交互，但从远处操作 | 系统已锁定，但助手请求激活 | 面向远场 UI 的全屏视觉对象。 实现关闭策略，以不阻止解锁。 |
| **语音激活预览 (20H1)** | 锁定后，助手没有焦点 | 与助手交互，但以干扰性较低的方式操作 | 系统已锁定，助手请求后台激活 | 最小画布。 根据需要调整主应用视图的大小或将其转交给主应用屏幕。 |

## <a name="requirements-summary"></a>要求摘要

访问不同体验需要最少的工作量。 但是，助手需要为每个视图实现正确的设计指南。 下表列出了必须遵循的要求清单。

| **语音激活视图** | **助理要求摘要** |
| --- | --- |
| **应用中** | <ul><li>处理应用内请求</li><li>为聆听状态提供 UI 指示器</li><li>窗口大小更改时 UI 将自适应</li></ul> |
| **锁定时** | <ul><li>检测锁定状态和请求激活</li><li>不要提供可能阻止访问 Windows 锁屏界面的始终持久 UX</li><li>提供全屏视觉对象和语音优先体验</li><li>遵循下面的关闭指导</li><li>遵循下面的隐私和安全性注意事项</li></ul> |
| **语音激活预览** | <ul><li>检测解锁状态并请求后台激活</li><li>在预览窗格中绘制最少聆听 UX</li><li>在右上方绘制关闭 X，并在按下时自行关闭和停止流式传输音频</li><li>根据需要调整主助手应用视图的大小或将其转交给主应用屏幕以提供答案</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>良好聆听体验最佳做法

助手应构建一个聆听体验来提供重要的反馈，使客户能够了解助手的状态。 下面是构建助手体验时需要考虑的一些可能的状态。 这些只是可能的建议，不是必需的指南。

- 助手可用于语音输入
- 助手正在激活（按下关键字或麦克风按钮）
- 助手正在主动将音频流式传输到助手云
- 助手已准备好让客户开始讲话
- 助手听到客户开始讲话
- 助手了解客户已完成讲话
- 助手正在处理并准备响应
- 助手正在响应

即使状态更改迅速，也应考虑为状态提供 UX，因为在 Windows 生态系统中，持续时间是可变的。 视觉反馈以及简短的音频打钟声或鸟鸣（也称为“耳标”）都是解决方案的一部分。 同样，与音频说明耦合的可视卡可提供良好的响应选项。

## <a name="design-guidance-for-in-app-voice-activation"></a>应用内语音激活设计指南

当助手应用具有焦点时，客户意向清楚地与应用进行交互，因此所有语音激活体验都应由主应用视图处理。 客户可能会调整视图大小。 为了帮助说明助手 shell 交互，本文档的其余部分采用名为 Contoso 的财务服务助理的具体示例。 在此关系图和后续关系图中，客户所说的内容将显示在左侧的卡通语音气泡中，而助手响应显示在右侧的卡通气泡中。

应用内视图。语音激活开始时的初始状态：
![显示 Contoso 金融助手应用程序打开了默认画布的屏幕截图。 右侧的卡通语音气泡显示“Contoso”。](media/voice-assistants/windows_voice_assistant/initial_state.png)

应用内视图。语音激活成功后，聆听体验开始：![语音助手正在聆听时 Windows 语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/listening.png)

应用内视图。所有响应都将保留在应用体验中。![助手答复时 Windows 语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>锁定时语音激活设计指南

19H2 中可用，在 Windows 语音激活平台上构建的助手可用于在锁定时响应。

### <a name="customer-opt-in"></a>客户选择加入

默认情况下，始终禁用锁定时语音激活。 客户通过“Windows 设置”>“隐私”>“语音激活”选择加入。 有关监视和提示此设置的详细信息，请参阅[锁定时实现指南](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)。

### <a name="not-a-lock-screen-replacement"></a>不是锁屏的替代

尽管通知或其他标准应用锁屏集成点仍可供助手使用，但 Windows 锁屏界面仍始终定义初始客户体验，直到语音激活发生。 检测到语音激活后，助手应用会暂时显示在锁屏上方。 为了避免客户混淆，当在锁定时激活时，助手应用程序不得显示 UI 来要求提供任意类型的凭据或登录信息。

![Windows 锁屏界面的屏幕截图](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>锁定时语音激活后的体验

当屏幕打开时，助手应用程序为全屏，且锁屏上不显示任何标题栏。 较大的视觉对象和强大的语音说明，并具有强大的语音主界面，支持客户距离太远看不到 UI 时或正忙着其他（非 PC）任务时的方案。

当屏幕保持关闭时，助手应用可以播放耳标来指示助手正在激活，并提供仅语音体验。

![锁定时语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>关闭策略

助手必须实现本部分中的关闭指南，使客户在下次要使用 Windows PC 时可以更轻松地登录。 以下是助手必须实现的特定要求：

- 锁定时显示的所有助手画布都必须在右上方包含一个“X”，用于关闭助手。
- 按任意键还必须关闭助手应用。 键盘输入是客户想要登录的传统锁应用信号。 因此，不应将任何键盘/文本输入定向到应用。 相反，应用程序应在检测到键盘输入时自行关闭，以便客户可以轻松登录到其设备。
- 如果屏幕关闭，则应用必须自行关闭。 这可确保客户下次使用电脑时，登录屏幕将准备就绪并等待他们使用。
- 如果应用正在“使用中”，则它可以继续在锁定时使用。 “使用中”&quot;&quot;构成了任何输入或输出。 例如，当流式传输音乐或视频时，应用可以继续在锁定时使用。 允许执行“后续”和其他多回合对话框步骤，使应用在锁定时保持运行。
- 有关关闭应用程序的实现详细信息，请参阅[锁定时实现指南](windows-voice-assistants-implementation-guide.md#closing-the-application)。

![显示 Contoso 财务助手应用的锁定时视图的屏幕截图。](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![显示 Windows 锁屏的桌面的屏幕截图。](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>锁定时的隐私和安全性注意事项

很多 PC 是便携的，但并不总是在客户可接触到的范围内。 它们可能被暂时留在旅馆房间、飞机座位或工作区中，这些场所中的其他人可能会物理接触到这些 PC。 如果锁定时启用的助手未准备就绪，可能会受到所谓的“[evil maid](https://en.wikipedia.org/wiki/Evil_maid_attack)”（邪恶女仆）攻击。

因此，助手应遵循此部分中的指南来帮助保持体验安全。 如果未对 Windows 用户进行身份验证，则会发生锁定时交互。 这意味着，通常情况下，在助手中的输入还应视为未经身份验证。

- 助手应实现技能允许列表，以确定在锁定时访问确认安全的技能。
- 说话人 ID 技术可以在缓解某些风险的情况下发挥作用，但说话人 ID 并不是 Windows 身份验证的合适替代品。
- 允许的技能列表应考虑三类操作或技能：

| **操作类** | **说明** | **示例（非完整列表）** |
| --- | --- | --- |
| 未经身份验证时安全 | 常规用途信息或基本应用命令和控制 | “什么时间？”，“播放下一个曲目” |
| 使用说话人 ID 时安全 | 模拟风险，泄露个人信息。 | “我的下一个约会在几点？”，“查看我的购物清单”，“回复来电” |
| 仅在 Windows 身份验证后安全 | 攻击者可能会用来损害客户的高风险操作 | “购买更多日用品”，“删除我的（重要）约会”，“发送（不友好的）短信”，“启动（恶意）网页” |

对于 Contoso，有关公开发行股票信息的常规信息在未经身份验证的情况下是安全的。 特定于客户的信息（例如，拥有的共享数）很可能在使用说话人 ID 时是安全的。 但是，在未经 Windows 身份验证的情况下，不允许购买或销售股票。

为了进一步保护体验、Web 链接或其他应用到应用，Windows 将始终阻止启动，直到客户登录。 作为最后的缓解措施，如果不及时解决严重安全问题，Microsoft 保留从已启用助手的允许列表中删除应用程序的权利。

## <a name="design-guidance-for-voice-activation-preview"></a>语音激活预览设计指南

锁定后，如果助手应用没有焦点，Windows 将提供一个干扰较少的语音激活 UI，帮助客户保留在流中。 对于在启动完整应用时会造成严重影响的错误激活，此操作尤其适用。 核心理念是，每个助手在外壳程序中都有另一个主页，即助手任务栏图标。 当发生后台激活请求时，将在助手任务栏图标上方显示一个小视图。 助手应在此画布中提供小的聆听体验。 处理请求后，助手可以选择调整此视图的大小以显示上下文中答案，或转交主应用视图以显示更大、更详细的视觉对象。

- 为了保持最小，预览没有标题栏，因此，助手必须在右上方绘制一个 X，以允许客户关闭视图。 对于在按下“关闭”按钮时要调用的特定 API，请参阅[关闭应用程序](windows-voice-assistants-implementation-guide.md#closing-the-application)。
- 为了支持语音激活预览，助手可能会邀请客户在首次运行时将助手固定到任务栏。

**语音激活预览：初始状态**

Contoso 助手在任务栏上有一个主页：即卷状圆形图标。

![在任务栏图标预激活时 Windows 语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

激活过程中，助手请求后台激活。 助手会获得一个小预览窗格（默认宽度为 408，高度为 248）。 如果服务器端语音激活确定信号为误报，则可以关闭此视图以将中断的可能性降至最低。

![验证激活时，紧凑视图中的 Windows 语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

确认最终激活后，助手会显示其聆听 UX。 助手必须始终在语音激活预览的右上方绘制一个关闭 X。

![紧凑视图中的 Windows 聆听语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

语音激活预览中可能会显示快速答案。 TryResizeView 将允许助手请求不同的大小。

![紧凑视图中的 Windows 答复语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

转交。 助手随时可能会转交给其主应用视图，以提供更多信息、对话框或需要更多屏幕空间的答案。 有关实现的详细信息，请参阅[从紧凑视图过渡到完整视图](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view)部分。

![展开紧凑视图之前和之后的 Windows 语音助手的屏幕截图](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始开发你的语音助手](how-to-windows-voice-assistants-get-started.md)