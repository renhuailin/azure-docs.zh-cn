---
title: 快速入门 - 连接并播放 Azure Web PubSub 实例
description: 介绍如何从 Azure CLI 播放实例的快速入门
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 08/06/2021
ms.openlocfilehash: ac81c3422036abafc5e2d034e06e81e1f35d2e02
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445381"
---
# <a name="quickstart-connect-to-the-azure-web-pubsub-instance-from-cli"></a>快速入门：从 CLI 连接到 Azure Web PubSub 实例

本快速入门演示如何使用 [Azure 命令行接口 (Azure CLI)](/cli/azure) 连接到 Azure Web PubSub 实例并将消息发布到连接的客户端。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本快速入门需要 Azure CLI 版本 2.22.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>创建 Web PubSub 实例

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="play-with-the-instance"></a>播放实例

[!INCLUDE [Try the Web PubSub instance](includes/cli-awps-client.md)]

## <a name="next-steps"></a>后续步骤

本快速入门提供有关如何连接到 Web PubSub 服务以及如何将消息发布到连接的客户端的基本想法。

在现实应用程序中，你可以使用各种语言的 SDK 来生成自己的应用程序。 我们还提供了函数扩展，供你轻松生成无服务器应用程序。

[!INCLUDE [next step](includes/include-next-step.md)]
