---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 4f2aa82388882c192213b168faedd4f61069ae64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829523"
---
在 C# 项目中，绑定被定义为函数方法上的绑定属性。 具体定义取决于应用是在进程内（C# 类库）还是在隔离进程中运行。  

# <a name="in-process"></a>[进程内](#tab/in-process)

打开 HttpExample.cs 项目文件，并将以下参数添加到 `Run` 方法定义中：

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` 参数为 `ICollector<T>` 类型，表示函数完成时写入输出绑定的消息集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `StorageAccountAttribute` 设置。 此属性指示包含存储帐户连接字符串的设置，可以在类、方法或参数级别应用。 在这种情况下，可以省略 `StorageAccountAttribute`，因为你已使用默认存储帐户。

Run 方法定义如下所示：  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

打开 HttpExample.cs 项目文件并添加以下 `MultiResponse` 类：

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

`MultiResponse` 类允许写入名为 `outqueue` 的存储队列和 HTTP 成功消息。 由于 `QueueOutput` 属性应用于字符串数组，因此可以将多条消息发送到队列。 

存储帐户的连接字符串由 `Connection` 属性设置。 在这种情况下，可以省略 `Connection`，因为你已使用默认存储帐户。

---