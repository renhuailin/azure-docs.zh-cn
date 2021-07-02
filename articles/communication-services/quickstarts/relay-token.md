---
title: 快速入门 - 获取网络中继令牌
description: 了解如何使用 Azure 通信服务检索 STUN/TURN 令牌
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 05/21/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 2fc0bfeaef1d3e0e49d1597f41582b6d79d9a3db
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025917"
---
# <a name="quickstart-get-a-network-relay-token"></a>快速入门：获取网络中继令牌

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

本快速入门介绍如何检索网络中继令牌以访问 Azure 通信服务 TURN 服务器

## <a name="prerequisites"></a>先决条件

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
