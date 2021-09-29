---
title: 如何使用 MSAL 在 Android 上启用跨应用 SSO | Azure
titleSuffix: Microsoft identity platform
description: 如何使用适用于 Android 的 Microsoft 身份验证库 (MSAL) 启用跨应用程序的单一登录。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: marsma
ms.reviewer: marsma
ms.openlocfilehash: 877ebb2ab58af978896e4cb61989e12bb5731a7c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628907"
---
# <a name="enable-cross-app-sso-on-android-using-msal"></a>使用 MSAL 在 Android 上启用跨应用 SSO

有了单一登录 (SSO)，用户只需输入一次凭据，这些凭据将能自动应用于多个应用程序上。

[Microsoft 标识平台](./index.yml)和 Microsoft 身份验证库 (MSAL) 可帮助你在自己的一套应用中启用 SSO。 利用中转站功能和 Authenticator 应用程序，可以在整个设备上扩展 SSO。

本操作指南介绍了如何配置应用程序所使用的 SDK，以便向客户提供 SSO。

## <a name="prerequisites"></a>先决条件

本操作指南假定你知道如何执行以下操作：

- 使用 Azure 门户预配应用。 有关本主题的详细信息，请参阅 [Android 教程](./tutorial-v2-android.md#create-a-project)中有关创建应用的说明
- 将应用程序与[适用于 Android 的 Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-android)进行集成。

## <a name="methods-for-single-sign-on"></a>单一登录方法

应用程序可通过两种方式，使用适用于 Android 的 MSAL 来实现 SSO：

* 通过[中转站应用程序](#sso-through-brokered-authentication)
* 通过[系统浏览器](#sso-through-system-browser)


   为了实现设备范围的 SSO、帐户管理和条件访问等权益，建议使用中转站应用程序。 但是，它要求用户下载其他应用程序。

## <a name="sso-through-brokered-authentication"></a>通过中转身份验证的 SSO

建议使用 Microsoft 的某一个身份验证中介来参与设备范围的单一登录 (SSO)，并保证符合组织条件访问策略的要求。 与中介集成可提供以下优势：

- 设备单一登录
- 对以下功能进行条件访问：
  - Intune 应用保护
  - 设备注册 (Workplace Join)
  - 移动设备管理
- 设备范围的帐户管理
  -  通过 Android AccountManager 和帐户设置
  - “工作帐户”- 自定义帐户类型

在 Android 上，Microsoft 身份验证中介是随附在 [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)应用中的一个组件。

下图演示了应用、Microsoft 身份验证库 (MSAL) 与 Microsoft 身份验证中介之间的关系。

![关系图显示了应用程序如何与 MSAL、中介应用以及 Android 帐户管理器进行关联。](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>安装托管中介的应用

设备所有者随时可以从应用商店（通常是 Google Play 商店）安装中介托管应用。 但是，某些 API（资源）受条件访问策略的保护，这些策略要求设备：

- 已注册（已加入工作区），和/或
- 已在设备管理中注册，或
- 已在 Intune 应用保护中注册

如果某个设备上尚未安装中介应用，当应用尝试以交互方式获取令牌时，MSAL 会立即指示用户安装一个中介应用。 然后，应用需要引导用户完成相关步骤，使设备符合所需的策略。

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>安装和卸载中介的影响

#### <a name="when-a-broker-is-installed"></a>已安装中介时

在设备上安装中介后，所有后续交互式令牌请求（对 `acquireToken()` 的调用）将由该中介处理，而不是由 MSAL 在本地处理。 以前提供给 MSAL 的任何 SSO 状态不会提供给中介。 因此，用户需要重新进行身份验证，或者从设备已知的现有帐户列表中选择一个帐户。

安装中介无需用户再次登录。 仅当用户需要解决 `MsalUiRequiredException` 时，下一个请求才会发送到中介。 引发 `MsalUiRequiredException` 的原因有很多，需要以交互方式解决。 例如：

- 用户更改了与其帐户关联的密码。
- 用户的帐户不再符合条件访问策略。
- 用户撤销了应用与其帐户关联的许可。

**多个中转站** - 如果设备上安装了多个中转站，则第一个安装的中转站始终是活动中转站。 仅单个中介可以在设备上处于活动状态。

#### <a name="when-a-broker-is-uninstalled"></a>已卸载中介时

如果只安装了一个中介托管应用，后来删除了该应用，则用户需要再次登录。 卸载活动中介会从设备中删除相应的帐户和关联的令牌。

如果 Intune 公司门户已安装并作为活动中介运行，此外还安装了 Microsoft Authenticator，则在卸载 Intune 公司门户（活动中介）后，用户需要再次登录。 一旦用户再次登录，Microsoft Authenticator 应用就会成为活动中介。

### <a name="integrating-with-a-broker"></a>与中介集成

#### <a name="generate-a-redirect-uri-for-a-broker"></a>为中介生成重定向 URI

必须注册与中介兼容的重定向 URI。 中介的重定向 URI 应该包含应用的包名称，以及应用签名的 base64 编码表示形式。

重定向 URI 的格式为：`msauth://<yourpackagename>/<base64urlencodedsignature>`

使用应用的签名密钥，你可以使用 [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) 生成 Base64 编码的签名哈希，然后使用 Azure 门户通过该哈希生成重定向 URI。

Linux 和 macOS：

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

使用 keytool 生成签名哈希后，请使用 Azure 门户生成重定向 URI：

1. 登录到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>，并选择“应用注册”中的 Android 应用。
1. 选择“身份验证” > “添加平台” > “Android”  。
1. 在“配置 Android 应用”窗格打开时，输入你之前生成的“签名哈希”，然后输入“包名称”  。
1. 选择“配置”按钮。

Azure 门户会为你生成重定向 URI，并将其显示在“Android 配置”窗格的“重定向 URI”字段中 。

有关对应用进行签名的详细信息，请参阅 Android Studio 用户指南中的[对应用进行签名](https://developer.android.com/studio/publish/app-signing)。

> [!IMPORTANT]
> 对于生产版本的应用，请使用生产签名密钥。

#### <a name="configure-msal-to-use-a-broker"></a>将 MSAL 配置为使用中介

若要在应用中使用中介，必须证明你已配置中介重定向。 例如，通过在 MSAL 配置文件中包含以下设置，来包含中介启用的重定向 URI，并指示已注册该中介：

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>中介相关的异常

MSAL 通过两种方式来与中介通信：

- 中介绑定服务
- Android AccountManager

MSAL 首先使用中介绑定服务，因为调用此服务不需要任何 Android 权限。 如果绑定到绑定服务失败，MSAL 将使用 Android AccountManager API。 仅当已为应用授予 `"READ_CONTACTS"` 权限时，MSAL 才使用此 API。

如果收到 `MsalClientException` 和错误代码 `"BROKER_BIND_FAILURE"`，可以采取两种做法：

- 要求用户禁用 Microsoft Authenticator 应用和 Intune 公司门户的超级优化。
- 要求用户授予 `"READ_CONTACTS"` 权限

### <a name="verify-broker-integration"></a>验证中转站集成

虽然可能无法立即弄清楚中介集成是否正在运行，但你可以使用以下步骤进行检查：

1. 在 Android 设备上，使用中介完成某个请求。
1. 在 Android 设备上的设置中，查找与进行身份验证时所使用的帐户相对应的新建帐户。 该帐户的类型应为“工作帐户”。

如果要重复测试，可以从设置中删除该帐户。

## <a name="sso-through-system-browser"></a>通过系统浏览器的 SSO

Android 应用程序可以选择使用 Web 视图、系统浏览器或 Chrome 自定义选项卡进行身份验证用户体验。 如果应用程序未使用中转身份验证，则需要使用系统浏览器，而不是本机 Web 视图来实现 SSO。

### <a name="authorization-agents"></a>授权代理

为授权代理选择特定的策略属于可选操作，这是应用可以自定义的附加功能。 大多数应用将使用 MSAL 默认设置（有关各项默认设置，请参阅[了解 Android MSAL 配置文件](msal-configuration.md)）。

MSAL 支持使用 `WebView` 或系统浏览器授权。  下图显示了使用 `WebView` 或使用包含或不包含自定义标签页的系统浏览器进行授权的大致形式：

![MSAL 登录示例](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>单一登录含义

默认情况下，与 MSAL 集成的应用程序使用系统浏览器的自定义标签页进行授权。 与 WebView 不同，自定义标签页与默认系统浏览器共享 Cookie jar，可以减少与自定义标签页集成的 Web 应用或其他本机应用中的登录次数。

如果应用程序使用 `WebView` 策略，不将 Microsoft Authenticator 或公司门户支持集成到其应用中，则用户将不会获得跨设备或在本机应用与 Web 应用之间的单一登录体验。

如果应用程序使用具有中转站（例如 Microsoft Authenticator 或 Intune 公司门户）的 MSAL，并且用户在其中某一个应用中拥有一个有效的登录名，则用户可以获得跨应用程序的 SSO 体验。

### <a name="webview"></a>WebView

若要使用应用中 WebView，请在传递给 MSAL 的应用配置 JSON 中添加以下行：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用应用中 `WebView` 时，用户可以直接登录到应用。 令牌保留在应用的沙盒内部，不能在应用 Cookie jar 的外部使用。 因此，除非应用与 Authenticator 或公司门户集成，否则用户无法获得跨应用程序的 SSO 体验。

但是，`WebView` 确实提供用于自定义登录 UI 外观的功能。 有关如何进行这种自定义的详细信息，请参阅 [Android WebView](https://developer.android.com/reference/android/webkit/WebView)。

### <a name="default-browser-plus-custom-tabs"></a>默认浏览器加上自定义标签页

MSAL 默认使用浏览器和[自定义标签页](https://developer.chrome.com/multidevice/android/customtabs)策略。 可以在自定义配置文件中使用以下 JSON 配置显式指定此策略，以防将来的版本对 `DEFAULT` 做出更改：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法可通过设备的浏览器提供 SSO 体验。 MSAL 使用共享的 Cookie jar，使其他本机应用或 Web 应用能够使用 MSAL 设置的持久会话 Cookie 在设备上实现 SSO。

### <a name="browser-selection-heuristic"></a>浏览器选择试探法

由于 MSAL 无法指定可在众多 Android 手机上使用的确切浏览器包，因此 MSAL 实施浏览器选择试探法，以尝试提供最佳的跨设备 SSO。

MSAL 主要从包管理器中检索默认浏览器，并检查它是否在已测试的安全浏览器列表中。 如果不在，MSAL 将退回到使用 Web 视图，而不是从安全列表启动其他非默认浏览器。 始终会选择默认浏览器，无论其是否支持自定义标签。 如果浏览器支持自定义标签页，MSAL 将启动自定义标签页。自定义标签页的外观更接近应用中 `WebView`，允许基本的 UI 自定义。 有关详细信息，请参阅 [Android 中的自定义标签页](https://developer.chrome.com/multidevice/android/customtabs)。

如果设备上没有浏览器包，MSAL 将使用应用中 `WebView`。 如果设备默认设置未更改，则每次登录时应启动同一浏览器，以确保提供 SSO 体验。

#### <a name="tested-browsers"></a>已测试的浏览器

我们已对以下浏览器进行测试，以确定它们是否可以正确重定向到配置文件中指定的 `"redirect_uri"`：

| 设备 | 内置浏览器 | Chrome | Opera  | Microsoft Edge | UC 浏览器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | 通过 | 通过 |不适用 |不适用 |不适用 |不适用 |
| Samsung S7 (API 25) | 通过<sup>1</sup> | 通过 | 通过 | 通过 | 失败 |通过 |
| Huawei (API 26) |通过<sup>2</sup> | 通过 | 失败 | 通过 | 通过 |通过 |
| Vivo (API 26) |通过|通过|通过|通过|通过|失败|
| Pixel 2 (API 26) |通过 | 通过 | 通过 | 通过 | 失败 |通过 |
| Oppo | 通过 | 不适用<sup>3</sup>|不适用  |不适用 |不适用 | 不适用|
| OnePlus (API 25) |通过 | 通过 | 通过 | 通过 | 失败 |通过 |
| Nexus (API 28) |通过 | 通过 | 通过 | 通过 | 失败 |通过 |
|MI | 通过 | 通过 | 通过 | 通过 | 失败 |通过 |

<sup>1</sup>Samsung 的内置浏览器是 Samsung Internet。<br/>
<sup>2</sup>Huawei 的内置浏览器是 Huawei Browser。<br/>
<sup>3</sup>无法在 Oppo 设备设置中更改默认浏览器。

## <a name="next-steps"></a>后续步骤

使用[适用于 Android 设备的共享设备模式](msal-android-shared-devices.md)，可以对 Android 设备进行配置，使其可由多名员工轻松共享。