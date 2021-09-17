---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 2b12b25b973d9e56de0241c1e088e2409137b086
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783369"
---
### <a name="publish-messages-and-manage-the-clients"></a>发布消息和管理客户端

Azure CLI 还提供 [az webpubsub service](/cli/azure/webpubsub/service) 命令来管理客户端连接。

打开其他 CLI 命令，然后可以将消息广播到客户端：

- 中心名称：myHub1。
- 资源组名称：myResourceGroup。

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1 --payload "Hello World"
```

切换回上一个 CLI 命令，可以看到客户端收到消息：
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

还可以使用 `--help` 选项列出所有可用命令，并播放列出的命令。

```azurecli-interactive
az webpubsub service --help
```
