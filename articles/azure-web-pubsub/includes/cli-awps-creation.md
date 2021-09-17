---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 785114c797fba5c84e97f5a0d0cc1a34dc14d540
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781901"
---
使用 Azure CLI [az webpubsub create](/cli/azure/webpubsub#az_webpubsub_create) 命令在上一步的资源组中创建 Web PubSub，并提供以下信息：

- 资源名称：由 3 到 24 个字符组成的字符串，只能包含数字 (0-9)、字母（a-z、A-Z）和连字符 (-)

  > [!Important]
  > 每个 Web PubSub 资源必须具有唯一名称。 在以下示例中，将 &lt;your-unique-resource-name&gt; 替换为 Web PubSub 的名称。

- 资源组名称：myResourceGroup。
- 位置：EastUS。
- Sku：Free_F1

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

> [!Note]
> 由于 Web PubSub 服务仍处于预览状态，你可能已经注意到，CLI 控制台中出现了一条警告，指出“该命令需要扩展 webpubsub。 是否要立即安装它？ 命令将在安装扩展后继续运行。 （是/否）”。 请按 Y 安装扩展。

此命令的输出会显示新建的资源的属性。 请记下下面列出的两个属性：

- **资源名称**：为上面的 `--name` 参数提供的名称。
- **主机名**：在本例中，主机名为 `<your-unique-resource-name>.webpubsub.azure.com/`。

目前，只有你的 Azure 帐户才有权对这个新资源执行任何操作。
