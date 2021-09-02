---
title: Teams Embed SDK
titleSuffix: An Azure Communication Services - Teams Embed SDK description
description: 本文档介绍 Teams Embed 功能以及如何在应用程序中工作
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 49fca98d6bd8bc45cb7306dbc6ae9fae5047651b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256819"
---
# <a name="teams-embed"></a>Teams Embed

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Teams Embed 是一项 Azure 通信服务功能，着眼于企业到消费者和企业对企业呼叫交互。 Teams Embed 系统的核心是[视频和语音呼叫](../voice-video-calling/calling-sdk-features.md)，其依托于 Azure 的呼叫基元，基于 Microsoft Teams 会议提供一套完整的用户体验。

Teams Embed SDK 是闭源，以全包式的复合格式向你提供这些功能。 你只需将 Teams Embed 拖放到应用的画布中，此 SDK 便会生成完整的用户体验。 由于这种用户体验非常类似于 Microsoft Teams 会议，你可以享受到以下优势：

- 减少开发时间和工程复杂性。
- 最终用户熟悉 Microsoft Teams 体验。
- 能够重复使用 [Microsoft Teams 的最终用户培训内容](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)。

Teams Embed SDK 提供 Microsoft Teams 会议支持的大多数功能，其中包括：

## <a name="joining-a-meeting"></a>加入会议

用户可以使用 Microsoft Teams 会议 URL 轻松地加入会议，获得更简单的出色体验，就像使用 Microsoft Teams 应用程序一样。 向用户添加功能，使其成为广泛的实时会议的一部分，同时又不会失去 Microsoft Teams 应用程序的简单性体验。

## <a name="pre-meeting-experience"></a>会议前体验

作为任何会议的参与者，你可以设置音频和视频设备的默认配置。 添加你的姓名，并提供自己的头像。

## <a name="meeting-experience"></a>会议体验

自定义用户体验，根据需要调整功能。 你将在会议期间控制总体的体验。

- [内容共享](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8)：用户可以共享视频、照片或整个屏幕，用户将看到共享内容。

- [视频库的多个布局选项](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae)：在会议过程中，可以选择布局默认选项：大型库，同时模式、焦点、固定和聚焦。 并根据设备分辨率相应地调整布局。

- [打开/关闭视频](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae#bkmk_turnvideoonoff)：使用户能够在会议期间管理其视频。

- 参与者操作：用户可以进行[“举手”](https://support.microsoft.com/en-us/office/raise-your-hand-in-a-teams-meeting-bb2dd8e1-e6bd-43a6-85cf-30822667b372)、静音和取消其麦克风的静音、更改相机或音频配置、挂起和更多操作。

- [多语言支持](https://support.microsoft.com/topic/languages-supported-in-microsoft-teams-for-education-293792c3-352e-4b24-9fc2-4c28b5de2db8)：在整个 Microsoft Teams 体验中支持 56 语言。

## <a name="quality-and-security"></a>质量和安全性

Teams Embed SDK 是根据 Microsoft Teams 质量标准所构建，因此对于视频质量，你可以查看[带宽需求。](/microsoftteams/prepare-network#bandwidth-requirements)

你可以使用 Azure Communication Service 访问令牌，如需了解详细信息，请参阅[如何生成和管理访问令牌。](../../quickstarts/access-tokens.md)

## <a name="capabilities"></a>功能

| SDK 功能                                                        | 可用性 |
|---------------------------------------------------------------------|--------------|
| 会议操作                                                   |              |
| 加入通话                                                         | 是          |
| 处理通话状态（包括连接状态、参与者计数以及麦克风或相机状态等形式）                                           | 是          |
| 增加用户事件“举手”、“静音”和发送视频事件                                                                 | 是          |
| 不可靠网络处理支持                                      | 是          |
| 从会议中删除参与者                                    | 是          |
| 支持 56 种语言                                               | 是          |
| 在会议和 1n1 聊天期间聊天                                    | 否           |
| PSTN 呼叫                                                        | 否           |
| 录制和听录                                            | 否           |
| 白板共享                                                  | 否           |
| 闯入房间                                                 | 否           |
| 加入会议体验                                        |              |
| 使用 GUID 和 ACS 令牌加入群通话                             | 是          |
| 使用 Live 会议 URL 和 ACS 令牌加入会议                    | 是          |
| 使用会议 URL 和 ACS 令牌加入会议                         | 是          |
| 通过在大厅等候加入                                           | 是          |

| 用户体验自定义                                       | 可用性 |
|---------------------------------------------------------------------|--------------|
| 会议操作                                                   |              |
| 显示呼叫名单                                             | 是          |
| 自定义布局：颜色、图标、按钮                        | 部分    |
| 自定义呼叫屏幕图标                                     | 是          |
| 更改会议视图布局                                         | 是          |
| 动态调用 NxN 布局更改                                    | 是          |
| 举手/放下手                                                     | 是          |
| 静音/取消静音                                                        | 是          |
| 置于保持状态                                                         | 是          |
| 选择音频路由                                                | 是          |
| 选择照相机                                                       | 是          |
| 共享照片、屏幕和视频                                       | 是          |
| 开始/停止视频                                                    | 是          |
| 用户磁贴按下事件                                               | 是          |
| 名牌按下事件                                              | 是          |
| 背景模糊                                                     | 是          |
| 自定义屏幕背景色                               | 否           |
| 自定义顶部/底部栏颜色                                  | 否           |
| 白板共享                                                  | 否           |
| 会议前体验                                            |              |
| 加入可以配置显示名称，启用照片共享            | 是          |
| 加入可以配置为显示呼叫暂存视图（呼叫前屏幕）   | 是          |
| 加入可以配置为在呼叫屏幕上显示名牌             | 是          |
| 自定义大厅屏幕                                          | 部分    |

若要详细了解如何开始使用 Teams Embed SDK，请遵循[入门指南](../../quickstarts/meeting/getting-started-with-teams-embed.md)。 若要详细了解 SDK 功能，请参阅[示例指南](../../quickstarts/meeting/samples-for-teams-embed.md)。