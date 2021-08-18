---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/17/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cc661942dc1cf110a07df383149b03c8a4ea7409
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321013"
---
特定函数应用中所有函数的代码均位于包含主机配置文件的根项目文件夹中。 [host.json](../articles/azure-functions/functions-host-json.md) 文件包含特定于运行时的配置，并位于函数应用的根文件夹中。 *bin* 文件夹包含函数应用所需的包和其他库文件。 函数应用所需的特定文件夹结构因语言而异：

* [C# 编译 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 脚本 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 脚本](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)

在 2.x 版及更高版本的 Functions 运行时中，函数应用中的所有函数必须共享同一语言堆栈。 
