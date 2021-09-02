---
title: Azure Active Directory 中对基于短信的身份验证的应用支持
description: 了解用户使用 SMS 登录到 Azure Active Directory 时所支持的应用
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: aanjusingh
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5b0ff9c70c84a1f3adda2e93c74ce388ceb322
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228577"
---
# <a name="app-support-for-sms-based-authentication"></a>对基于短信的身份验证的应用支持

基于短信的身份验证适用于与 Microsoft 标识平台集成的 Microsoft 应用 (Azure AD)。 下表列出了一些支持基于短信的身份验证的 Web 应用和移动应用。 如果要添加或验证任何应用，请[联系我们](https://feedback.azure.com/forums/169401-azure-active-directory)。 

| 应用 | Web/浏览器应用 | 本机移动应用 |
| --- |:---:|:---:|
| Office 365 - Microsoft Online Services* | ● |   |
| Microsoft One Note | ● |   |
| Microsoft Teams | ● | ● |
| Microsoft Intune/公司门户 | ● | ● |
| “我的应用”门户 | ● |不可用|
| Microsoft Forms | ● |不可用|
| Microsoft Edge | ● |   |
| Microsoft Power BI | ● |   |
| Microsoft Stream | ● |   |
| Microsoft Power Apps | ● |   |
| Microsoft Azure | ● | ● |
| Azure 虚拟桌面 | ● |  | 

*_当直接在 Web 上进行访问时，短信登录不适用于 Office 应用程序（如 Word、Excel 等），但当通过 [Office 365 Web 应用](https://www.office.com)_ 进行访问时适用

上面提到的 Microsoft 应用支持短信登录，因为它们使用 Microsoft 标识登录 (`https://login.microsoftonline.com/`)，这允许用户输入电话号码和短信代码。

## <a name="unsupported-microsoft-apps"></a>不受支持的 Microsoft 应用

直接在 Web 上进行访问的 Microsoft 365 桌面（Windows 或 Mac）应用和 Microsoft 365 Web 应用（MS One Note 除外）不支持短信登录。 这些应用使用需要密码才能登录的 Microsoft Office 登录 (`https://office.live.com/start/*`)。
出于同一原因，Microsoft Office 移动应用（Microsoft Teams、Intune 公司门户和 Microsoft Azure 除外）不支持短信登录。

| 不受支持的 Microsoft 应用| 示例 |
| --- | --- |
| 本机桌面 Microsoft 应用 | Microsoft Teams、O365 应用、Word、Excel 等。|
| 本机移动 Microsoft 应用（Microsoft Teams、Intune 公司门户和 Microsoft Azure 除外） | Outlook、Edge、Power BI、Stream、SharePoint、Power Apps、Word 等。|
| Microsoft 365 Web 应用（直接在 Web 上访问） | [Outlook](https://outlook.live.com/owa/)、[Word](https://office.live.com/start/Word.aspx)、[Excel](https://office.live.com/start/Excel.aspx)、[PowerPoint](https://office.live.com/start/PowerPoint.aspx)、[OneDrive](https://onedrive.live.com/about/signin)|  

## <a name="support-for-non-microsoft-apps"></a>支持非 Microsoft 应用 

若要使非 Micorosoft 应用与短信登录功能兼容，请执行以下操作： 
- 将非 Microsoft Web 应用与 Azure AD 集成并使用 Azure AD 身份验证。 使用安全断言标记语言 [SAML](../manage-apps/add-application-portal-setup-sso.md) 或 Open ID Connect [OIDC](../manage-apps/add-application-portal-setup-oidc-sso.md) 与 Azure AD SSO 集成。 
- 使用 [Azure AD 应用程序代理](../app-proxy/application-proxy-add-on-premises-application.md)将非 Microsoft 本地应用与 Azure AD 集成
- 将非 Microsoft 客户端应用与 [Microsoft 标识平台](../develop/v2-overview.md)集成以进行身份验证 
    - [示例应用 iOS](../develop/tutorial-v2-ios.md)
    - [示例应用 Android](../develop/tutorial-v2-android.md)

## <a name="next-steps"></a>后续步骤

- [如何为用户启用基于短信的登录](howto-authentication-sms-signin.md)
- 请参阅以下链接，使用 MSAL 库为本机移动应用启用短信登录： 
  - [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)
  - [Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [将 SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)

## <a name="recommended-content"></a>建议的内容

- [向 Azure Active Directory 添加应用程序](../manage-apps/add-application-portal.md)
- [从 Microsoft 标识平台获取用于对用户进行身份验证的令牌的 MSAL 库概述](../develop/msal-overview.md)
- [通过 Azure AD 配置 Microsoft 托管的主屏幕](/mem/intune/apps/app-configuration-managed-home-screen-app)
