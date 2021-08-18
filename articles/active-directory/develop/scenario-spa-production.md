---
title: 将单页应用移到生产环境
titleSuffix: Microsoft identity platform
description: 了解如何生成单页应用程序（移到生产环境）
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4f4821dc48650c14b6d9736a7238dc44c500c259
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357546"
---
# <a name="single-page-application-move-to-production"></a>单页应用程序：移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，下面是将应用程序移到生产环境时需要考虑的一些事项。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>部署你的应用

请查看[部署示例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3)，了解如何使用 Azure 存储和 Azure 应用服务分别部署 SPA 和 Web API 项目。

## <a name="code-samples"></a>代码示例

这些代码示例演示了单页应用的几个关键操作。
- [使用 ASP.NET 后端的 SPA](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)：如何使用 MSAL.js 获取自己的后端 Web API (ASP.NET Core) 的令牌。

- [Node.js Web API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)：如何使用 passport-azure-ad 验证后端 Web API (Node.js) 的访问令牌。

- [使用 Azure AD B2C 的 SPA](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)：如何使用 MSAL.js 在向 Azure Active Directory B2C (Azure AD B2C) 注册的应用中登录用户 。

- [Node.js web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)：如何使用 passport-azure-ad 为已向 Azure Active Directory B2C (Azure AD B2C) 注册的应用验证访问令牌 。

## <a name="next-steps"></a>后续步骤

- [JavaScript SPA 教程](./tutorial-v2-javascript-auth-code.md)：深入了解如何使用 MSAL.js 登录用户并获取访问令牌来调用 Microsoft Graph API 。