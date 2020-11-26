---
title: 支持你开发的移动应用中的单一登录和应用保护策略 |Microsoft
titleSuffix: Microsoft identity platform
description: 介绍如何使用 Microsoft 标识平台构建支持单一登录和应用保护策略的移动应用程序并将其与 Azure Active Directory 集成。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 59924c1f876d08aa504f19c5d6c86dca32fbd1e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173458"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>支持你开发的移动应用中的单一登录和应用保护策略

单一登录 (SSO) 是 Microsoft 标识平台和 Azure Active Directory 的主要产品，为你的应用程序的用户提供了简单且安全的登录。 此外，应用保护策略 (应用) 支持使用户数据安全的关键安全策略。 这些功能共同实现了用户登录和应用程序数据的管理。

本文介绍了为什么 SSO 和应用非常重要，并提供了用于构建支持这些功能的移动应用程序的高级指南。 这适用于手机和平板电脑应用。 如果你是 IT 管理员，想要在组织的 Azure Active Directory 租户中部署 SSO，请查看 [有关规划单一登录部署的指南](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>关于单一登录和应用保护策略

[单一登录 (SSO) ](../manage-apps/plan-sso-deployment.md) 使用户能够登录一次并访问其他应用程序，而无需重新输入凭据。 这样可以更轻松地访问应用，而无需用户记住长列表的用户名和密码。 在应用程序中实现此功能可以更轻松地访问和使用应用。

此外，在应用程序中启用单一登录可解锁新式身份验证附带的新身份验证机制，如 [无密码登录名](../authentication/concept-authentication-passwordless.md)。 用户名和密码是针对应用程序的最常见的攻击媒介之一，启用 SSO 使你可以通过强制实施条件访问或无密码登录名来缓解这一风险，这些登录名添加了额外的安全性或依赖更安全的身份验证机制。 最后，启用单一登录还可以启用 [单一](v2-protocols-oidc.md#single-sign-out)注销。这对于将在共享设备上使用的工作应用程序之类的情况非常有用。

应用[保护策略 (应用) ](/mem/intune/apps/app-protection-policy)确保组织的数据保持安全和包含。 它们允许公司在应用中管理和保护他们的数据，并允许控制谁可以访问应用程序及其数据。 通过实现应用保护策略，你的应用可以将用户连接到受条件访问策略保护的资源，并与其他受保护的应用安全地传输数据。 应用保护策略解除锁定的方案包括：需要使用 PIN 来打开应用、控制应用之间的数据共享以及阻止将公司应用数据保存到个人存储位置。

## <a name="implementing-single-sign-on"></a>实现单一登录

建议使用以下项来使应用程序能够利用单一登录。

### <a name="use-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL) 

在应用程序中实现单一登录的最佳选择是使用 [Microsoft 身份验证库 (MSAL) ](msal-overview.md)。 通过使用 MSAL，你可以通过最少的代码和 API 调用向应用添加身份验证，获取 [Microsoft 标识平台](./index.yml)的全部功能，并让 Microsoft 处理安全身份验证解决方案的维护。 默认情况下，MSAL 会为应用程序添加 SSO 支持。 此外，如果还计划实施应用保护策略，则需要使用 MSAL。

> [!NOTE]
> 可以将 MSAL 配置为使用嵌入的 web 视图。 这会阻止单一登录。 使用默认行为 (即，系统 web 浏览器) 以确保 SSO 正常工作。

如果你当前使用的是应用程序中的 [ADAL 库](../azuread-dev/active-directory-authentication-libraries.md) ，我们强烈建议你将 [其迁移到 MSAL](msal-migration.md)，因为 [ADAL 将被弃用](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)。

对于 iOS 应用程序，我们提供了一个 [快速入门](quickstart-v2-ios.md) ，其中展示了如何使用 MSAL 设置登录，并提供了 [针对各种 SSO 方案配置 MSAL 的指南](single-sign-on-macos-ios.md)。

对于 Android 应用程序，我们提供了一个 [快速入门](quickstart-v2-android.md) ，其中展示了如何使用 MSAL 设置登录，并指导 [如何使用 MSAL 在 Android 上启用跨应用 SSO](msal-android-single-sign-on.md)。

### <a name="use-the-system-web-browser"></a>使用系统 web 浏览器

完成交互式身份验证需要使用 Web 浏览器。 对于使用 MSAL 以外的新式身份验证库的移动应用 (即) 的其他 OpenID Connect 或 SAML 库，或者如果你实现自己的身份验证代码，你应使用系统浏览器作为身份验证界面来启用 SSO。

Google 在 Android 应用程序中提供了执行此操作的指南： [Chrome 自定义选项卡-Google Chrome](https://developer.chrome.com/multidevice/android/customtabs)。

Apple 在 iOS 应用程序中提供了执行此操作的指南： [通过 Web 服务对用户进行身份验证 |Apple 开发人员文档](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service)。

> [!TIP]
> [Apple 设备的 sso 插件](apple-sso-plugin.md)允许使用 Intune 在托管设备上使用嵌入式 web 视图的 iOS 应用的 sso。 建议将 MSAL 和系统浏览器作为开发为所有用户启用 SSO 的应用程序的最佳选项，但这样做会允许在某些情况下不可能使用 SSO。

## <a name="enable-app-protection-policies"></a>启用应用保护策略

若要启用应用保护策略，请使用 [ (MSAL) 的 Microsoft 身份验证库 ](msal-overview.md)。 MSAL 是 Microsoft 标识平台的身份验证和授权库，并开发了 Intune SDK 来与它一起工作。

此外，必须使用代理应用进行身份验证。 代理要求该应用提供应用程序和设备信息，以确保应用程序符合性。 iOS 用户将使用 [Microsoft Authenticator 应用程序](../user-help/user-help-auth-app-sign-in.md) ，Android 用户将使用 Microsoft Authenticator 应用或 [公司门户应用程序](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 进行 [中转身份验证](./msal-android-single-sign-on.md)。 默认情况下，MSAL 使用 broker 作为其第一次选择来完成身份验证请求，因此，在使用 MSAL 时，将自动为应用启用使用代理进行身份验证。

最后， [将 INTUNE SDK 添加](/mem/intune/developer/app-sdk-get-started) 到应用以启用应用保护策略。 大多数情况下，SDK 都遵循截取模式，会自动应用应用保护策略，以确定是否允许应用执行的操作。 此外，还可以手动调用 Api 来告知应用是否对某些操作有限制。

## <a name="additional-resources"></a>其他资源

- [规划 Azure Active Directory 单一登录部署](../manage-apps/plan-sso-deployment.md)
- [如何：在 macOS 和 iOS 上配置 SSO](single-sign-on-macos-ios.md)
- [适用于 Apple 设备的 Microsoft 企业 SSO 插件（预览版）](apple-sso-plugin.md)
- [Android 中的中介身份验证](./msal-android-single-sign-on.md)
- [授权代理以及如何启用它们](./msal-android-single-sign-on.md)
- [Microsoft Intune App SDK 入门](/mem/intune/developer/app-sdk-get-started)
- [配置 Intune App SDK 设置](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [受 Microsoft Intune 保护的应用](/mem/intune/apps/apps-supported-intune-apps)