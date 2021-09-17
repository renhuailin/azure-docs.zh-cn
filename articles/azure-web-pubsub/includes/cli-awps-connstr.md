---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 4bfab858862bfb08cfaa2e9dc685b8ade7c0325e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781900"
---
> [!Important]
> 连接字符串包括应用程序访问 Azure Web PubSub 服务所需的授权信息。 连接字符串中的访问密钥类似于服务的根密码。 请始终小心保护访问密钥。 使用 Azure 密钥保管库安全地管理和轮换密钥。 避免将访问密钥分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他人可以访问的任何位置。 如果你认为访问密钥可能已泄露，请轮换密钥。

使用 Azure CLI [az webpubsub key](/cli/azure/webpubsub#az_webpubsub_key) 命令获取服务的 ConnectionString。

```azurecli-interactive
az webpubsub key show --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --query primaryConnectionString
```
