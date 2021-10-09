---
title: 教程：创建使用 Microsoft 标识平台进行身份验证的 Android 应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，我们生成一个使用 Microsoft 标识平台登录用户的 Android 应用，并获取访问令牌以代表用户调用 Microsoft Graph API。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: marsma
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40, has-adal-ref
ms.openlocfilehash: 0c79ad939a28913a13193b2743c6fda98f553663
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587074"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>教程：从 Android 应用程序登录用户并调用 Microsoft Graph API

在本教程中，你要构建一个与 Microsoft 标识平台集成的 Android 应用，用户可登录该应用并获取访问令牌以调用 Microsoft Graph API。

完成本教程后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。

在本教程中： 

> [!div class="checklist"]
> * 在 Android Studio 中创建 Android 应用项目
> * 在 Azure 门户中注册应用
> * 添加代码以支持用户登录和注销
> * 添加代码以调用 Microsoft Graph API
> * 测试应用程序

## <a name="prerequisites"></a>先决条件

* Android Studio 3.5+

## <a name="how-this-tutorial-works"></a>本教程工作原理

![显示本教程生成的示例应用的工作原理](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

本教程中的应用会将用户登录并代表他们获取数据。 该数据可通过一个受保护的 API (Microsoft 图形 API) 进行访问，该 API 需要授权并且受 Microsoft 标识平台保护。

更具体说来：

* 你的应用将通过浏览器或 Microsoft Authenticator 和 Intune 公司门户登录用户。
* 最终用户将接受应用程序请求的权限。
* 将为你的应用颁发 Microsoft Graph API 的一个访问令牌。
* 该访问令牌将包括在对 Web API 的 HTTP 请求中。
* 处理 Microsoft Graph 响应。

该示例使用适用于 Android 的 Microsoft 身份验证库 (MSAL) 来实现身份验证：[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)。

MSAL 将自动续订令牌，在设备上的其他应用之间提供单一登录 (SSO)，并管理帐户。

本教程演示简化的示例，介绍如何使用适用于 Android 的 MSAL。 为简单起见，本教程仅使用“单帐户模式”。 若要探索更复杂的场景，请参阅 GitHub 上已完成的[工作代码示例](https://github.com/Azure-Samples/ms-identity-android-java/)。

## <a name="create-a-project"></a>创建一个项目
如果你还没有 Android 应用程序，请按照以下步骤设置新项目。

1. 打开 Android Studio，然后选择“启动新的 Android Studio 项目”  。
2. 选择“基本活动”，再选择“下一步”   。
3. 命名应用程序。
4. 保存包名称。 以后需将它输入 Azure 门户中。
5. 将语言从“Kotlin”  更改为“Java”  。
6. 将“最低 API 级别”  设置为 **API 19** 或更高，然后单击“完成”。 
7. 在项目视图的下拉列表中选择“项目”  ，以便显示源和非源的项目文件，然后打开 **app/build.gradle**，将 `targetSdkVersion` 设置为 `28`。

## <a name="integrate-with-the-microsoft-authentication-library"></a>与 Microsoft 身份验证库集成

### <a name="register-your-application"></a>注册应用程序

1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
1. 搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册” > “新建注册”  。
1. 输入应用程序的 **名称**。 应用的用户可能会看到此名称，你稍后可对其进行更改。
1. 选择“注册”  。
1. 在“管理”下，选择“身份验证” > “添加平台” > “Android”   。
1. 输入项目的包名称。 如果下载了代码，则该值为 `com.azuresamples.msalandroidapp`。
1. 在“配置 Android 应用”页的“签名哈希”部分，选择“生成开发签名哈希”。   然后复制用于平台的 KeyTool 命令。


     安装 KeyTool.exe，使其作为 Java 开发工具包 (JDK) 的一部分。 还必须安装 OpenSSL 工具才能执行 KeyTool 命令。 有关详细信息，请参阅[有关如何生成密钥的 Android 文档](https://developer.android.com/studio/publish/app-signing#generate-key)。

1. 生成由 KeyTool 生成的 **签名哈希**。
1. 选择“配置”并保存出现在“Android 配置”页中的“MSAL 配置”，以便在稍后配置应用时输入它  。  
1. 选择“完成”。

### <a name="configure-your-application"></a>配置应用程序

1. 在 Android Studio 的项目窗格中，导航到 **app\src\main\res**。
1. 右键单击“res”  ，选择“新建”   >   “目录”。 输入 `raw` 作为新目录名称，然后单击“确定”。 
1. 在 **app** > **src** > **main** > **res** > **raw** 中，新建名为 `auth_config_single_account.json` 的 JSON 文件，然后粘贴以前保存的 MSAL 配置。

    在“重定向 URI”下方，粘贴：
    ```json
      "account_mode" : "SINGLE",
    ```
    配置文件应与如下示例类似：
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```

     本教程仅演示如何在单帐户模式下配置应用。 查看文档，详细了解[单帐户模式与多帐户模式](./single-multi-account.md)以及[配置应用](./msal-configuration.md)

4. 在 **app** > **src** > **main** > **AndroidManifest.xml** 中，将以下 `BrowserTabActivity` 活动添加到应用程序主体。 该条目允许 Microsoft 在完成身份验证后回调应用程序：

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
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

    将 `android:host=` 值替换为在 Azure 门户中注册的包名称。
    将 `android:path=` 值替换为在 Azure 门户中注册的密钥哈希。 签名哈希不应进行 URL 编码  。 确保签名哈希的开头有前导 `/`。
    
    将用来替换 `android:host` 值的“包名称”应类似于：“com.azuresamples.msalandroidapp”。
    将用来替换 `android:path` 值的“签名哈希”应类似于：“/1wIqXSqBj7w+h11ZifsnqwgyKrY=”。
    
    还可以在应用注册的“身份验证”边栏选项卡中找到这些值。 请注意，重定向 URI 将如下所示：“msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D”。 尽管签名哈希会在此值末尾进行 URL 编码，但签名哈希不应在 `android:path` 值中进行 URL 编码  。

## <a name="use-msal"></a>使用 MSAL

### <a name="add-msal-to-your-project"></a>将 MSAL 添加到项目

1. 在 Android Studio 项目窗口中，导航到 app > build.gradle，然后添加以下内容 ：

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
     implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
     }
    packagingOptions{
     exclude("META-INF/jersey-module-version")
    }
    ```
    [有关 Microsoft Graph SDK 的详细信息](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>要求的导入

将以下内容添加到 app > src > main> java > com.example(yourapp) > MainActivity.java 的顶部      

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>实例化 PublicClientApplication
#### <a name="initialize-variables"></a>初始化变量
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
在 `MainActivity` 类中，参阅下方的 onCreate() 方法以使用 `SingleAccountPublicClientApplication` 实例化 MSAL。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
侦听按钮并相应地调用方法或日志错误。
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> 使用 MSAL 注销会从应用程序中删除有关用户的所有已知信息，但是用户的设备上仍然有一个活动会话。 如果用户尝试再次登录，则可能会看到登录 UI，但由于设备会话仍处于活动状态，可能无需重新输入其凭据。

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
用于交互式请求的回调。

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
用于无提示请求的回调
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>调用 Microsoft Graph API

以下代码演示如何使用 Graph SDK 调用 GraphAPI。

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>添加 UI
### <a name="activity"></a>活动
如果要根据本教程为 UI 建模，则以下方法可提供有关更新文本和侦听按钮的指导。

#### <a name="updateui"></a>updateUI
根据登录状态启用/禁用按钮，并设置文本。
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
在 UI 中更新文本以表示注销的方法。

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>布局

示例 `activity_main.xml` 文件，显示按钮和文本框。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>测试应用

### <a name="run-locally"></a>在本地运行

构建应用并将其部署到测试设备或模拟器。 你应能够登录并获取 Azure AD 或个人 Microsoft 帐户的令牌。

你登录后，此应用将显示从 Microsoft Graph `/me` 终结点返回的数据。
PR 4
### <a name="consent"></a>同意

任何用户首次登录你的应用时，Microsoft 标识都将提示他们同意所请求的权限。 某些 Azure AD 租户已禁用用户同意功能，这要求管理员代表所有用户同意。 若要支持此场景，需创建自己的租户或获得管理员的同意。

## <a name="clean-up-resources"></a>清理资源

如果不再需要，请删除[注册应用程序](#register-your-application) 步骤中创建的应用对象。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

在我们的多部分场景系列中，详细了解如何构建可调用受保护 Web API 的移动应用。

> [!div class="nextstepaction"]
> [方案：用于调用 Web API 的移动应用程序](scenario-mobile-overview.md)
