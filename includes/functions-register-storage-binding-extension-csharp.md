---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/16/2021
ms.author: glenga
ms.openlocfilehash: bda70e418b86c44750e9c826144fce8bbc027cbb
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830488"
---
::: zone pivot="programming-language-csharp"  

除了 HTTP 和计时器触发器，绑定将实现为扩展包。 在终端窗口中运行以下 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令，将存储扩展包添加到项目中。

# <a name="in-process"></a>[进程内](#tab/in-process) 
```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage 
```
# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)
```bash
dotnet add package Microsoft.Azure.Functions.Worker.Extensions.Storage.Queues --prerelease
```
---
现在，你可以将存储输出绑定添加到项目。  
::: zone-end  
