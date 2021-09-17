---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 2356b3af08577063326da7dbb3cf8141d52cea07
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597191"
---
- 连接：连接（也称为客户端或客户端连接）表示连接到 Web PubSub 服务的单个 WebSocket 连接。 成功连接后，Web PubSub 服务会向此连接分配唯一的连接 ID。

- 中心：中心是客户端连接集的逻辑概念。 通常将一个中心用于一种用途，例如聊天中心或通知中心 。 当客户端连接进行连接时，它会连接到某个中心，并且在其生存期内属于该中心。 不同的应用程序可以使用不同的中心名称共享一个 Azure Web PubSub 服务。

- 组：组是与中心的连接的子集。 组相对轻型，可以随时向组添加客户端连接或者从组中删除客户端连接。 例如，客户端加入聊天室，客户端离开聊天室，此类聊天室可以是一个组。 一个客户端可以加入多个组，一个组可以包含多个客户端。

- 用户：与 Web PubSub 的连接可以属于一个用户。 用户可能具有多个连接，例如当单个用户跨多个设备或多个浏览器选项卡进行连接时。

- 消息：客户端连接后，可以通过 WebSocket 连接将消息发送到上游或是从上游接收消息。
