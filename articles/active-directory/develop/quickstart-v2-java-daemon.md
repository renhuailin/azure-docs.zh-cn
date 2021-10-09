---
title: 快速入门：从 Java 守护程序调用 Microsoft Graph | Microsoft
titleSuffix: Microsoft identity platform
description: 在本快速入门中，你将学习 Java 应用如何使用应用自己的标识获取访问令牌并调用受 Microsoft 标识平台终结点保护的 API
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 519ef3c4e53eb55bd00957d1d825c96375e241ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604834"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>快速入门：使用应用的标识获取令牌并从 Java 控制台应用中调用 Microsoft Graph API

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Java 应用程序如何使用应用的标识获取访问令牌来调用 Microsoft Graph API 并在目录中显示[用户列表](/graph/api/user-list)。 代码示例演示无人参与的作业或 Windows 服务如何使用应用程序标识而不是用户标识运行。

> [!div renderon="docs"]
> ![显示本快速入门生成的示例应用的工作原理](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>先决条件

若要运行此示例，需要：

- [Java 开发工具包 (JDK)](https://openjdk.java.net/) 8 或更高版本
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用

> [!div renderon="docs" class="sxs-lookup"]
>
> 可以使用两个选项来启动快速入门应用程序：“快速”（下面的选项 1）和“手动”（选项 2）
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 遵照说明下载内容，并只需单击一下自动配置新应用程序。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的名称（例如 `Daemon-console`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 选择“注册”  。
> 1. 在“管理”下，选择“证书和机密” 。
> 1. 在“客户端机密”下，选择“新建客户端机密”，输入名称，然后选择“添加”  。 将机密值记录在安全的位置，以供在后面的步骤中使用。
> 1. 在“管理”下，选择“API 权限” > “添加权限”  。 选择“Microsoft Graph”。
> 1. 选择“应用程序权限”。
> 1. 在“用户”节点下选择“User.Read.All”，然后选择“添加权限”  。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>下载并配置快速入门应用
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使本快速入门的代码示例正常运行，需创建客户端机密，并添加 Graph API 的 **User.Read.All** 应用程序权限。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-netcore-daemon/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-java-project"></a>步骤 2：下载 Java 项目

> [!div renderon="docs"]
> [下载 Java 守护程序项目](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>步骤 3：配置 Java 项目
>
> 1. 将 zip 文件提取到靠近磁盘根目录的本地文件夹，例如 *C:\Azure-Samples*。
> 1. 导航到子文件夹 msal-client-credential-secret。
> 1. 编辑 src\main\resources\application.properties，并将字段 `AUTHORITY`、`CLIENT_ID` 和 `SECRET` 的值替换为以下代码片段：
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    其中：
>    - `Enter_the_Application_Id_Here` - 是已注册应用程序的 **应用程序（客户端）ID**。
>    - `Enter_the_Tenant_Id_Here` - 将此值替换为租户 ID 或租户名称（例如 contoso.microsoft.com） 。
>    - `Enter_the_Client_Secret_Here` - 将此值替换为在步骤 1 中创建的客户端机密。
>
> > [!TIP]
> > 若要查找“应用程序(客户端) ID”、“目录(租户) ID”的值，请转到 Azure 门户中应用的“概览”页。   若要生成新密钥，请转到“证书和机密”页。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>步骤 3：管理员同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>步骤 4：管理员同意

如果尝试在此时运行应用程序，则会收到“HTTP 403 - 禁止访问”错误：`Insufficient privileges to complete the operation`。 之所以出现这种错误，是因为任何仅限应用的权限都需要管理员许可：目录的全局管理员必须为应用程序授予许可。 根据自己的角色选择下面的一个选项：

##### <a name="global-tenant-administrator"></a>全局租户管理员

> [!div renderon="docs"]
> 如果你是全局租户管理员，请在 Azure 门户中转到“应用注册”中的“API 权限”页面，选择“为 {租户名称} 授予管理员许可”（其中，{租户名称} 是目录的名称）  。

> [!div renderon="portal" class="sxs-lookup"]
> 如果你是全局管理员，请转到“API 权限”页面，选择“为 Enter_the_Tenant_Name_Here 授予管理员许可” 。
> > [!div id="apipermissionspage"]
> > [转到“API 权限”页]()

##### <a name="standard-user"></a>标准用户

如果你是租户的标准用户，则需请求全局管理员为你的应用程序授予管理员许可。 为此，请将以下 URL 提供给管理员：

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> 其中：
>> * `Enter_the_Tenant_Id_Here` - 将此值替换为 **租户 ID** 或 **租户名称**（例如 contoso.microsoft.com）
>> * `Enter_the_Application_Id_Here` - 是已注册应用程序的 **应用程序（客户端）ID**。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>步骤 4：运行应用程序

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>步骤 5：运行应用程序

可通过从 IDE 中运行 ClientCredentialGrant.java 的主方法，直接测试示例。

从 shell 或命令行：

```
$ mvn clean compile assembly:single
```

这将在 /targets 目录中生成一个 msal-client-credential-secret-1.0.0.jar 文件。 使用 Java 可执行文件运行此内容，如下所示：

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

运行后，应用程序应显示配置的租户中的用户列表。


> [!IMPORTANT]
> 本快速入门应用程序使用客户端机密将自己标识为机密客户端。 由于客户端机密是以纯文本形式添加到项目文件的，因此为了安全起见，建议在考虑将应用程序用作生产应用程序之前，使用证书来代替客户端机密。 若要详细了解如何使用证书，请查看有关此示例的[这些说明](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate)，它们位于同一 GitHub 存储库中，但在另一个文件夹“msal-client-credential-certificate”中。

## <a name="more-information"></a>详细信息

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) 是一个用于登录用户和请求令牌的库，此类令牌用于访问受 Microsoft 标识平台保护的 API。 如前所述，本快速入门请求令牌的方法是使用应用程序自身的标识而不是委托的权限。 在此示例中使用的身份验证流称为[客户端凭据 oauth 流](v2-oauth2-client-creds-grant-flow.md)。 若要详细了解如何搭配使用 MSAL Java 和守护程序应用，请参阅[本文](scenario-daemon-overview.md)。

可以使用 Maven 或 Gradle 将 MSAL4J 添加到应用程序，以通过对应用程序的 pom.xml (Maven) 或 build.gradle (Gradle) 文件进行以下更改来管理依赖项。

在 pom.xml 中：

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

在 build.gradle 中：

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL 初始化

通过将以下代码添加到要在其中使用 MSAL4J 的文件的顶部，来添加对 MSAL for Java 的引用：

```Java
import com.microsoft.aad.msal4j.*;
```

然后，使用以下代码对 MSAL 进行初始化：

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | 其中： |说明 |
> |---------|---------|
> | `CLIENT_SECRET` | 是在 Azure 门户中为应用程序创建的客户端机密。 |
> | `CLIENT_ID` | 是在 Azure 门户中注册的应用程序的 **应用程序(客户端) ID**。 可以在 Azure 门户的应用的“概览”页中找到此值。 |
> | `AUTHORITY`    | 用户要进行身份验证的 STS 终结点。 对于公有云，通常为 `https://login.microsoftonline.com/{tenant}`，其中 {tenant} 是租户名称或租户 ID。|

### <a name="requesting-tokens"></a>请求令牌

若要通过应用的标识来请求令牌，请使用 `acquireToken` 方法：

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |其中：| 说明 |
> |---------|---------|
> | `SCOPE` | 包含请求的范围。 对于机密客户端，这应该使用与 `{Application ID URI}/.default` 类似的格式，指示所请求的范围是在 Azure 门户的应用对象集中静态定义的范围（就 Microsoft Graph 来说，`{Application ID URI}` 指向 `https://graph.microsoft.com`）。 对于自定义 Web API，`{Application ID URI}` 是在 Azure 门户的“应用注册”的“公开 API”部分中定义的 。|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解守护程序应用程序，请参阅方案登陆页面。

> [!div class="nextstepaction"]
> [调用 Web API 的守护程序应用程序](scenario-daemon-overview.md)
