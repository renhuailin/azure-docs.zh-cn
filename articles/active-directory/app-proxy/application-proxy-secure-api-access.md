---
title: 使用 Azure Active Directory 应用程序代理访问本地 API
description: 通过使用 Azure Active Directory 应用程序代理，本机应用程序可安全访问本地或云 VM 上托管的 API 和业务逻辑。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: has-adal-ref
ms.openlocfilehash: 5bcdfdd3a56807db967d6be1a8bde540b88c17ca
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230943"
---
# <a name="secure-access-to-on-premises-apis-with-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理安全访问本地 API

你可以使用本地运行的业务逻辑 API，也可以使用云中虚拟机上托管的业务逻辑 API。 本机 Android、iOS、Mac 或 Windows 应用需要与 API 终结点交互才能使用数据或提供用户交互功能。 通过使用 Azure AD 应用程序代理和 [Microsoft 身份验证库 (MSAL)](../azuread-dev/active-directory-authentication-libraries.md)，本机应用可安全访问本地 API。 与开启防火墙端口和控制应用层身份验证和授权相比，Azure Active Directory 应用程序代理是一种更快速、更安全的解决方案。

本文逐步讲解如何设置 Azure AD 应用程序代理解决方案，以托管本机应用可以访问的 Web API 服务。

## <a name="overview"></a>概述

下图显示了发布本地 API 的传统方法。 此方法需要开启传入端口 80 和 443。

