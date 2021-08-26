---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9c3dd4ecede4e8087b2ec9ad470d5ab415ea6e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801512"
---
按以下代码所示更新 *HttpExample\\\_\_init\_\_.py*，将 `msg` 参数添加到函数定义，并将 `msg.set(name)` 添加到 `if name:` 语句下。

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` 参数是 [`azure.functions.Out class`](/python/api/azure-functions/azure.functions.out) 的实例。 其 `set` 方法将字符串消息写入队列，在本例中，此消息是在 URL 查询字符串中传递给函数的名称。
