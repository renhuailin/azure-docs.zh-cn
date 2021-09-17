---
title: 快速入门 - 通过 Azure CLI 创建 Web PubSub 实例
description: 快速入门展示如何通过 Azure CLI 创建 Web PubSub 实例
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 08/06/2021
ms.openlocfilehash: ebe8f22c5a77da0a09b69926e6a4b881b823a513
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444270"
---
# <a name="quickstart-create-a-web-pubsub-instance-with-the-azure-cli"></a>快速入门：通过 Azure CLI 创建 Web PubSub 实例

[Azure 命令行接口 (Azure CLI)](/cli/azure) 是一组用来创建和管理 Azure 资源的命令。 Azure CLI 可用于各种 Azure 服务，可用来快速使用 Azure（侧重于自动化）。 本快速入门向你展示通过 Azure CLI 创建 Azure Web PubSub 实例的选项。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本快速入门需要 Azure CLI 版本 2.22.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>创建 Web PubSub 实例

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="try-the-newly-created-instance"></a>试用新创建的实例

> [!div class="nextstepaction"]
> [通过 CLI 来试用新创建的实例](./quickstart-cli-try.md#play-with-the-instance)

> [!div class="nextstepaction"]
> [从浏览器中试用新创建的实例](./quickstart-live-demo.md#try-the-instance-with-an-online-demo)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean up resource](includes/cli-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在现实应用程序中，你可以使用各种语言的 SDK 来生成自己的应用程序。 我们还提供了函数扩展，供你轻松生成无服务器应用程序。

[!INCLUDE [next step](includes/include-next-step.md)]
