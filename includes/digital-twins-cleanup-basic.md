---
author: baanders
description: 用于清除 Azure 数字孪生实例的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102234370"
---
* 如果不再需要在本教程中创建的任何资源，可使用 [az group delete](/cli/azure/group#az-group-delete) 命令从本文中删除 Azure 数字孪生实例及其他所有资源。 这会删除资源组中的所有 Azure 资源及资源组本身。
    
    > [!IMPORTANT]
    > 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不要意外删除错误的资源组或资源。
    
    打开 [Azure Cloud Shell](https://shell.azure.com) 并运行以下命令，以删除资源组及其包含的所有内容。
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```