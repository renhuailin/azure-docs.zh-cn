---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: c2f05a81e52edc858bc13bc016e030236241a6f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781899"
---
资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group#az_group_create) 命令在 eastus 位置创建一个名为 myResourceGroup 的资源组 。

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```
