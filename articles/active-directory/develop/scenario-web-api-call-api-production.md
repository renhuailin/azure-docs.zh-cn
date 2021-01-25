---
title: 将用于调用 web Api 的 web API 移动到生产环境 |Microsoft
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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753345"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>调用 Web API 的 Web API：移到生产环境

获得调用 Web API 的令牌后，就可以将应用移到生产环境了。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>了解详细信息

既然你已了解了如何从自己的 Web API 调用 Web API 的基础知识，你可能会对下一个教程感兴趣，它介绍了用于构建调用 Web API 的受保护 Web API 的代码。

| 示例 | 平台 | 说明 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 第 1 章 | ASP.NET Core Web API、桌面 (WPF) | ASP.NET Core web API 调用 Microsoft Graph，你可以使用 Microsoft 标识平台从 WPF 应用程序调用它。 |
