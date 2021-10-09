---
title: 转移呼叫
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 转移呼叫。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e68c81a88c972cc05ffafea0d94e82c84de4268f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699103"
---
# <a name="transfer-calls"></a>转移呼叫

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

在活动呼叫期间，可能需要将呼叫转移到另一个人或号码。 让我们来了解如何操作。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../quickstarts/access-tokens.md)。
- 可选：完成快速入门[向应用程序添加语音呼叫](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

[!INCLUDE [Transfer calls Client-side JavaScript](./includes/transfer-calls/transfer-calls-web.md)]

## <a name="next-steps"></a>后续步骤
- [了解如何管理通话](./manage-calls.md)
- [了解如何管理视频](./manage-video.md)
- [了解如何记录通话](./record-calls.md)
- [了解如何转录通话](./call-transcription.md)