---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424599"
---
运行以下命令以查看准实时[流式处理日志](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)：

```console
func azure functionapp logstream <APP_NAME> 
```

在单独的终端窗口或浏览器中，再次调用远程函数。 终端中显示了 Azure 中函数执行的详细日志。 