![传统 API 访问](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下图显示如何使用 Azure AD 应用程序代理安全发布 API，而无需开启任何传入端口：

![Azure AD 应用程序代理 API 访问](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 应用程序代理是此解决方案的主干，可用作 API 访问的公共终结点，并提供身份验证和授权。 你可以使用 [Microsoft 身份验证库 (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) 中的多个库，从大量平台访问 API。

由于 Azure AD 应用程序代理身份验证和授权是基于 Azure AD 实现的，因此可以使用 Azure AD 条件访问，确保只有受信任的设备才可访问通过应用程序代理发布的 API。 对于桌面设备，可使用 Azure AD 联接或 Azure AD 混合联接设备，而对于设备，可使用 Intune 托管设备。 你还可以利用 Azure Active Directory Premium 功能，例如 Azure AD 多重身份验证，以及由机器学习提供支持的 [Azure 标识保护](../identity-protection/overview-identity-protection.md)安全功能。

## <a name="prerequisites"></a>先决条件

若要完成本演练，你需要：

- Azure 目录的管理员访问权限，以及可用于创建和注册应用的帐户
- 来自 [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 的示例 Web API 和本机客户端应用

## <a name="publish-the-api-through-application-proxy"></a>通过应用程序代理发布 API

若要通过应用程序代理在 Intranet 外部发布 API，请遵循与发布 Web 应用相同的模式。 有关详细信息，请参阅[教程：添加本地应用程序以通过 Azure Active Directory 应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)。

通过应用程序代理发布 SecretAPI Web API：

1. 生成示例 SecretAPI 项目并将其发布为本地计算机或 Intranet 上的 ASP.NET Web 应用。 请确保可以在本地访问 Web 应用。

1. 在“Azure 门户”中，选择“Azure Active Directory”。 然后选择“企业应用程序”。

1. 在“企业应用程序 - 所有应用程序”页的顶部，选择“新建应用程序”。

1. 在“添加应用程序”页上，选择“本地应用程序”。 随即显示“添加自己的本地应用程序”页。

1. 如果尚未安装应用程序代理连接器，系统会提示你安装。 选择“下载应用程序代理连接器”，以下载并安装连接器。

1. 安装应用程序代理连接器后，请在“添加自己的本地应用程序”页上执行以下操作：

   1. 在“名称”旁边，输入“SecretAPI”。

   1. 在“内部 URL”旁边，输入用于从 Intranet 中访问 API 的 URL。

   1. 确保将“预身份验证”设置为“Azure Active Directory”。

   1. 在页面顶部选择“添加”，等待应用完成创建。

   ![添加 API 应用](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. 在“企业应用程序 - 所有应用程序”页上，选择“SecretAPI”应用。

1. 在“SecretAPI - 概述”页上，选择左侧导航栏中的“属性”。

1. 如果不希望在“MyApps”面板中向最终用户提供 API，则在“属性”页底部将“对用户可见”设置为“否”，然后选择“保存”。

   ![对用户不可见](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

你已通过 Azure AD 应用程序代理发布了 Web API。 现在，添加可以访问应用的用户。

1. 在“SecretAPI - 概述”页上，选择左侧导航栏中的“用户和组”。

1. 在“用户和组”页上，选择“添加用户”。

1. 在“添加分配”页上，选择“用户和组”。

1. 在“用户和组”页上，搜索并选择可以访问应用的用户，至少应包括你自己。 选择所有用户后，选择“选择”。

   ![选择并分配用户](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. 回到“添加分配”页上，选择“分配”。

> [!NOTE]
> 对于使用集成 Windows 身份验证的 API，可能还需要执行[其他步骤](./application-proxy-configure-single-sign-on-with-kcd.md)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>注册本机应用并授予对 API 的访问权限

本机应用是为在特定平台或设备上使用而开发的程序。 在本机应用可以连接和访问 API 之前，必须在 Azure AD 中进行注册。 以下步骤演示如何注册本机应用，并授予它访问通过应用程序代理所发布 Web API 的权限。

注册 AppProxyNativeAppSample 本机应用：

1. 在 Azure Active Directory“概述”页上，选择“应用注册”，然后在“应用注册”窗格顶部，选择“新建注册”。

1. 在“注册应用程序”页面上：

   1. 在“名称”下，输入 AppProxyNativeAppSample。

   1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户”。 

   1. 在“重定向 URL”下，使用下拉列表选择“公共客户端(移动和桌面)”，然后输入 https://login.microsoftonline.com/common/oauth2/nativeclient。

   1. 选择“注册”，并等待应用成功注册。

      ![新应用程序注册](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

你现在已经在 Azure Active Directory 中注册了 AppProxyNativeAppSample 应用。 为本机应用提供访问 SecretAPI Web API 的权限：

1. 在 Azure Active Directory“概述” > “应用注册”页上，选择“AppProxyNativeAppSample”应用。

1. 在“AppProxyNativeAppSample”页上，选择左侧导航栏中的“API 权限”。

1. 在“API 权限”页上，选择“添加权限”。

1. 在第一个“请求获取 API 权限”页上，选择“我的组织使用的 API”选项卡，然后搜索并选择“SecretAPI”。

1. 在下一个“请求获取 API 权限”页上，选中“user_impersonation”旁边的复选框，然后选择“添加权限”。

    ![选择 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. 返回“API 权限”页，你可以选择“为 Contoso 授予管理员许可”，以防止其他用户必须单独同意该应用。

## <a name="configure-the-native-app-code"></a>配置本机应用代码

最后一步是配置本机应用。 NativeClient 示例应用中 Form1.cs 文件的以下代码片段可让 MSAL 库获取用于请求 API 调用的令牌，并以持有者身份将其附加到应用标头中。

   ```
   // Acquire Access Token from AAD for Proxy Application
 IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }
 
if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application
  
  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

若要配置本机应用以连接到 Azure Active Directory 并调用 API 应用代理，请在 NativeClient 示例应用的 App.config 文件中将占位符值更新为来自 Azure AD 的值：

- 将“目录(租户) ID”粘贴到 `<add key="ida:Tenant" value="" />` 字段中。 你可以从任一应用的“概述”页中找到并复制此值 (GUID)。

- 将 AppProxyNativeAppSample“应用程序(客户端) ID”粘贴到 `<add key="ida:ClientId" value="" />` 字段中。 你可以从 AppProxyNativeAppSample“概述”页中找到并复制此值 (GUID)。

- 将 AppProxyNativeAppSample“重定向 URI”粘贴到 `<add key="ida:RedirectUri" value="" />` 字段中。 你可以从 AppProxyNativeAppSample“身份验证”页中找到并复制此值 (URI)。

- 将 SecretAPI“应用程序 ID URI”粘贴到 `<add key="todo:TodoListResourceId" value="" />` 字段中。 你可以从 SecretAPI“公开 API”页中找到并复制此值 (URI)。

- 将 SecretAPI“主页 URL”粘贴到 `<add key="todo:TodoListBaseAddress" value="" />` 字段中。 你可以从 SecretAPI“品牌打造”页中找到并复制此值 (URL)。

配置参数后，生成并运行本机应用。 选择“登录”按钮时，应用程序会让你登录，然后显示成功屏幕，以确认它已成功连接到 SecretAPI。

![显示“已成功连接 Secret A P I”消息和“确定”按钮的屏幕截图。](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)
- [快速入门：配置客户端应用程序以访问 Web API](../develop/quickstart-configure-app-access-web-apis.md)
- [如何让本机客户端应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)
