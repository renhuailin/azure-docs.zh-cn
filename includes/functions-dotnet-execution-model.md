---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: ed981abd0ff849fb2a88164b2814794a48603ce7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559862"
---
本文支持创建两种类型的已编译 C# 函数： 

| 执行模型 | 说明 |
| --- | --- |
| **[进程内](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| 函数代码在与 Functions 主机进程相同的进程中运行。 若要了解详细信息，请参阅[使用 Azure Functions 开发 C# 类库函数](../articles/azure-functions/functions-dotnet-class-library.md)。 |
| **[隔离进程](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| 函数代码在单独的 .NET 工作进程中运行。 若要了解详细信息，请参阅[有关在 Azure 中的 .NET 5.0 上运行函数的指南](../articles/azure-functions/dotnet-isolated-process-guide.md)。 |