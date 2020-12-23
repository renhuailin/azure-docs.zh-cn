---
title: 将单页应用移到生产环境 - Microsoft 标识平台 | Azure
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
ms.openlocfilehash: 0a51442870fb72e2b3cd93d9f03736d2c679ed06
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442814"
---
# <a name="single-page-application-move-to-production"></a>单页应用程序：移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，接下来了解如何移到生产环境。

## <a name="improve-your-app"></a>改进应用

[启用日志记录](msal-logging.md)使应用生产环境准备就绪。

## <a name="test-your-integration"></a>测试集成

按照 [Microsoft 标识平台集成清单](identity-platform-integration-checklist.md)测试你的集成。

## <a name="deploy-your-app"></a>部署你的应用

请查看[部署示例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3)，了解如何使用 Azure 存储和 Azure 应用服务分别部署 SPA 和 Web API 项目。 

## <a name="next-steps"></a>后续步骤

- 深入了解快速入门示例，其中介绍了有关如何使用 **MSAL.js** ： [JavaScript SPA 教程](./tutorial-v2-javascript-spa.md)登录用户和获取访问令牌来调用 **Microsoft Graph API** 的代码。

- 此示例演示如何使用 **MSAL.js** ： [SPA 和 ASP.NET 后端](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)获取自己的后端 web API (ASP.NET Core) 。

- 此示例演示如何使用 **passport azure ad** ： [Node.js web api (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)验证后端 Web API ( # A0) 的访问令牌。

- 此示例演示如何使用 **MSAL.js** 在向注册到 **Azure Active Directory B2C** (Azure AD B2C) ： [SPA 与 Azure AD B2C 一起](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)注册的应用中登录用户。

- 此示例演示了如何使用 **passport azure ad** 来验证 **Azure Active Directory B2C** (Azure AD B2C) 中注册的应用的访问 [令牌Node.js (Azure AD B2C) 。](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
