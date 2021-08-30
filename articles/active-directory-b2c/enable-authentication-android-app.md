---
title: 在 Android 应用中启用身份验证 - Azure AD B2C
description: 使用 Azure Active Directory B2C 构建基块在 Android 应用程序中启用身份验证。 了解如何使用 Azure AD B2C 将用户注册并登录到 Android 应用程序中。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/06/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support, has-adal-ref
ms.openlocfilehash: b9341909f6dd1dd9d01408a7b7af46e47a0ff339
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428711"
---
# <a name="enable-authentication-in-your-own-android-application-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在自己的 Android 应用程序中启用身份验证

本文介绍如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证添加到你自己的 Android 移动应用程序。 

请将本文与[在示例 Android 应用程序中配置身份验证](./configure-authentication-sample-android-app.md)结合使用，并将示例 Android 应用替换为你自己的 Android 应用。 完成本文中的步骤后，应用程序将接受通过 Azure AD B2C 进行登录。

## <a name="prerequisites"></a>先决条件

查看[在示例 Android 应用程序中配置身份验证](configure-authentication-sample-android-app.md)一文中的先决条件和集成步骤。

## <a name="create-an-android-app-project"></a>创建 Android 应用项目

如果你还没有 Android 应用程序，请按照以下步骤设置新项目。

1. 打开 Android Studio，然后选择“启动新的 Android Studio 项目”  。
2. 选择“基本活动”，再选择“下一步”   。
3. 命名应用程序。
4. 保存包名称。 稍后需要在 Azure 门户中输入此名称。
5. 将语言从“Kotlin”  更改为“Java”  。
6. 将“最低 API 级别”设置为“API 19”或更高级别，然后选择“完成”  。
7. 在项目视图中，从下拉列表中选择“项目”以显示源和非源的项目文件，打开 app/build.gradle，并将 `targetSdkVersion` 设置为 `28` 。

## <a name="install-the-dependencies"></a>安装依赖项

在 Android Studio 项目窗口中，导航到 app > build.gradle，然后添加以下内容 ：

```gradle
apply plugin: 'com.android.application'

allprojects {
    repositories {
    mavenCentral()
    google()
    mavenLocal()
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    maven {
        name "vsts-maven-adal-android"
        url "https://identitydivision.pkgs.visualstudio.com/_packaging/AndroidADAL/maven/v1"
        credentials {
            username System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") : project.findProperty("vstsUsername")
            password System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") : project.findProperty("vstsMavenAccessToken")
        }
    }
    jcenter()
    }
}
dependencies{
    implementation 'com.microsoft.identity.client:msal:2.+'
    }
packagingOptions{
    exclude("META-INF/jersey-module-version")
}
```


## <a name="add-the-authentication-components"></a>添加身份验证组件

[示例代码](configure-authentication-sample-android-app.md#step-3-get-the-android-mobile-app-sample)由以下组件构成。 将示例 Android 应用中的这些组件添加到你自己的应用。 

|组件  |类型  | 源 |说明  |
|---------|---------|---------|---------|
| B2CUser| 类| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CUser.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CUser.java)| 表示 B2C 用户。 此类允许用户使用多个策略登录。 | 
| B2CModeFragment | 片段类| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CModeFragment.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CModeFragment.java)| 片段表示用于在主活动中使用 Azure AD B2C 用户界面进行登录的模块化部分。 此片段包含大部分身份验证代码。 |
| fragment_b2c_mode.xml | 片段布局| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) | 定义 B2CModeFragment 片段组件的用户界面结构。 |
| B2CConfiguration| 类| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CConfiguration.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CConfiguration.java)| 配置文件包含有关 Azure AD B2C 标识提供者的信息。 移动应用使用此信息来与 Azure AD B2C 建立信任关系、将用户登录和注销、获取令牌以及验证令牌。 有关更多配置设置，请参阅 auth_config_b2c.json 文件。  | 
|auth_config_b2c.json | JSON 文件| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/raw/auth_config_b2c.json) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/raw/auth_config_b2c.json)| 配置文件包含有关 Azure AD B2C 标识提供者的信息。 移动应用使用此信息来与 Azure AD B2C 建立信任关系、将用户登录和注销、获取令牌以及验证令牌。 有关更多配置设置，请参阅 B2CConfiguration 类。 | 

## <a name="configure-your-android-app"></a>配置 Android 应用

