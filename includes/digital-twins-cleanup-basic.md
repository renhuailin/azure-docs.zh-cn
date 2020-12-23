---
author: baanders
description: 包括用于清理基本 Azure 数字孪生实例的文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011235"
---
如果不再需要本教程中创建的资源，请按照以下步骤将其删除。

利用 [Azure Cloud Shell](https://shell.azure.com)，你可以使用 [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 该命令将删除资源组和 Azure 数字孪生实例。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不要意外删除错误的资源组或资源。

打开 Azure Cloud Shell 并运行以下命令，以删除资源组及其包含的所有内容。

```azurecli-interactive
az group delete --name <your-resource-group>
```