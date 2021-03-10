---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244795"
---
让我们创建一个名为 mySecret 的机密，其值为 Success!。 机密可以是密码、SQL 连接字符串，或者需要安全保存的、可供应用程序使用的其他任何信息。 

若要将机密添加到新创建的 Key Vault，请使用 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) 命令：

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```