---
author: baanders
description: 用于清除 Azure 数字孪生实例的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799786"
---
* 如果不再需要在本教程中创建的任何资源，可使用 [az group delete](/cli/azure/group#az_group_delete) 命令从本文中删除 Azure 数字孪生实例及其他所有资源。 这会删除资源组中的所有 Azure 资源及资源组本身。
    
    > [!IMPORTANT]
    > 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不要意外删除错误的资源组或资源。
    
    打开 [Azure Cloud Shell](https://shell.azure.com) 并运行以下命令，以删除资源组及其包含的所有内容。
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```