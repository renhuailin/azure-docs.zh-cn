---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 156633c04cabd3e97bca4ae6d861b007be8fe84c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446954"
---
使用 Azure CLI [az webpubsub event-handler hub](/cli/azure/webpubsub/event-handler/hub) 命令更新事件处理程序设置：

  > [!Important]
  > 将 &lt;your-unique-resource-name&gt; 替换为在前面的步骤中创建的 Web PubSub 资源的名称。
  > 将 &lt;domain-name&lt; 替换为 ngrok 打印的名称。

```azurecli-interactive
az webpubsub event-handler hub update -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name myHub1 --template url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event-pattern="connected"
```
