---
title: 获取活动说话人
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 在通话中呈现活动说话人。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: cb2391db2f24eaf4dca4e0463ab508b27361fae0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698993"
---
# <a name="get-active-speakers-within-a-call"></a>在通话中获取活动说话人

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

在活动通话期间，可能需要获取活动说话人列表，以便以不同方式呈现或显示这些说话人。 操作方法如下。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../quickstarts/access-tokens.md)。
- 可选：完成[向应用程序添加语音呼叫](../../quickstarts/voice-video-calling/getting-started-with-calling.md)的快速入门

[!INCLUDE [Dominant Speaker JavaScript](./includes/dominant-speaker/dominant-speaker-web.md)]

## <a name="next-steps"></a>后续步骤
- [了解如何管理视频](./manage-video.md)
- [了解如何管理通话](./manage-calls.md)
- [了解如何记录通话](./record-calls.md)