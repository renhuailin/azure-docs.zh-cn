---
title: 将可使用户登录的 Web 应用移到生产环境 | Azure
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
ms.openlocfilehash: f670af1fca4b4638988e53075f092ca1bbac55b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578254"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>用于登录用户的 Web 应用：移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，下面是将应用程序移到生产环境时需要考虑的一些事项。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>故障排除
用户首次登录到 Web 应用程序时，他们将需要同意。 但在某些组织中，用户可能会看到如下所示的消息：“AppName 需要权限来访问组织中只有管理员才能授权的资源。请先让管理员授予对此应用的权限，然后你才能使用此应用。”
这是因为租户管理员已禁用让用户同意的功能。 在这种情况下，请与租户管理员联系，以便他们针对应用程序要求的范围进行管理员许可。

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

## <a name="tutorial-nodejs-web-app"></a>教程：Node.js web 应用

在本教程中详细了解 Node.js web：

[教程：在 Node.js 和 Express Web 应用中让用户登录](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-nodejs-webapp-msal)

## <a name="sample-code-java-web-app"></a>示例代码：Java Web 应用

通过 GitHub 上的此示例详细了解 Java Web 应用： 

[一个 Java Web 应用程序，该应用程序使用 Microsoft 标识平台登录用户并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>后续步骤

Web 应用登录用户后，它就可以代表已登录用户调用 Web API。 从 Web 应用调用 Web API 是以下方案的目标：[调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)。