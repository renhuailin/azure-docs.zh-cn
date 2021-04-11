---
title: 将调用 Web API 的 Web API 移动到生产环境 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将调用 Web API 的 Web API 移到生产环境。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675866"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>调用 Web API 的 Web API：移到生产环境

获取调用 Web API 的令牌后，将应用程序移动到生产环境时，需要考虑以下事项。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

既然你已了解了如何从自己的 Web API 调用 Web API 的基础知识，你可能会对下一个教程感兴趣，它介绍了用于构建调用 Web API 的受保护 Web API 的代码。

| 示例 | 平台 | 说明 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 第 1 章 | ASP.NET Core Web API、桌面 (WPF) | ASP.NET Core Web API 会调用你使用 Microsoft 标识平台从 WPF 应用程序调用的 Microsoft Graph。 |
