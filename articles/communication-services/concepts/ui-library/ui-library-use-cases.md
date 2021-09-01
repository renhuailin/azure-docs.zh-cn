---
title: UI 库用例
titleSuffix: An Azure Communication Services concept document
description: 了解 UI 库以及它如何帮助你生成通信体验
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: dd338cd0f7d8e39f7f2d72445477991219e8d91c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651573"
---
# <a name="ui-library-use-cases"></a>UI 库用例

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> 有关 UI 库的详细文档，请访问 [UI 库 Storybook](https://azure.github.io/communication-ui-library)。 可在其中找到其他概念文档、快速入门和示例。


UI 库支持跨通话和聊天体验的多种情况。
这些功能通过 UI 组件和复合提供。
对于复合，这些功能是直接内置的，并在将复合集成到应用程序中时公开。
对于 UI 组件，这些功能通过组合 UI 功能和有状态基础库来公开。
为了充分利用这些功能，建议将 UI 组件与有状态通话和聊天客户端库结合使用。

## <a name="calling-use-cases"></a>通话用例

| 区域                | 用例                                              |
| ------------------- | ------------------------------------------------------ |
| Call Types          | 加入 Teams 会议                                     |
|                     | 使用组 ID 加入 Azure 通信服务通话   |
| [团队互操作](../teams-interop.md)      | 通话大厅                                             |
|                     | 听录和录制警报横幅               |
| 通话控件       | 将通话静音/取消静音                                       |
|                     | 在通话时打开/关闭视频                                   |
|                     | 屏幕共享                                         |
|                     | 结束通话                                               |
| 参与者库 | 远程参与者显示在网格上              |
|                     | 本地用户可在整个通话期间进行视频预览 |
|                     | 视频关闭时默认头像可用            |
|                     | 共享屏幕内容显示在参与者库上 |
| 通话配置  | 麦克风设备管理                           |
|                     | 相机设备管理                               |
|                     | 扬声器设备管理                              |
|                     | 用户可使用本地预览来查看视频        |
| 参与者        | 参与者名单                                     |

## <a name="chat-use-cases"></a>聊天用例

| 区域         | 用例                                        |
| ------------ | ------------------------------------------------ |
| 聊天类型   | 加入 Teams 会议聊天                        |
|              | 加入 Azure 通信服务聊天线程 |
| 聊天操作 | 发送聊天消息                                |
|              | 接收聊天消息                             |
| 聊天事件  | 键入指示符                                |
|              | 阅读回执                                     |
|              | 已添加/删除参与者                        |
|              | 已更改聊天标题                               |
| 参与者 | 参与者名单                               |

## <a name="supported-identities"></a>支持的标识

需要使用 Azure 通信服务标识来初始化有状态客户端库并对该服务进行身份验证。
有关身份验证的详细信息，请参阅[身份验证](../authentication.md)和[访问令牌](../../quickstarts/access-tokens.md?pivots=programming-language-javascript)

## <a name="teams-interop-use-case"></a>Teams 互操作用例

对于 [Teams 互操作](../teams-interop.md)场景，开发人员可以通过 Azure 通信服务使用 UI 库组件加入 Teams 会议。
若要启用 Teams 互操作，开发人员可以直接使用通话和聊天复合，也可使用 UI 组件来生成自定义体验。
在启用同时具有通话和聊天功能的应用程序时，请记住，在参与者获得许可加入通话之前无法初始化聊天客户端。
获得许可后，便可以初始化聊天客户端以加入会议聊天线程。
有关指导，请参阅下图：

:::image type="content" source="../media/teams-interop-pattern.png" alt-text="通话和聊天的 Teams 互操作模式":::

使用 UI 组件实现 Teams 互操作体验时，UI 库会提供体验关键部分的示例。
例如：
- [大厅示例](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--lobby)：参与者等待获得许可加入通话的大厅示例。
- [合规性横幅](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--compliance-banner)：向用户显示通话是否正在录制的示例横幅。
- [Teams 主题](https://azure.github.io/communication-ui-library/?path=/story/examples-themes--teams)：使 UI 库看起来像 Microsoft Teams 的示例主题。


## <a name="customization"></a>自定义

UI 库为开发人员提供了修改组件以适应其应用程序外观的模式。
这些功能是复合与 UI 组件之间区别的一个关键方面，其中复合提供的自定义选项较少，有利于实现更简单的集成体验。

| 用例                                            | 复合物 | UI 组件 |
| --------------------------------------------------- | ---------- | ------------- |
| 基于 Fluent 的主题                                | X          | X             |
| 体验布局可组合                     |            | X             |
| CSS 样式可用于修改样式属性  |            | X             |
| 图标可替换                               |            | X             |
| 参与者库布局可修改          |            | X             |
| 通话控件布局可修改                 | X          | X             |
| 可插入数据模型来修改用户元数据 | X          | X             |

## <a name="observability"></a>可观察性

在 UI 库的分离状态管理体系结构中，开发人员能够直接访问有状态的通话和聊天客户端。

开发人员可以连接到有状态客户端来读取状态、处理事件并替代传入 UI 组件的行为。

| 用例                                  | 复合物 | UI 组件 |
| ----------------------------------------- | ---------- | ------------- |
| 可访问通话/聊天客户端状态    | X          | X             |
| 可访问和处理客户端事件 | X          | X             |
| 可访问和处理 UI 事件     | X          | X             |

## <a name="recommended-architecture"></a>建议体系结构

:::image type="content" source="../media/ui-library-architecture.png" alt-text="UI 库建议的客户端-服务器体系结构":::

复合组件和基本组件使用 Azure 通信服务访问令牌进行初始化。 应通过你所管理的受信任服务从 Azure 通信服务获得访问令牌。 请参阅[快速入门：创建访问令牌](../../quickstarts/access-tokens.md?pivots=programming-language-javascript)和[受信任的服务教程](../../tutorials/trusted-service-tutorial.md)来获取详细信息。

这些客户端库还需要它们将要加入的通话或聊天的上下文。 与用户访问令牌类似，此上下文应通过你自己的受信任服务传送到客户端。 以下列表汇总了需要操作的初始化和资源管理功能。

| Contoso 责任                                 | UI 库责任                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| 从 Azure 提供访问令牌                          | 传递给定的访问令牌来初始化组件        |
| 提供刷新功能                                 | 使用开发人员提供的功能来刷新访问令牌          |
| 检索/传递用于通话或聊天的联接信息          | 传递通话和聊天信息以初始化组件 |
| 检索/传递任何自定义数据模型的用户信息 | 将自定义数据模型传递到要呈现的组件          |

## <a name="platform-support"></a>平台支持

| SDK 中 IsInRole 中的声明    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
| ------ | ------------------ | -------------------- | -------- | -------- | -------- | ---------- |
| UI SDK | Chrome\*、新版 Microsoft Edge | Chrome、Safari\*\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*请注意，除前两个版本外，还支持最新版本的 Chrome。

\*\*请注意，支持 Safari 版本 13.1+。 尚不支持 Safari macOS 的传出视频，但它在 iOS 上受支持。 仅桌面 iOS 支持传出屏幕共享。

## <a name="accessibility"></a>可访问性

辅助功能设计是 Microsoft 产品的一项原则。
UI 库遵循这一原则，以确保所有 UI 组件都是完全可访问的。
在公共预览阶段，UI 库将继续改进并向 UI 组件添加辅助功能。
我们希望在 UI 库正式发布之前添加更多有关辅助功能的详细信息。

## <a name="localization"></a>本地化

本地化是制造可由世界各地说不同语言的人使用的产品的关键。
UI 库将为某些语言和功能（如 RTL）提供现成支持。
开发人员可以提供自己的本地化文件以用于 UI 库。
这些本地化功能将在正式发布之前添加。

> [!div class="nextstepaction"]
> [访问 UI 库 Storybook](https://azure.github.io/communication-ui-library)
