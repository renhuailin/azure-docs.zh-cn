---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94884847"
---
<!-- List and set subscriptions -->

1. 使用 [az account list](/cli/azure/account#az-account-list) 命令获取订阅列表。

    ```
    az account list --output table
    ```

2. 结合你要切换到的订阅 ID 或名称使用 `az account set`。

    ```
    az account set --subscription "My Demos"
    ```
