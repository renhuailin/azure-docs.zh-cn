---
title: 在开发的移动应用支持单一登录和应用保护策略 | Azure
titleSuffix: Microsoft identity platform
description: 说明并概述如何使用 Microsoft 标识平台构建支持单一登录和应用保护策略的移动应用程序并与 Azure Active Directory 集成。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.custom: has-adal-ref
ms.openlocfilehash: 43f66b7f44e7d30816da5ac59a9692807f257a2b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787256"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>在开发的移动应用支持单一登录和应用保护策略

单一登录 (SSO) 是 Microsoft 标识平台和 Azure Active Directory 的关键产品/服务，可应用的用户提供简单且安全的登录。 此外，借助应用保护策略 (APP) 可支持使用户数据保持安全的关键安全策略。 这些功能共同实现了安全用户登录和应用数据管理。

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

本文说明为何 SSO 和 APP 非常重要，并提供了有关构建支持这些功能的移动应用程序的高级指导。 这适用于手机和平板电脑应用。 如果你是要在组织的 Azure Active Directory 租户中部署 SSO 的 IT 管理员，请查看[有关计划单一登录部署的指导](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>关于单一登录和应用保护策略

[单一登录 (SSO)](../manage-apps/plan-sso-deployment.md) 使用户能够登录一次并获取其他应用程序的访问权限，而无需重新输入凭据。 这样可以更轻松地访问应用，用户无需用户记住长长的用户名和密码列表。 在应用中实现此功能可以更轻松地访问和使用应用。

此外，在应用中启用单一登录可解锁附带新式身份验证的新身份验证机制，如[无密码登录](../authentication/concept-authentication-passwordless.md)。 用户名和密码是针对应用程序的最常见攻击途径之一，启用 SSO 使你可以通过强制实施条件访问或无密码登录（添加了额外安全性或依赖于更安全的身份验证机制）来缓解此风险。 最后，启用单一登录还会启用[单一注销](v2-protocols-oidc.md#single-sign-out)。这对于将在共享设备上使用的工作应用程序之类的情况非常有用。

[应用保护策略 (APP)](/mem/intune/apps/app-protection-policy) 可确保组织的数据保持安全且受控制。 它们使公司可以在应用中管理和保护其数据，并允许控制可以访问应用及其数据的人员。 通过实现应用保护策略，应用可以将用户连接到受条件访问策略保护的资源，并与其他受保护应用安全地来回传输数据。 应用保护策略解锁的方案包括：需要使用 PIN 来打开应用、控制应用之间的数据共享以及阻止将公司应用数据保存到个人存储位置。

## <a name="implementing-single-sign-on"></a>实现单一登录

建议通过以下方法使应用可以利用单一登录。

### <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL)

在应用程序中实现单一登录的最佳选择是使用 [Microsoft 身份验证库 (MSAL)](msal-overview.md)。 使用 MSAL 可以通过最少的代码和 API 调用向应用添加身份验证，获取 [Microsoft 标识平台](./index.yml)的完整功能，并让 Microsoft 处理安全身份验证解决方案的维护。 默认情况下，MSAL 会为应用程序添加 SSO 支持。 此外，如果还计划实现应用保护策略，则必须使用 MSAL。

> [!NOTE]
> 可以将 MSAL 配置为使用嵌入式 Web 视图。 这会阻止单一登录。 使用默认行为（即系统 Web 浏览器）可确保 SSO 正常工作。

如果当前在应用程序中使用 [ADAL 库](../azuread-dev/active-directory-authentication-libraries.md)，则强烈建议[将它迁移到 MSAL](msal-migration.md)，因为 [ADAL 将被弃用](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)。

对于 iOS 应用程序，我们提供了演示如何使用 MSAL 设置登录的[快速入门](quickstart-v2-ios.md)，以及[有关为各种 SSO 方案配置 MSAL 的指导](single-sign-on-macos-ios.md)。

对于 Android 应用程序，我们提供了演示如何使用 MSAL 设置登录的[快速入门](quickstart-v2-android.md)，以及有关[如何使用 MSAL 在 Android 上启用跨应用 SSO](msal-android-single-sign-on.md) 的指导。

### <a name="use-the-system-web-browser"></a>使用系统 Web 浏览器

完成交互式身份验证需要使用 Web 浏览器。 对于使用 MSAL 以外的新式身份验证库（即其他 OpenID Connect 或 SAML 库）的移动应用，或者如果实现自己的身份验证代码，应使用系统浏览器作为身份验证界面来启用 SSO。

Google 为在 Android 应用程序中执行此操作提供了指导：[Chrome 自定义选项卡 - Google Chrome](https://developer.chrome.com/multidevice/android/customtabs)。

Apple 为在 iOS 应用程序中执行此操作提供了指导：[通过 Web 服务对用户进行身份验证 | Apple 开发人员文档](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service)。

> [!TIP]
> [Apple 设备的 SSO 插件](apple-sso-plugin.md)允许将 SSO 用于通过 Intune 在托管设备上使用嵌入式 Web 视图的 iOS 应用。 对于开发为所有用户启用 SSO 的应用程序，建议将 MSAL 和系统浏览器作为最佳选项，不过这样做会允许在某些其他情况下无法使用 SSO 的方案中使用 SSO。

## <a name="enable-app-protection-policies"></a>启用应用保护策略

若要启用应用保护策略，请使用 [Microsoft 身份验证库 (MSAL)](msal-overview.md)。 MSAL 是 Microsoft 标识平台的身份验证和授权库，并开发了 Intune SDK 与它配合工作。

此外，必须使用代理应用进行身份验证。 代理要求应用提供应用程序和设备信息以确保应用合规性。 iOS 用户使用 [Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)，Android 用户使用 Microsoft Authenticator 应用或[公司门户应用](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)进行[中转身份验证](./msal-android-single-sign-on.md)。 默认情况下，MSAL 使用代理作为其完成身份验证请求的首选，因此在使用现成 MSAL 时，会自动为应用启用使用代理进行身份验证。

最后，向应用[添加 Intune SDK](/mem/intune/developer/app-sdk-get-started)以启用应用保护策略。 该 SDK 在很大程度上遵循截获模型，会自动应用应用保护策略以确定是否允许应用所执行的操作。 还可以手动调用一些 API 来告知应用是否对某些操作有限制。

## <a name="additional-resources"></a>其他资源

- [规划 Azure Active Directory 单一登录部署](../manage-apps/plan-sso-deployment.md)
- [操作说明：在 macOS 和 iOS 上配置 SSO](single-sign-on-macos-ios.md)
- [适用于 Apple 设备的 Microsoft 企业 SSO 插件（预览版）](apple-sso-plugin.md)
- [Android 中的中介身份验证](./msal-android-single-sign-on.md)
- [授权代理以及如何启用它们](./msal-android-single-sign-on.md)
- [Microsoft Intune App SDK 入门](/mem/intune/developer/app-sdk-get-started)
- [配置 Intune App SDK 设置](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [受 Microsoft Intune 保护的应用](/mem/intune/apps/apps-supported-intune-apps)