[添加身份验证组件](#add-the-authentication-components)后，使用 Azure AD B2C 设置配置 Android 应用。 Azure AD B2C 标识提供者设置是在 auth_config_b2c.json 文件和 B2CConfiguration 类中配置的。 

请按照有关如何[配置示例移动应用](configure-authentication-sample-android-app.md#step-5-configure-the-sample-mobile-app)的指导操作。

## <a name="set-the-redirect-uri"></a>设置重定向 URI

在本部分将会配置应用程序要在哪个位置侦听 Azure AD B2C 令牌响应。 


1. 生成新的开发签名哈希。 这会针对每个开发环境进行更改。
    
    在 Windows 操作系统上：
    
    ```
    keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    在 IOS 操作系统上：
    
    ```dotnetcli
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    对于生产环境，请使用以下命令：
    
    ```
    keytool -exportcert -alias SIGNATURE_ALIAS -keystore PATH_TO_KEYSTORE | openssl sha1 -binary | openssl base64
    ```

    如需有关为应用签名的更多帮助，请查看[为 Android 应用签名](https://developer.android.com/studio/publish/app-signing)。 

1. 在 app > src > main > AndroidManifest.xml 中，将以下 `BrowserTabActivity` 活动添加到应用程序主体   ：
    
    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Package_Name"
                android:path="/Signature_Hash" />
        </intent-filter>
    </activity>
    ```
1. 将 `Signature_Hash` 替换为生成的哈希。
1. 将 `Package_Name` 替换为 Android 包名称。
 
按照以下步骤使用应用重定向 URI 更新移动应用注册：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择 **应用注册**，然后在 [2.3 注册移动应用](configure-authentication-sample-android-app.md#23-register-the-mobile-app) 中选择已注册的应用程序。
1. 选择“身份验证”。
1. 在“Android”下，选择“添加 URI” 。
1. 填写“包名称”和“签名哈希” 。
1. 选择“保存”  。

重定向 URI 和 `BrowserTabActivity` 活动应如以下示例所示： 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

示例 Android 的重定向 URL：

```
msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

然后，意向筛选器将使用相同的模式，如以下 XML 代码片段中所示：

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:host="com.azuresamples.msalandroidkotlinapp"
            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
            android:scheme="msauth" />
    </intent-filter>
</activity>
```



#### <a name="java"></a>[Java](#tab/java)

示例 Android 的重定向 URL：

```
msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

然后，意向筛选器将使用相同的模式，如以下 XML 代码片段中所示：

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
<intent-filter>
    <action android:name="android.intent.action.VIEW" />

    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data
        android:host="com.azuresamples.msalandroidapp"
        android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
        android:scheme="msauth" />
</intent-filter>
</activity>
```

--- 

## <a name="code-building-blocks"></a>代码构建基块

本部分介绍用来为 Android 应用启用身份验证的代码构建基块。 下表列出了 B2CModeFragment 的方法，并说明了如何自定义代码。 

### <a name="instantiate-a-public-client-application"></a>实例化公共客户端应用程序

公共客户端应用程序不被信任能够安全保存应用程序机密，并且它们也不包含客户端机密。 在 [onCreate](https://developer.android.com/reference/android/app/Fragment#onCreate(android.os.Bundle)) 或 [onCreateView](https://developer.android.com/reference/android/app/Fragment#onCreateView(android.view.LayoutInflater,%20android.view.ViewGroup,%20android.os.Bundle)) 中，使用多个帐户的公共客户端应用程序对象实例化 MSAL。

`MultipleAccountPublicClientApplication` 类用于创建允许同时登录多个帐户的基于 MSAL 的应用。 它允许使用多个 Azure AD B2C 用户流或自定义策略登录。 例如，用户可以使用[注册或登录](add-sign-up-and-sign-in-policy.md)用户流登录，然后运行[编辑配置文件](add-profile-editing-policy.md)用户流。 

以下代码片段演示如何使用 `auth_config_b2c.json` 配置 JSON 文件启动 MSAL 库。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
PublicClientApplication.createMultipleAccountPublicClientApplication(context!!,
    R.raw.auth_config_b2c,
    object : IMultipleAccountApplicationCreatedListener {
        override fun onCreated(application: IMultipleAccountPublicClientApplication) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application
            // Load the account (if there is any)
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            // Error handling
            displayError(exception)
        }
    })
```

#### <a name="java"></a>[Java](#tab/java)

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
    R.raw.auth_config_b2c,
    new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication application) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application;

            // Load the account (if there is any)
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            // Error handling
            displayError(exception);
        }
    });
