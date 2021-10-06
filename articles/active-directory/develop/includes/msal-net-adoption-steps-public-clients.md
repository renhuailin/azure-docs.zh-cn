---
title: 公共客户端迁移到 MSAL 的常见步骤
description: 包含文件，说明从 ADAL 迁移到 MSAL 时所有公共客户端应用需要采取的常见步骤。
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 09/08/2021
ms.author: sahmalik
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 419b038f89df0d7fb5185fe2ef8ae43c76db7f7f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837675"
---
以下用于更新代码的步骤适用于所有机密客户端方案：

1. 在源代码中使用 MSAL.NET 命名空间：`using Microsoft.Identity.Client;`。
2. 使用 `PublicClientApplicationBuilder.Create` 实例化 `IPublicClientApplication`，而不是实例化 `AuthenticationContext`。
3. MSAL.NET 使用作用域，而不是字符串 `resourceId`。 由于使用 ADAL.NET 的应用程序是预先授权的，因此可以始终使用以下作用域：`new string[] { $"{resourceId}/.default" }`。
4. 将对 `AuthenticationContext.AcquireTokenAsync` 的调用替换为对 `IPublicClientApplication.AcquireTokenXXX` 的调用，其中 XXX 取决于你的方案。