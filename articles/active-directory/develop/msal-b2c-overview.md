---
title: 将 MSAL.js 与 Azure AD B2C 配合使用
titleSuffix: Microsoft identity platform
description: 借助适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js)，应用程序可以使用 Azure AD B2C 并获取用于调用受保护 Web API 的令牌。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、其他来源的 Web API 或你自己的 Web API。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 1aa07a4135452ae4303ecfee410b840cc75efffb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729380"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>通过适用于 JavaScript 的 Microsoft 身份验证库来使用 Azure AD B2C

借助[适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js)，JavaScript 应用程序开发人员可以使用 [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C) 通过社交和本地标识对用户进行身份验证。

使用作为标识管理服务的 Azure AD B2C，可以自定义和控制客户的注册和登录方式，并在他们使用你的应用程序时管理其配置文件。 

使用 Azure AD B2C，还可以为应用程序在身份验证过程中显示的 UI 提供品牌标志并进行自定义。

## <a name="supported-app-types-and-scenarios"></a>支持的应用类型和方案

MSAL.js 通过使用[授权代码流和 PKCE](../../active-directory-b2c/authorization-code-flow.md) 授权，使[单页应用程序](../../active-directory-b2c/application-types.md#single-page-applications)能够使用 Azure AD B2C 登录用户。 借助 MSAL.js 和 Azure AD B2C：

- 用户可以通过其社交和本地标识进行身份验证。
- 用户可以获授权访问 Azure AD B2C 的受保护资源（但不包括 Azure AD 的受保护资源）。
- 用户无法使用[委托权限](./v2-permissions-and-consent.md#permission-types)获取 Microsoft API（如 MS 图形 API）的令牌。
- 具有管理员权限的用户可以使用[委托权限](./v2-permissions-and-consent.md#permission-types)获取 Microsoft API（如 MS 图形 API）的令牌。

有关详细信息，请参阅[使用 Azure AD B2C](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/working-with-b2c.md)

## <a name="next-steps"></a>后续步骤

遵循教程，了解如何：

- [在单页应用程序中使用 Azure AD B2C 登录用户](../../active-directory-b2c/configure-authentication-sample-spa-app.md)
- [调用 Azure AD B2C 受保护的 Web API](../../active-directory-b2c/enable-authentication-web-api.md)