```

--- 

### <a name="load-accounts"></a>加载帐户

当应用进入前台时，它将加载现有帐户以确定用户是否已登录。  通过此方法可以使用身份验证状态更新 UI。 例如，启用或禁用注销按钮。

以下代码片段演示如何加载帐户。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private fun loadAccounts() {
    if (b2cApp == null) {
        return
    }
    b2cApp!!.getAccounts(object : LoadAccountsCallback {
        override fun onTaskCompleted(result: List<IAccount>) {
            users = B2CUser.getB2CUsersFromAccountList(result)
            updateUI(users)
        }
    
        override fun onError(exception: MsalException) {
            displayError(exception)
        }
    })
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private void loadAccounts() {
    if (b2cApp == null) {
        return;
    }

    b2cApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            users = B2CUser.getB2CUsersFromAccountList(result);
            updateUI(users);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

--- 

### <a name="interactive-authorization-request"></a>交互式授权请求

交互式授权请求是提示用户注册或登录的流。 `initializeUI` 方法配置 `runUserFlowButton` 单击事件。 当用户选择“运行用户流”按钮时，应用会将该用户带到 Azure AD B2C 以完成登录流。 

`runUserFlowButton.setOnClickListener` 方法使用授权请求的相关数据准备 `AcquireTokenParameters` 对象。 然后，`acquireToken` 方法提示用户完成注册或登录流。 

以下代码片段演示如何启动交互式授权请求。 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority(getAuthorityFromPolicyName(policy_list.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.scopes)
        .withPrompt(Prompt.LOGIN)
        .withCallback(authInteractiveCallback)
        .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(getActivity())
        .fromAuthority(B2CConfiguration.getAuthorityFromPolicyName(policyListSpinner.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.getScopes())
        .withPrompt(Prompt.LOGIN)
        .withCallback(getAuthInteractiveCallback())
        .build();

b2cApp.acquireToken(parameters);
```

--- 

 
### <a name="interactive-authorization-request-callback"></a>交互式授权请求回调

用户完成授权流后（成功或不成功），结果将返回给 `getAuthInteractiveCallback()` 回调方法。 

该回调方法传递 `AuthenticationResult` 对象，或在 `MsalException` 对象中传递错误消息。 使用此方法可以：

- 在登录完成后使用信息更新移动应用 UI
- 重载 accounts 对象
- 使用访问令牌调用 Web API 服务
- 处理身份验证错误

以下代码片段演示如何使用交互式身份验证回调。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private val authInteractiveCallback: AuthenticationCallback
    private get() = object : AuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            /* Successfully got a token, use it to call a protected resource; web API  */
            Log.d(TAG, "Successfully authenticated")

            /* display result info */
            displayResult(authenticationResult)

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            val B2C_PASSWORD_CHANGE = "AADB2C90118"
            if (exception.message!!.contains(B2C_PASSWORD_CHANGE)) {
                txt_log!!.text = """
                    The user clicks the 'Forgot Password' link in a sign-up or sign-in user flow.
                    Your application needs to handle this error code by running a specific user flow that resets the password.
                    """.trimIndent()
                return
            }

            /* Failed to acquireToken */Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        override fun onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.")
        }
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");

            /* display result info */
            displayResult(authenticationResult);

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            final String B2C_PASSWORD_CHANGE = "AADB2C90118";
            if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
                logTextView.setText("The user clicks the 'Forgot Password' link in a sign-up or sign-in user flow.\n" +
                        "Your application needs to handle this error code by running a specific user flow that resets the password.");
                return;
            }

            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

--- 

## <a name="call-a-web-api"></a>调用 Web API

若要调用[基于令牌的授权 Web API](enable-authentication-web-api.md)，应用需有有效的访问令牌。 应用将执行以下步骤：


1. 获取对 Web API 终结点拥有所需权限（范围）的访问令牌。
1. 使用以下格式，在 HTTP 请求的 authorization 头中将该访问令牌作为持有者令牌进行传递：

```http
Authorization: Bearer <access-token>
```

