---
title: 使用 Web 帐户管理器（桌面应用）获取用于调用 Web API 的令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用 Web 帐户管理器，构建调用 Web API 以获取应用的令牌的桌面应用
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: a6b57ac40c124b2290642e452ef9c9f294acc9da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837663"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-wam"></a>调用 Web API 的桌面应用：使用 WAM 获取令牌

MSAL 能够调用 Web 帐户管理器（Windows 10 OS 附带的组件）。 此组件充当身份验证中转站，应用的用户受益于与 Windows 已知帐户（例如在 Windows 会话中登录的帐户）的集成。

## <a name="availability"></a>可用性

MSAL 4.25+ 支持 UWP、.NET Classic、.NET Core 3.x 和 .NET 5 上的 WAM。

对于 .NET Classic 和 .NET Core 3.x，WAM 功能完全受支持，但必须添加对 [Microsoft.Identity.Client.Desktop](https://www.nuget.org/packages/Microsoft.Identity.Client.Desktop/) 包以及 MSAL 的引用，并且调用 `.WithWindowsBroker()` 而不是 `WithBroker()`。

对于 .NET 5，目标为 `net5.0-windows10.0.17763.0`（或更高版本），而不只是 `net5.0`。 如果在 csproj 中添加 `<SupportedOSPlatformVersion>7</SupportedOSPlatformVersion>`，应用仍将在旧版 Windows 上运行。 WAM 不可用时，MSAL 将使用浏览器。

## <a name="wam-value-proposition"></a>WAM 价值主张

使用身份验证中转站（例如 WAM）有很多好处。

- 增强的安全性（应用无需管理功能强大的刷新令牌）
- 对 Windows Hello、条件访问和 FIDO 密钥更好的支持
- 与 Windows“电子邮件和帐户”视图集成
- 更好的单一登录（用户不必重新输入密码）
- 大多数 bug 修补程序和增强功能都将随 Windows 提供

## <a name="wam-limitations"></a>WAM 限制

- 不支持 B2C 机构。
- 在 Win10、Win Server 2016、Win Server 2019 上可用。 在 Mac、Linux 和早期版本的 Windows 上，MSAL 将回退到浏览器。

## <a name="wam-calling-pattern"></a>WAM 调用模式

可以通过以下模式来使用 WAM。

```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```

调用 `.WithBroker(true)`。 如果中转站（例如 Win8.1、Mac 或 Linux）不存在，则 MSAL 将回退到浏览器！ 重定向 URI 规则适用于浏览器。

## <a name="redirect-uri"></a>重定向 URI

WAM 重定向 URI 无需在 MSAL 中进行配置，但必须在应用注册中进行配置。

### <a name="win32-net-framework--net-5"></a>Win32 (.NET framework/.NET 5)

```
ms-appx-web://microsoft.aad.brokerplugin/{client_id}
```

### <a name="uwp"></a>UWP
```csharp
            // returns smth like S-1-15-2-2601115387-131721061-1180486061-1362788748-631273777-3164314714-2766189824
            string sid = WebAuthenticationBroker.GetCurrentApplicationCallbackUri().Host.ToUpper();

            // the redirect uri you need to register
            string redirectUri = $"ms-appx-web://microsoft.aad.brokerplugin/{sid}";
```

## <a name="token-cache-persistence"></a>令牌缓存持久性

保留 MSAL 的令牌缓存非常重要，因为 MSAL 需要在其中保存内部 WAM 帐户 ID。 如果没有它，重启应用意味着 `GetAccounts` API 会错过某些帐户。 请注意，在 UWP 上，MSAL 知道在何处保存令牌缓存。

## <a name="getaccounts"></a>GetAccounts

`GetAccounts` 会返回之前以交互方式登录应用的用户帐户。

除此之外，WAM 还可以列出在 Windows（Win32 应用而不是 UWP 应用）中配置的 OS 范围内工作和学校帐户。 若要选择启用此功能，请将 `WindowsBrokerOptions` 中的 `ListWindowsWorkAndSchoolAccounts` 设置为 true。 可以按以下方式启用它。

```csharp
.WithWindowsBrokerOptions(new WindowsBrokerOptions()
{
    // GetAccounts will return Work and School accounts from Windows
    ListWindowsWorkAndSchoolAccounts = true,

    // Legacy support for 1st party apps only
    MsaPassthrough = true
})
```

>[!NOTE]
> 出于隐私原因，Microsoft（例如 outlook.com 等）帐户不会在 Win32 或 UWP 中列出。

应用程序无法从 Windows中删除帐户！ 

## <a name="removeasync"></a>RemoveAsync

- 从 MSAL 的令牌缓存中删除所有帐户信息（这包括 MSA (即个人帐户) 帐户信息以及 MSAL 复制到其缓存中的其他帐户）。
- 删除仅应用（非 OS 范围）帐户。

>[!NOTE]
> 应用无法删除 OS 帐户。 只有用户才能进行该操作。 如果将 OS 帐户传递到 `RemoveAsync`，然后在启用 `ListWindowsWorkAndSchoolAccounts` 时调用 `GetAccounts`，则仍将返回同一 OS 帐户。

## <a name="other-considerations"></a>其他注意事项

- WAM 的交互式操作需要位于 UI 线程上。 当不在 UI 线程上时，MSAL 将引发有意义的异常。 这不适用于控制台应用。
- 如果 MSAL 帐户最初是通过 WAM 获取的，或者 WAM 可以在 Windows 中找到工作和学校帐户，则 `WithAccount` 提供加速身份验证体验。
- 除非在 Windows 中找到了具有相同用户名的工作和学校帐户，否则 WAM 无法通过登录提示预先填充用户名字段。
- 如果 WAM 无法提供加速身份验证体验，则会显示帐户选取器。 用户可以添加新帐户。

![“WAM 帐户选取器”](media/scenario-desktop-acquire-token-wam/wam-account-picker.png)

- Windows 会自动记住新的帐户。 工作和学校可以选择加入组织的目录或完全选择退出，在这种情况下，帐户不会显示在“电子邮件和帐户”下。 Microsoft 帐户会自动添加到 Windows 中。 应用无法以编程方式列出这些帐户（只能通过帐户选取器列出）。

## <a name="troubleshooting"></a>疑难解答

使用 MSAL 的应用作为提升的进程运行时，WAM 中的一些调用可能会由于不同的进程安全级别而失败。 在内部，MSAL.NET COM 使用本机 Windows 方法（[COM](/windows/win32/com/the-component-object-model)）与 WAM 集成。 从版本 4.32.0 开始，MSAL 在检测到应用进程已提升且 WAM 未返回帐户时会显示描述性的错误消息。

一种解决方案是尽可能地不以提升的权限运行应用。 另一种可能的规避方法是在应用启动时调用 `WindowsNativeUtils.InitializeProcessSecurity` 方法。 这会将 WAM 所用进程的安全性设置为相同的级别。 有关示例，请参阅[此示例应用](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/devapps/WAM/NetCoreWinFormsWam/Program.cs#L18-L21)。 但是，请注意，由于基础 CLR 行为等外部因素，此规避方法不一定会成功。 在这种情况下，将引发 `MsalClientException`。 有关其他信息，请参阅问题[#2560](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/2560)。

## <a name="sample"></a>示例

[使用 WAM 的 WPF 示例](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)

[使用 WAM 以及 Xamarin 的 UWP 示例](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/2-With-broker)

---
## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[从桌面应用调用 Web API](scenario-desktop-call-api.md)。
