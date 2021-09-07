---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 7537ad41aaac6fab1eb591300b561bba4167d85d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829524"
---
# <a name="in-process"></a>[进程内](#tab/in-process)

添加使用 `msg` 输出绑定对象来创建队列消息的代码。 请在方法返回之前添加此代码。

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

此时，你的函数应如下所示：

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

将现有 `HttpExample` 类替换为以下代码：

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="11-32":::

---