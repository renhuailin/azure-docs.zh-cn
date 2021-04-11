---
title: Xamarin Android 代码配置和故障排除 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的注意事项。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199563"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Xamarin Android 与 MSAL.NET 配合使用时的配置要求和故障排除提示

在将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时，需要在代码中完成一些配置更改。 以下部分介绍了所要求的修改，之后的[故障排除](#troubleshooting)部分用于帮助避免一些最常见的问题。

## <a name="set-the-parent-activity"></a>设置父活动

在 Xamarin Android 上，设置父活动，使令牌在交互后返回：

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

在 MSAL.NET 4.2 和更高版本中，也可以在 [PublicClientApplication][PublicClientApplication] 级别设置此功能。 为此，请使用一个回调：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

如果使用 [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)，则 [`PublicClientApplication`][PublicClientApplication] 生成器代码应类似于以下代码片段：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>确保控制权返回到 MSAL

在身份验证流的交互部分结束时，请将控制权返回到 MSAL，具体方式是重写 [`Activity`][Activity].[`OnActivityResult()`][OnActivityResult] 方法。

在重写的代码中，请调用 MSAL.NET 的 `AuthenticationContinuationHelper`.`SetAuthenticationContinuationEventArgs()` 方法，以在身份验证流的交互部分结束时使控制权返回到 MSAL。

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>更新 Android 清单以获取系统 Web 视图支持 

若要支持系统 Web 视图，*AndroidManifest.xml* 文件应包含以下值：

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

`android:scheme` 值是从应用程序门户中配置的重定向 URI 创建的。 例如，如果你的重定向 URI 是 `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth`，则清单中的 `android:scheme` 条目将类似于以下示例：

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

或者，[在代码中创建活动](/xamarin/android/platform/android-manifest#the-basics)，而不要手动编辑 *AndroidManifest.xml*。 若要在代码中创建活动，请先创建一个包含 `Activity` 属性和 `IntentFilter` 属性的类。

下面是表示 XML 文件值的类的示例：

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>在中转身份验证中使用系统 Web 视图

在将应用程序配置为使用中转身份验证并且设备上未安装中介时，若要将系统 Web 视图用作交互式身份验证的回退机制，请启用 MSAL，以使用中介的重定向 URI 捕获身份验证响应。 当 MSAL 检测到中介不可用时，它会尝试使用设备上的默认系统 Web 视图进行身份验证。 使用此默认项时，MSAL 将会失败，因为重定向 URI 已配置为使用中介，而系统 Web 视图不知道如何使用中介返回到 MSAL。 若要解决此问题，请使用前面配置的中介重定向 URI 创建一个意向筛选器。 如以下示例所示，通过修改应用程序的清单来添加该意向筛选器：

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

将 `android:host=` 值替换为在 Azure 门户中注册的包名称。 将 `android:path=` 值替换为在 Azure 门户中注册的密钥哈希。 不应该对签名哈希进行 URL 编码。  确保前导正斜杠 (`/`) 出现在签名哈希的开头。

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.x 清单

Xamarin.Forms 4.3.x 会生成可在 *AndroidManifest.xml* 中将 `package` 属性设置为 `com.companyname.{appName}` 的代码。 如果使用 `DataScheme` 作为 `msal{client_id}`，则可能需要更改该值，使之与 `MainActivity.cs` 命名空间的值匹配。

## <a name="android-11-support"></a>Android 11 支持

若要在 Android 11 中使用系统浏览器和中转身份验证，必须先声明这些包，使它们对应用可见。 面向 Android 10 (API 29) 及更低版本的应用可在任意给定时间查询 OS 以获取设备上可用的包列表。 为了支持隐私和安全保护，Android 11 降低了包的可见性，只会显示默认的 OS 包列表，以及应用的 *AndroidManifest.xml* 文件中指定的 OS 包。 

要使应用程序能够使用系统浏览器和中介进行身份验证，请将以下节添加到 *AndroidManifest.xml*：

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

请将 `{Package Name}` 替换为应用程序包名称。 

更新的清单（现在包含对系统浏览器和中转身份验证的支持）应类似于以下示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式 Web 视图（可选）

默认情况下，MSAL.NET 使用系统 Web 浏览器。 在此浏览器中，可以使用 Web 应用程序和其他应用来实现单一登录 (SSO)。 在罕见的情况下，你可能希望系统使用嵌入式 Web 视图。

此代码示例演示如何设置嵌入式 Web 视图：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

有关详细信息，请参阅[使用适用于 MSAL.NET 的 Web 浏览器](msal-net-web-browsers.md)和 [Xamarin Android 系统浏览器注意事项](msal-net-system-browser-android-considerations.md)。

## <a name="troubleshooting"></a>疑难解答

### <a name="general-tips"></a>一般技巧

- 将现有的 MSAL.NET NuGet 包更新到 [MSAL.NET 的最新版](https://www.nuget.org/packages/Microsoft.Identity.Client/)。
- 验证 Xamarin.Forms 是否为最新版本。
- 验证 Xamarin.Android.Support.v4 是否为最新版本。
- 确保所有 Xamarin.Android.Support 包都面向最新版本。
- 清理或重新生成应用程序。
- 在 Visual Studio 中，尝试将最大并行项目生成数设置为 1。 为此，请选择“选项” > “项目和解决方案” > “生成和运行” > “最大并行项目生成数”。    
- 如果通过命令行生成并且命令使用 `/m`，请尝试从命令中删除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>错误：名称 AuthenticationContinuationHelper 不存在于当前上下文中

如果有错误指明当前上下文中不存在 `AuthenticationContinuationHelper`，则说明 Visual Studio 可能错误地更新了 Android.csproj\* 文件。 `<HintPath>` 元素中的文件路径有时会错误地包含 `netstandard13` 而不是 `monoandroid90`。

此示例包含正确的文件路径：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用 Microsoft 标识平台的 Xamarin 移动应用程序](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)示例。 下表汇总了自述文件中的相关信息。

| 示例 | 平台 | 说明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS、Android、UWP | 一个简单的 Xamarin.Forms 应用，展示了如何使用 MSAL 通过 Azure AD 2.0 终结点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还展示了如何访问 Microsoft Graph 并显示了生成的令牌。 <br>![身份验证流示意图](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
