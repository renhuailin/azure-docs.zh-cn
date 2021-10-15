---
title: 注册调用 Web API 的移动应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用（应用的注册）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/18/2021
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: dba6dcf0dded3db4c1cf1ddc26071e9803b55cd6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232910"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>注册调用 Web API 的移动应用

可以借助本文中的说明注册你正在创建的移动应用程序。

## <a name="supported-account-types"></a>支持的帐户类型

移动应用程序支持的帐户类型取决于要启用的体验，以及要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

大多数移动应用程序使用交互式身份验证。 如果应用使用这种形式的身份验证，则你可以从任何[帐户类型](quickstart-register-app.md)将用户登录。

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>集成 Windows 身份验证、用户名-密码以及 B2C 的受众

如果拥有通用 Windows 平台 (UWP) 应用，则可使用集成 Windows 身份验证 (IWA) 使用户登录。 若要使用 IWA 或用户名-密码身份验证，则应用程序需在你自己的业务线 (LOB) 开发人员租户中使用户登录。 在独立软件供应商 (ISV) 方案中，应用程序可以在 Azure Active Directory 组织中将用户登录。 Microsoft 个人帐户不支持这些身份验证流。

还可以使用传递 B2C 颁发机构和策略的社交标识将用户登录。 若要使用此方法，只能使用交互式身份验证和用户名-密码身份验证。 用户名-密码身份验证目前仅在 Xamarin.iOS、Xamarin.Android 和 UWP 上受支持。

有关详细信息，请参阅[方案和支持的身份验证流](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)以及[方案和支持的平台与语言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>平台配置和重定向 URI

### <a name="interactive-authentication"></a>交互式身份验证

构建使用交互式身份验证的移动应用时，最关键的注册步骤是重定向 URI。 可以通过[“身份验证”边栏选项卡上的平台配置](https://aka.ms/MobileAppReg)来设置交互式身份验证。

应用可以在此体验中通过 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）实现单一登录 (SSO)。 它还支持设备管理策略。

应用注册门户提供一个预览版体验来帮助你计算 iOS 和 Android 应用程序的中介回复 URI：

1. 在应用注册门户中选择“身份验证” > “尝试新体验”。

   ![“身份验证”边栏选项卡，可在其中选择新体验](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 选择“添加平台”。 

   ![添加平台](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 如果支持平台列表，请选择“iOS”。 

   ![选择移动应用程序](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 输入捆绑 ID，然后选择“注册”。 

   ![输入捆绑 ID](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

完成这些步骤后，系统将会计算重定向 URI，如下图所示。

![生成的重定向 URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果你偏向于手动配置重定向 URI，可以通过应用程序清单进行配置。 下面是清单的建议格式：

- **iOS**：`msauth.<BUNDLE_ID>://auth`
  - 例如，输入 `msauth.com.yourcompany.appName://auth`
- **Android**：`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 可以通过 KeyTool 命令使用发布密钥或调试密钥来生成 Android 签名哈希。

### <a name="username-password-authentication"></a>用户名-密码身份验证

如果应用仅使用用户名-密码身份验证，则无需为应用程序注册重定向 URI。 此流将往返访问 Microsoft 标识平台。 不会在任何特定 URI 上调用你的应用程序。

但是，请将应用程序标识为公共客户端应用程序。 为此，请执行以下操作：

1. 仍在 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>中，选择“应用注册”中的应用，然后选择“身份验证” 。
1. 在“高级设置” > “允许公共客户端流” > “启用以下移动和桌面流:”中，选择“是”。

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="在 Azure 门户中的“身份验证”窗格上启用公共客户端设置":::

## <a name="api-permissions"></a>API 权限

适用于已登录用户的移动应用程序调用 API。 应用需要请求委托的权限。 这些权限也称为范围。 根据所需的体验，可以通过 Azure 门户以静态方式请求委托的权限。 或者，可以在运行时动态请求这些权限。

以静态方式注册权限可让管理员轻松审批应用。 建议使用静态注册。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[应用代码配置](scenario-mobile-app-configuration.md)。
