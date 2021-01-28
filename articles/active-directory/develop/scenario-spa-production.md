---
title: 将单页面应用移至生产环境
titleSuffix: Microsoft identity platform
description: 了解如何生成单页应用程序（移到生产环境）
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954948"
---
# <a name="single-page-application-move-to-production"></a>单页应用程序：移到生产环境

现在，你已了解如何获取令牌来调用 web Api，以下是将应用程序移动到生产环境时要考虑的一些事项。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>部署你的应用

请查看[部署示例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3)，了解如何使用 Azure 存储和 Azure 应用服务分别部署 SPA 和 Web API 项目。 

## <a name="code-samples"></a>代码示例

这些代码示例演示了单页面应用的几个关键操作。
- [使用 ASP.NET 后端的 SPA](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)：如何使用 **MSAL.js** 获取自己的后端 web API (ASP.NET Core) 的令牌。

- [Node.js WEB api (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)：如何使用 **passport Azure AD** 验证后端 Web API 的访问令牌 ( # A1) 。

- [SPA 与 Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)：如何使用 **MSAL.js** 在注册到 **Azure Active Directory B2C** (Azure AD B2C) 的应用中登录用户。

- [Node.js WEB API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)：如何使用 **passport Azure AD** 验证使用 **Azure Active Directory B2C** (Azure AD B2C) 注册的应用的访问令牌。

## <a name="next-steps"></a>后续步骤

- [JAVASCRIPT SPA 教程](./tutorial-v2-javascript-spa.md)：深入了解如何使用 **MSAL.js** 登录用户并获取访问令牌来调用 **Microsoft Graph API** 。