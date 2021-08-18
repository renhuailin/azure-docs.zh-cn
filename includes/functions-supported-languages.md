---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 76bfd886b471e5e4e2c3fa8a6733dcb67e7ea77d
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493219"
---
|语言                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)<sup>1</sup>|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>2</sup>)| GA (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA（Node 10 和 8）| GA（Node 14、12 和 10） |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>2</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|空值|GA (Java 8)| GA（Java 11 和 8）|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |空值|GA (PowerShell Core 6)| GA（PowerShell 7.0 和 Core 6）|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|不适用|GA（Python 3.7 和 3.6）| GA（Python 3.8、3.7 和 3.6） <br/> 预览版 (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |不适用|GA<sup>3</sup>| GA<sup>3</sup> |

<sup>1</sup> 提供 Azure Functions 的试验版本，以便你使用 .NET 6.0 预览版。 若要了解详细信息，请参阅 [Azure Functions v4 早期预览版](https://aka.ms/functions-dotnet6earlypreview-wiki) 页面。
<sup>2</sup> 可以在 .NET Core 2.x 兼容模式下在 .NET Core 3.1 上运行面向运行时版本 2.x 的 .NET 类库应用。 有关详细信息，请参阅 [Functions v2.x 注意事项](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)。  
<sup>3</sup> 转译为 JavaScript 后支持。

若要更详细地了解受支持的语言版本，请参阅语言特定的开发人员指南文章。   
有关语言支持计划更改的信息，请参阅 [Azure 路线图](https://azure.microsoft.com/roadmap/?tag=functions)。
