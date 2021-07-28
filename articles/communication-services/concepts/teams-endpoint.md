---
title: 自定义 Teams 终结点
titleSuffix: An Azure Communication Services concept document
description: 生成自定义 Teams 终结点
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: acbd6a332d74a9b244f34230ac4b4eb591a1ab2d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108056"
---
# <a name="custom-teams-endpoint"></a>自定义 Teams 终结点

> [!IMPORTANT]
> 若要启用/禁用自定义 Teams 终结点体验，请完成[此表单](https://forms.office.com/r/B8p5KqCH19)。

Azure 通信服务可用于生成自定义 Teams 终结点，以便与 Microsoft Teams 客户端或其他自定义 Teams 终结点进行通信。 利用自定义 Teams 终结点，可以为 Teams 用户自定义语音、视频、聊天和屏幕共享体验。

你可以使用 Azure 通信服务标识 SDK，将 AAD 用户令牌换成 Teams 的访问令牌。 下图演示了多租户用例，其中 Fabrikam 是 Contoso 公司的客户。

## <a name="calling"></a>调用 

语音、视频和屏幕共享功能是通过 Azure 通信服务呼叫 SDK 提供的。 下图显示了在将呼叫体验与自定义 Teams 终结点集成时要遵循的过程的概述。

![为自定义 Teams 终结点体验启用呼叫功能的过程](./media/teams-identities/teams-identity-calling-overview.png)

## <a name="chat"></a>聊天

你还可以使用自定义 Teams 终结点，通过图形 API 选择性地集成聊天功能。 详细了解[本文档](https://docs.microsoft.com/graph/api/channel-post-messages)中介绍的图形 API。 


![为自定义 Teams 终结点体验启用聊天功能的过程](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [颁发 Teams 访问令牌](../quickstarts/manage-teams-identity.md)

你可能会对下列文档感兴趣：

- 了解 [Teams 互操作性](./teams-interop.md)
