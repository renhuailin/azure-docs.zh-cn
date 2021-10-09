---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6f0978a5177a499ccc48eda83c10b327d92b8b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599923"
---
|语言                                 |1.x         |2.x| 3.x | 4.x（预览版） |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| GA (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | 预览版 (.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA（Node 10 和 8）| GA（Node 14、12 和 10） | 预览 (Node 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| GA (.NET Core 3.1) | 预览版 (.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|空值|GA (Java 8)| GA（Java 11 和 8）| 预览版（Java 11 和 8）|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |空值|GA (PowerShell Core 6)| GA（PowerShell 7.0 和 Core 6）| 预览版 (PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|不适用|GA（Python 3.7 和 3.6）| GA（Python 3.9、3.8、3.7 和 3.6）| 预览版（Python 3.9、3.8）|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |不可用|GA| GA | 预览 |

<sup>1</sup> 可以在 .NET Core 2.x 兼容模式下在 .NET Core 3.1 上运行面向运行时版本 2.x 的 .NET 类库应用。 有关详细信息，请参阅 [Functions v2.x 注意事项](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)。  
<sup>2</sup> 转译为 JavaScript 后支持。

若要更详细地了解受支持的语言版本，请参阅语言特定的开发人员指南文章。   
有关语言支持计划更改的信息，请参阅 [Azure 路线图](https://azure.microsoft.com/roadmap/?tag=functions)。
