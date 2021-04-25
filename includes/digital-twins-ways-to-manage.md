---
author: baanders
description: 用于 Azure 数字孪生的 include 文件 - 管理实例的方式
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303742"
---
本文重点介绍如何使用 [Azure 数字孪生 .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/management)完成不同的管理操作。 还可使用[如何：使用 Azure 数字孪生 API 和 SDK](../articles/digital-twins/how-to-use-apis-sdks.md) 中所述的其他语言 SDK 创建这些管理调用。

> [!TIP] 
> 请记住，所有 SDK 方法都提供同步版本和异步版本。 对于分页调用，异步方法会返回 `AsyncPageable<T>`，而同步版本会返回 `Pageable<T>`。

另一种管理方法是通过 Postman 等 REST 客户端为此主题区域直接调用 Azure 数字孪生 [REST API](/rest/api/azure-digitaltwins/)。 如需有关如何执行此操作的说明，请查看[如何：使用 Postman 发出请求](../articles/digital-twins/how-to-use-postman.md)。

最后，也可使用 Azure 数字孪生 CLI 完成这些管理操作。 若要详细了解如何使用 CLI，请查看[如何：使用 Azure 数字孪生 CLI](../articles/digital-twins/how-to-use-cli.md)。