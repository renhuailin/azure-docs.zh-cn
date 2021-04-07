---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010435"
---
安装绑定扩展的最简单方法是启用[扩展捆绑包](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 启用捆绑包时，会自动安装一组预定义的扩展包。

若要启用扩展捆绑包，请打开 host.json 文件并更新其内容以匹配以下代码：

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```