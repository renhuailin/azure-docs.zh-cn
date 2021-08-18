---
title: Azure 中继混合连接 - 使用 Node 的 HTTP 请求
description: 使用 Node 为 Azure 中继混合连接 HTTP 请求编写 Node.js 控制台应用程序。
ms.topic: conceptual
ms.date: 06/23/2021
ms.custom: devx-track-js
ms.openlocfilehash: bead32c9f32c09bfdcabe8fb5692734a556c7436
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667444"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>开始在 Node 中使用中继混合连接 HTTP 请求

[!INCLUDE [relay-selector-hybrid-connections](./includes/relay-selector-hybrid-connections.md)]

在本快速入门中，请创建 Node.js 发送者和接收者应用程序，用于通过 HTTP 协议发送和接收消息。 这些应用程序使用 Azure 中继的混合连接功能。 若要了解 Azure 中继的常规信息，请参阅 [Azure 中继](relay-what-is-it.md)。 

在本快速入门中，你将执行以下步骤：

1. 使用 Azure 门户创建中继命名空间。
2. 使用 Azure 门户在该命名空间中创建混合连接。
3. 编写服务器（侦听器）控制台应用程序，用于接收消息。
4. 编写客户端（发送方）控制台应用程序，用于发送消息。
5. 运行应用程序。

## <a name="prerequisites"></a>先决条件
- [Node.js](https://nodejs.org/en/)。
- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-namespace-using-the-azure-portal"></a>使用 Azure 门户创建命名空间
[!INCLUDE [relay-create-namespace-portal](./includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>使用 Azure 门户创建混合连接
[!INCLUDE [relay-create-hybrid-connection-portal](./includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>创建服务器应用程序（侦听程序）
若要侦听和接收来自中继的消息，请编写 Node.js 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-node-get-started-server](./includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>创建客户端应用程序（发送程序）

若要将消息发送到中继，可使用任何 HTTP 客户端，或编写 Node.js 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-node-get-started-client](./includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>运行应用程序

1. 运行服务器应用程序：在 Node.js 命令提示符处，键入 `node listener.js`。
2. 运行客户端应用程序：在 Node.js 命令提示符处键入 `node sender.js`，然后输入某些文本。
3. 确保服务器应用程序控制台输出了客户端应用程序中输入的文本。

祝贺你，现已使用 Node.js 创建端到端混合连接应用程序！

## <a name="next-steps"></a>后续步骤
在本快速入门中，你创建了 Node.js 客户端和服务器应用程序，此类程序使用 HTTP 来发送和接收消息。 Azure 中继的混合连接功能也支持使用 WebSocket 发送和接收消息。 若要了解如何将 WebSocket 与 Azure 中继混合连接配合使用，请参阅 [WebSocket 快速入门](relay-hybrid-connections-node-get-started.md)。

在本快速入门中，你使用了 Node.js 来创建客户端和服务器应用程序。 若要了解如何使用 .NET Framework 编写客户端和服务器应用程序，请参阅 [.NET WebSocket 快速入门](relay-hybrid-connections-dotnet-get-started.md)或 [.NET HTTP 快速入门](relay-hybrid-connections-http-requests-dotnet-get-started.md)。
