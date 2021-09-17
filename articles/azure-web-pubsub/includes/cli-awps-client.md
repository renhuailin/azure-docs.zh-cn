---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1c9875f6c3c6b69098c915d4257ed05e3ca0abc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783366"
---
### <a name="connect-to-the-service"></a>连接到服务

[!INCLUDE [az webpubsub client](cli-awps-client-connect.md)]

体验一下，然后尝试使用 `joingroup <group-name>` 加入群组并使用 `sendtogroup <group-name>` 向群组发送消息：

```azurecli
joingroup group1
```

```azurecli
sendtogroup group1 hello
```

[!INCLUDE [publish messages](cli-awps-service.md)]
