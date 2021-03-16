---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431834"
---
|语言                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[预览版 (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA（Node 10 和 8）| GA（Node 12 和 10）<br />预览 (Node 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|空值|GA (Java 8)| GA（Java 11 和 8）|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |空值|GA (PowerShell Core 6)| GA（PowerShell 7 和 Core 6）|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|不适用|GA（Python 3.7 和 3.6）| GA（Python 3.8、3.7 和 3.6） <br/> 预览版 (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |不适用|正式版<sup>2</sup>| 正式版<sup>2</sup> |


<sup>1</sup> 现在可以在 .NET Core 2.x 兼容模式下在 .NET Core 3.1 上运行面向运行时版本 2.x 的 .NET 类库应用。 有关详细信息，请参阅 [Functions v2.x 注意事项](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)。  
<sup>2</sup> 转译为 JavaScript 后支持。

若要更详细地了解受支持的语言版本，请参阅语言特定的开发人员指南文章。   
有关语言支持计划更改的信息，请参阅 [Azure 路线图](https://azure.microsoft.com/roadmap/?tag=functions)。
