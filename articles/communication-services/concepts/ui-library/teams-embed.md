---
title: UI 库 Teams Embed
titleSuffix: An Azure Communication Services quickstart
description: 在本文档中，你将了解如何使用 Azure 通信服务 UI 库 Teams Embed 功能来构建统包式呼叫体验。
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c342d839cf794c490903a880bc7578d6cdbe50b1
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113112538"
---
# <a name="teams-embed"></a>Teams Embed

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Teams Embed 是一项 Azure 通信服务功能，着眼于企业到消费者和企业对企业呼叫交互。 Teams Embed 系统的核心是[视频和语音呼叫](../voice-video-calling/calling-sdk-features.md)，但 Teams Embed 系统依托于 Azure 的呼叫基元，基于 Microsoft Teams 会议提供一套完整的用户体验。

Teams Embed SDK 是一个闭源，以全包式的复合格式向你提供这些功能。 你只需将 Teams Embed 拖放到应用的画布中，此 SDK 便会生成完整的用户体验。 由于这种用户体验非常类似于 Microsoft Teams 会议，你可以享受到以下优势：

- 减少开发时间和工程复杂性
- 最终用户熟悉 Microsoft Teams
- 能够重复使用 [Microsoft Teams 的最终用户培训内容](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

Teams Embed 提供了 Microsoft Teams 会议支持的大多数功能，其中包括：

- 由用户配置其音频和视频设备的会议前体验
- 配置音频和视频设备的会议体验
- [视频背景](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780)：允许参与者模糊或替换其背景
- [用于视频库的多个选项](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae)：大图库、协同、焦点、单边锁定和聚光灯
- [内容共享](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8)：允许参与者共享其屏幕

有关此 UI 与其他 Azure 通信 SDK 的比较信息，请参阅 [UI SDK 概念介绍](ui-library-overview.md)。 
