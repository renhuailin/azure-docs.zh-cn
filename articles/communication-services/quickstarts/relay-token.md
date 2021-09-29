---
title: 快速入门 - 访问 TURN 中继
description: 了解如何使用 Azure 通信服务检索 STUN/TURN 令牌
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 7e449fa88a080fd9568436c7806767bac52f1861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677143"
---
# <a name="quickstart-access-turn-relays"></a>快速入门：访问 TURN 中继

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

本快速入门介绍如何检索网络中继令牌以访问 Azure 通信服务 TURN 服务器。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)
- 有效的 Azure 通信服务资源，如果没有，请参阅[创建一个通信服务资源](./create-communication-resource.md)。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务资源，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](./create-communication-resource.md#clean-up-resources)的详细信息。
