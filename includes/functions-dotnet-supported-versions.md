---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: 729275766806bf5fe47d35b04ddc58b11c58217b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909058"
---
## <a name="supported-versions"></a>支持的版本

Functions 运行时版本使用特定版本的 .NET。 若要详细了解 Functions 版本，请参阅 [Azure Functions 运行时版本概述](../articles/azure-functions/functions-versions.md)。 版本支持取决于 Functions 是在进程内运行还是在进程外（独立）运行。 

下表显示了可与特定版本的 Functions 配合使用的 .NET Core 或 .NET Framework 的最高级别。 

| Functions 运行时版本 | 进程内<br/>（[.NET 类库](../articles/azure-functions/functions-dotnet-class-library.md)） | 进程外<br/>（[.NET 独立](../articles/azure-functions/dotnet-isolated-process-guide.md)） |
| ---- | ---- | --- |
| Functions 4.x（预览版） | .NET 6.0（预览版）| .NET 6.0（预览版）<sup>2</sup> |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>1</sup> | 不适用 |
| Functions 1.x | .NET Framework 4.8 | 不适用 |

<sup>1</sup> 有关详细信息，请参阅 [Functions v2.x 注意事项](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)。    
<sup>2</sup> 目前只能使用 Azure Functions Core Tools 创建独立的过程函数。 有关详细信息，请参阅[快速入门：在 Azure 中从命令行创建 C# 函数](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)。  

有关 Azure Functions 版本的最新消息，包括删除较旧的特定次要版本，请关注 [Azure 应用服务公告](https://github.com/Azure/app-service-announcements/issues)。
