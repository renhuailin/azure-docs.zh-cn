---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493933"
---
Visual Studio Code 可让你遵照一组方便的提示将绑定添加到 function.json 文件。 

若要添加绑定，请打开命令托盘 (F1) 并键入“Azure Functions: add binding...”，选择新绑定的函数，然后按照提示操作，这些提示因添加到函数的绑定类型而异。 

下面是有关定义新的存储输出绑定的示例提示：

| 提示 | 值 | 说明 |
| -------- | ----- | ----------- |
| **选择绑定方向** | `out` | 该绑定是输出绑定。 |
| **选择具有方向的绑定** | `Azure Queue Storage` | 该绑定是 Azure 存储队列绑定。 |
| **用于在代码中标识此绑定的名称** | `msg` | 用于标识代码中引用的绑定参数的名称。 |
| **要将消息发送到的队列** | `outqueue` | 绑定要写入到的队列的名称。 如果 *queueName* 不存在，首次使用绑定时，它会创建该属性。 |
| **从“local.settings.json”中选择设置** | `MyStorageConnection` | 包含存储帐户连接字符串的应用程序设置的名称。 `AzureWebJobsStorage` 设置包含连同函数应用一起创建的存储帐户的连接字符串。 |

还可以直接右键单击（在 macOS 上，请按住 Ctrl 并单击）function 文件夹中的 function.json 文件，选择“添加绑定”，然后按照相同的提示进行操作 。

在此示例中，以下绑定已添加到 function.json 文件中的 `bindings` 数组：

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```