---
title: UI 库概述
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务 UI 库。
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 86ef660c5d4b0f0132218df1df37c8934f27ec08
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653115"
---
# <a name="ui-library-overview"></a>UI 库概述

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> 有关 UI 库的详细文档，请访问 [UI 库 Storybook](https://azure.github.io/communication-ui-library)。 可在其中找到其他概念文档、快速入门和示例。

Azure 通信服务 - UI 库使你能够轻松地使用 Azure 通信服务构建现代通信用户体验。 它提供了一个可用于生产的 UI 组件库，可以将其置于应用程序中：

- 复合组件 - 这些组件是实现常见通信场景的统包解决方案。
  可以向应用程序快速添加视频通话或聊天体验。
  复合组件是使用 UI 组件生成的开源高阶组件。

- UI 组件 - 这些组件是开源构建基块，可用于生成自定义通信体验。
  提供了实现通话和聊天功能的组件，可以将这些组件组合在一起来构建体验。

这些 UI 客户端库都使用 [Microsoft 的 Fluent 设计语言](https://developer.microsoft.com/fluentui/)和资产。 Fluent UI 为 UI 库提供一个基础层，并在 Microsoft 产品中广泛使用。

与 UI 组件一起，UI 库公开了一个用于通话和聊天的有状态客户端库。
此客户端与任何特定的状态管理框架无关，可以与常见的状态管理器（如 Redux 或 React Context）集成。
此有状态客户端库可与 UI 组件结合使用，为 UI 组件传递属性和方法以呈现数据。 有关详细信息，请参阅[有状态客户端概述](https://azure.github.io/communication-ui-library/?path=/story/stateful-client-what-is-stateful--page)。

## <a name="installing-ui-library"></a>安装 UI 库

有状态客户端是 `@azure/communication-react` 包的一部分。 

```bash
npm i --save @azure/communication-react
```

## <a name="composites-overview"></a>复合组件概述

复合组件是由 UI 组件组成的较高级别的组件，它们使用 Azure 通信服务为常见通信场景提供统包解决方案。
开发人员可以使用 Azure 通信服务访问令牌和通话或聊天所需配置来轻松地实例化复合组件。

| 合成    | 用例  | 
| ------------ | ---------- |
| [CallComposite](https://azure.github.io/communication-ui-library/?path=/story/composites-call--basic-example) | 允许用户发起或加入通话的通话体验。 在体验中，用户可以配置自己的设备，通过视频参与通话，以及查看其他参与者，其中包括打开视频的参与者。 Teams 互操作性包括大厅功能，供用户等待获准进入。 |
| [ChatComposite](https://azure.github.io/communication-ui-library/?path=/story/composites-chat--basic-example)    | 用户可以发送和接收消息的聊天体验。 如输入、读取、参与者进入和离开等线程事件将作为聊天线程的一部分显示给用户。                                                                                                                          |

## <a name="ui-component-overview"></a>UI 组件概述

纯 UI 组件，开发人员可以用于组合通信体验，从将视频磁贴拼接成网格以展示远程参与者，到组织组件以符合应用程序规范。
UI 组件支持自定义，为组件提供适当的感观，以匹配应用程序的品牌和风格。

| 区域    | 组件    | 说明       |
| ------- | ------------ | ----------------- |
| 调用 | [网格布局](https://azure.github.io/communication-ui-library/?path=/story/ui-components-gridlayout--grid-layout)                | 将视频磁贴组织成 NxN 网格的网格组件                                            |
|         | [视频磁贴](https://azure.github.io/communication-ui-library/?path=/story/ui-components-videotile--video-tile)                   | 在可用时显示视频流，在不可用时显示默认静态组件的组件        |
|         | [控件条](https://azure.github.io/communication-ui-library/?path=/story/ui-components-controlbar--control-bar)                | 组织 DefaultButton 以连接到静音或共享屏幕等特定通话操作的容器 |
|         | [VideoGallery](https://azure.github.io/communication-ui-library/?path=/story/ui-components-video-gallery--video-gallery)                                           | 随着参与者的加入而动态变化的统包视频库组件               |
| 聊天    | [消息线程](https://azure.github.io/communication-ui-library/?path=/story/ui-components-messagethread--message-thread)       | 呈现聊天消息、系统消息和自定义消息的容器                          |
|         | [发送框](https://azure.github.io/communication-ui-library/?path=/story/ui-components-sendbox--send-box)                         | 具有离散发送按钮的文本输入组件                                                   |
|         | [消息状态指示器](https://azure.github.io/communication-ui-library/?path=/story/ui-components-messagestatusindicator--message-status-indicator)        | 显示已发送消息的状态的多状态阅读回执组件                                   |
|         | [键入指示符](https://azure.github.io/communication-ui-library/?path=/story/ui-components-typingindicator--typing-indicator) | 用于呈现正在线程上主动键入的参与者的文本组件                      |
| 通用  | [参与者项](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participantitem--participant-item) | 用于呈现通话或聊天参与者（包括头像和显示名称）的常用组件            |
|         | [参与者列表](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participantlist--participant-list)                                 | 用于呈现通话或聊天参与者列表（包括头像和显示名称）的常用组件       |

## <a name="what-ui-artifact-is-best-for-my-project"></a>哪些 UI 项目最适合我的项目？

了解这些要求可帮助你选择正确的客户端库：

- **你需要多少自定义？** Azure 通信核心客户端库没有用户体验，它的设计初衷是让你可以构建任何你想要的用户体验。 UI 库组件以减少自定义为代价提供 UI 资产。
- **你的目标平台是什么？** 不同平台具有不同功能。

有关 UI 库中的功能可用性的详细信息，请参阅[此处](https://azure.github.io/communication-ui-library/?path=/story/use-cases--page)，但下面汇总了一些重要权衡。

| 客户端库/SDK  | 实施复杂性 | 自定义功能 | 调用 | 聊天 | [团队互操作](../teams-interop.md) |
| --------------------- | ------------------------- | --------------------- | ------- | ---- | ----------------------------------------------------------------------------------------------------- |
| 复合组件  | 低                       | 低                   | ✔       | ✔    | ✔                                                                                                     |
| 基本组件       | 中                    | 中                | ✔       | ✔    | ✔                                                                                                     |
| 核心客户端库 | 高                      | 高                  | ✔       | ✔    | ✔                                                                                                     |

> [!div class="nextstepaction"]
> [访问 UI 库 Storybook](https://azure.github.io/communication-ui-library)
