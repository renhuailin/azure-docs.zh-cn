---
author: baanders
description: Azure 数字孪生的包含文件 - 设置 Cloud Shell 和 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105047"
---
要在打开的 [Azure Cloud Shell](https://shell.azure.com) 窗口中开始处理 Azure 数字孪生，首先需要登录，并为此会话将 shell 上下文设置为你的订阅。 在 Cloud Shell 中运行以下命令：

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> 在上面的命令中，还可以使用订阅名称而不使用 ID。 

如果这是你首次将此订阅与 Azure 数字孪生一起使用，请运行该命令以向 Azure 数字孪生命名空间进行注册。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下来，将[适用于 Azure CLI 的 Microsoft Azure IoT 扩展](/cli/azure/service-page/azure%20iot)添加到 Cloud Shell 以支持用于与 Azure 数字孪生和其他 IoT 服务进行交互的命令。 运行此命令以确保你已安装最新版本的扩展：

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

现在 Azure 数字孪生已可在 Cloud Shell 中使用。

若要验证 Azure 数字孪生在 Cloud Shell 中的就绪性，可以随时运行 `az dt -h` 查看可用的顶层 Azure 数字孪生命令列表。