当用户[以交互方式登录](#interactive-authorization-request)时，应用将在 `getAuthInteractiveCallback` 回调方法中获取访问令牌。 要进行连续的 Web API 调用，请使用本部分所述的“获取令牌”无提示过程。 

在调用 Web API 之前，请使用 Web API 终结点的相应范围调用 `acquireTokenSilentAsync` 方法。 MSAL 库将执行以下步骤：

1. 尝试从令牌缓存中提取具有所请求范围的访问令牌。 如果该令牌存在，则返回该令牌。 
1. 如果令牌缓存中不存在该令牌，则 MSAL 将尝试使用其刷新令牌来获取新令牌。 
1. 如果该刷新令牌不存在或已过期，则返回异常。 建议提示用户[以交互方式登录](#interactive-authorization-request)。  

以下代码片段演示如何获取访问令牌：

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

`acquireTokenSilentButton` 按钮单击事件获取具有所提供范围的访问令牌。 

```kotlin
btn_acquireTokenSilently.setOnClickListener(View.OnClickListener {
    if (b2cApp == null) {
        return@OnClickListener
    }
    val selectedUser = users!![user_list.getSelectedItemPosition()]
    selectedUser.acquireTokenSilentAsync(b2cApp!!,
            policy_list.getSelectedItem().toString(),
            B2CConfiguration.scopes,
            authSilentCallback)
})
```

`authSilentCallback` 回调方法返回访问令牌并调用 Web API：

```kotlin
private val authSilentCallback: SilentAuthenticationCallback
    private get() = object : SilentAuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            Log.d(TAG, "Successfully authenticated")

            /* Call your web API here*/
            callWebAPI(authenticationResult)
        }

        override fun onError(exception: MsalException) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception is MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    }
```

以下示例演示如何使用持有者令牌调用受保护的 Web API：

```kotlin
@Throws(java.lang.Exception::class)
private fun callWebAPI(authenticationResult: IAuthenticationResult) {
    val accessToken = authenticationResult.accessToken
    val thread = Thread {
        try {
            val url = URL("https://your-app-service.azurewebsites.net/helo")
            val conn = url.openConnection() as HttpsURLConnection
            conn.setRequestProperty("Accept", "application/json")
            
            // Set the bearer token
            conn.setRequestProperty("Authorization", "Bearer $accessToken")
            if (conn.responseCode == HttpURLConnection.HTTP_OK) {
                val br = BufferedReader(InputStreamReader(conn.inputStream))
                var strCurrentLine: String?
                while (br.readLine().also { strCurrentLine = it } != null) {
                    Log.d(TAG, strCurrentLine)
                }
            }
            conn.disconnect()
        } catch (e: IOException) {
            e.printStackTrace()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
    thread.start()
}
```

#### <a name="java"></a>[Java](#tab/java)

`acquireTokenSilentButton` 按钮单击事件获取具有所提供范围的访问令牌。 

```java
acquireTokenSilentButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        if (b2cApp == null) {
            return;
        }

        final B2CUser selectedUser = users.get(b2cUserList.getSelectedItemPosition());
        selectedUser.acquireTokenSilentAsync(b2cApp,
                policyListSpinner.getSelectedItem().toString(),
                B2CConfiguration.getScopes(),
                getAuthSilentCallback());
    }
});
```

`authSilentCallback` 回调方法返回访问令牌并调用 Web API：

```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Call your web API here*/
            callWebAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

以下示例演示如何使用持有者令牌调用受保护的 Web API：

```java
private void callWebAPI(IAuthenticationResult authenticationResult) throws Exception {
    final String accessToken = authenticationResult.getAccessToken();
    
    
    Thread thread = new Thread(new Runnable() {
    
        @Override
        public void run() {
            try {
                URL url = new URL("https://your-app-service.azurewebsites.net/helo");
                HttpsURLConnection conn = (HttpsURLConnection)url.openConnection();
                conn.setRequestProperty("Accept", "application/json");
                
                // Set the bearer token
                conn.setRequestProperty("Authorization", "Bearer " +  accessToken);
    
                if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                    String strCurrentLine;
                    while ((strCurrentLine = br.readLine()) != null) {
                        Log.d(TAG, strCurrentLine);
                    }
                }
                conn.disconnect();
            } catch (IOException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
    
    thread.start();
    
    }
```

--- 

### <a name="add-permission-to-perform-network-operations"></a>添加用于执行网络操作的权限

若要在应用程序中执行网络操作，请在清单中包含以下权限。 有关详细信息，请参阅[连接到网络](https://developer.android.com/training/basics/network-ops/connecting)。

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

## <a name="next-steps"></a>后续步骤

* [在 Android 应用程序中配置身份验证选项](enable-authentication-android-app-options.md)
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)
