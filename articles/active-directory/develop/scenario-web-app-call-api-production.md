---
title: 将调用 Web API 的 Web 应用移动到生产 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将调用 Web API 的 Web 应用移到生产环境。
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
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675934"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>调用 Web API 的 Web 应用：移动到生产

现在你已了解如何获取用于调用 Web API 的令牌，下面是将应用程序移到生产环境时需要考虑的一些事项。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

通过试用 ASP.NET Core Web 应用的完整渐进式教程了解更多信息。 本教程：

- 演示如何将用户登录到多个受众或国家云，或使用社交标识登录。
- 调用 Microsoft Graph。
- 调用多个 Microsoft API。
- 处理增量同意。
- 调用自己的 Web API。

[ASP.NET Core Web 应用教程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
