---
title: 将登录用户的 web 应用移到生产 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建用于登录用户的 Web 应用（移到生产环境）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e4a47112d2f66edc8af9b7f100d48bc205f2e85e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584291"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>用于登录用户的 Web 应用：移到生产环境

现在，你已了解如何获取令牌来调用 web Api，以下是将应用程序移动到生产环境时要考虑的一些事项。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>故障排除
用户首次登录到 Web 应用程序时，他们将需要同意。 但是，在某些组织中，用户可以看到如下所示的消息： *AppName 需要访问你组织中只有管理员才能授予的资源的权限。请让管理员向此应用授予权限，然后才能使用该应用。*
这是因为租户管理员已禁用让用户同意的功能。 在这种情况下，请与你的租户管理员联系，以便他们对应用程序所需的范围进行管理员同意。

## <a name="same-site"></a>同一站点

请确保了解 Chrome 浏览器的新版本可能存在的问题：[如何在 Chrome 浏览器中处理 SameSite Cookie 更改](howto-handle-samesite-cookie-changes-chrome-browser.md)。

Microsoft.Identity.Web NuGet 包处理最常见的 SameSite 问题。

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深入探讨：ASP.NET Core Web 应用教程

了解如何在此 ASP.NET Core 教程中通过其他方法登录用户： 

[允许 Web 应用通过 Microsoft 面向开发人员的标识平台登录用户和调用 API](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

此循序渐进教程提供了 Web 应用的生产就绪代码，包括如何在以下组织中添加使用帐户进行的登录：

- 你的组织
- 多个组织
- 工作或学校帐户或个人 Microsoft 帐户
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- 国家云

## <a name="sample-code-java-web-app"></a>示例代码：Java Web 应用

通过 GitHub 上的此示例详细了解 Java Web 应用： 

[一个 Java Web 应用程序，该应用程序使用 Microsoft 标识平台登录用户并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>后续步骤

Web 应用登录用户后，它就可以代表已登录用户调用 Web API。 从 Web 应用调用 Web API 是以下方案的目标：[调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)。