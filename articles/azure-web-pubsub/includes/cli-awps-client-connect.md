---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1f6356ceaa844433e6da3102b8db0fcbd5a61615
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781898"
---
使用 Azure CLI [az webpubsub client](/cli/azure/webpubsub/client) 命令启动与上一步创建的服务的 WebSocket 客户端连接，并提供以下信息：

- 中心名称：1 到 127 个字符组成的字符串。 应以字母字符 `(a-z, A-Z)` 开头，并且仅包含字母数字 `(0-9, a-z, A-Z)` 字符或下划线 `(_)`。

中心是已连接的 WebSocket 连接的逻辑集。 有关概念的详细信息，请查看[关于中心、组和连接](../key-concepts.md)。

  > [!Important]
  > 将 &lt;your-unique-resource-name&gt; 替换为在前面的步骤中创建的 Web PubSub 资源的名称。

- 中心名称：myHub1。
- 资源组名称：myResourceGroup。

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1
```

可以看到该命令建立了与 Web PubSub 服务的 WebSocket 连接，并且你收到了一条 JSON 消息，指示它现在已成功连接，并分配有唯一的 `connectionId`：

```json
{"type":"system","event":"connected","userId":null,"connectionId":"<your_unique_connection_id>"}
```
