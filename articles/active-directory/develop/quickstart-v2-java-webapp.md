---
title: 快速入门：向 Java Web 应用添加 Microsoft 登录功能 | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入门中，你将了解如何使用 OpenID Connect 向 Java Web 应用添加 Microsoft 登录功能。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196500eb70a559339bd237670434ad8f562eda8c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551163"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>快速入门：向 Java Web 应用添加 Microsoft 登录功能

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Java Web 应用程序如何让用户登录并调用 Microsoft Graph API。 任何 Azure Active Directory (Azure AD) 组织的用户都可以登录到应用程序。

 有关概述，请参阅[示例工作原理关系图](#how-the-sample-works)。

## <a name="prerequisites"></a>先决条件

若要运行此示例，需要：

- [Java 开发工具包 (JDK)](https://openjdk.java.net/) 8 或更高版本。 
- [Maven](https://maven.apache.org/)。

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用
> 可以通过两种方式来启动快速入门应用程序：“快速”（选项 1）和“手动”（选项 2）。
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 按照门户快速入门体验中的说明下载自动配置的应用程序代码。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 若要注册应用程序并手动向其中添加应用的注册信息，请执行以下步骤：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册”。
> 1. 选择“新注册”。
> 1. 输入应用程序的名称（例如 java-webapp） 。 应用的用户可能会看到此名称。 稍后可对其进行更改。
> 1. 选择“注册”。
> 1. 在“概述”页面上，记下“应用程序(客户端) ID”和“目录(租户) ID”  。 在后面的步骤中会用到这些值。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 选择“添加平台” > “Web” 。
> 1. 在“重定向 URI”部分中，输入 `https://localhost:8443/msal4jsample/secure/aad`。
> 1. 选择“配置” 。
> 1. 在“Web”部分的“重定向 URI”下，输入 `https://localhost:8443/msal4jsample/graph/me` 作为第二个重定向 URI 。
> 1. 在“管理”下，选择“证书和机密”。   在“客户端密码”部分中，选择“新建客户端密码” 。
> 1. 输入密钥说明（例如应用机密），保留默认的到期日期，然后选择“添加”。
> 1. 记下客户端密码的值。 稍后需要用到此信息。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
>
> 若要使用此快速入门中的代码示例：
>
> 1. 添加回复 URL `https://localhost:8443/msal4jsample/secure/aad` 和 `https://localhost:8443/msal4jsample/graph/me`。
> 1. 创建客户端机密。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-aspnet-webapp/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-code-sample"></a>步骤 2：下载代码示例
> [!div renderon="docs"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 下载项目并将 .zip 文件解压缩到驱动器根目录附近的文件夹中。 例如，C:\Azure-Samples。
>
> 若要将 HTTPS 与 localhost 一起使用，请提供 `server.ssl.key` 属性。 若要生成自签名证书，请使用 keytool 实用工具（包含在 JRE 中）。
>
> 下面是一个示例：
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   将生成的 keystore 文件放在“resources”文件夹中。

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>步骤 3：配置代码示例
> 1. 将 zip 文件解压缩到某个本地文件夹。
> 1. *可选。* 如果使用集成开发环境，请在该环境中打开示例。
> 1. 打开 *application.properties* 文件。 可以在 src/main/resources/ 文件夹中找到它。 将字段 `aad.clientId`、`aad.authority` 和 `aad.secretKey` 中的值分别替换为应用程序 ID、租户 ID 和客户端密码值。 下面是结果的大致形式：
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    在前面的代码中：
>
>    - `Enter_the_Application_Id_here` 是已注册应用程序的应用程序 ID。
>    - `Enter_the_Client_Secret_Here` 是你在“证书和机密”中为注册的应用程序创建的客户端机密 。
>    - `Enter_the_Tenant_Info_Here` 是注册的应用程序的目录（租户）ID 值。
> 1. 若要将 HTTPS 与 localhost 一起使用，请提供 `server.ssl.key` 属性。 若要生成自签名证书，请使用 keytool 实用工具（包含在 JRE 中）。
>
>    下面是一个示例：
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. 将生成的 keystore 文件放在“resources”文件夹中。


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>步骤 3：运行代码示例
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>步骤 4：运行代码示例

若要运行项目，请执行以下步骤之一：

- 使用嵌入式 Spring Boot 服务器直接从 IDE 运行它。 
- 使用 [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) 将其打包为 WAR 文件，然后将其部署到 J2EE 容器解决方案，如 [Apache Tomcat](http://tomcat.apache.org/)。

##### <a name="running-the-project-from-an-ide"></a>从 IDE 中运行项目

若要从 IDE 运行 Web 应用，请选择“运行”，然后转到项目的主页。 对于本示例，标准主页 URL 为 https://localhost:8443 。

1. 在前面的页面上，选择“登录”按钮将用户重定向到 Azure Active Directory 并提示他们输入凭据。

1. 用户经过身份验证后，会被重定向到 `https://localhost:8443/msal4jsample/secure/aad`。 他们现在已登录，页面将显示有关用户帐户的信息。 示例 UI 包含以下按钮：
    - **注销**：从应用程序中注销当前用户，并将该用户重定向到主页。
    - **显示用户信息**：获取 Microsoft Graph 的令牌，并使用包含该令牌的请求调用 Microsoft Graph，这将返回有关已登录用户的基本信息。

##### <a name="running-the-project-from-tomcat"></a>从 Tomcat 中运行项目

若要将 Web 示例部署到 Tomcat，请对源代码进行一些更改。

1. 打开 ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication。

    - 删除所有源代码并用以下代码替换：

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

2.   Tomcat 的默认 HTTP 端口是 8080，但需要通过端口 8443 建立 HTTPS 连接。 配置此设置：
        - 转到 tomcat/conf/server.xml。
        - 搜索 `<connector>` 标记，并用此连接器替换现有连接器：

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

3. 打开命令提示符窗口。 转到此示例的根文件夹（pom.xml 位于其中），然后运行 `mvn package` 以生成项目。
    - 此命令将在 /targets 目录中生成一个 msal-web-sample-0.1.0.war 文件 。
    - 将此文件重命名为 msal4jsample.war。
    - 使用 Tomcat 或任何其他 J2EE 容器解决方案部署 WAR 文件。
        - 若要部署 msal4jsample.war 文件，请将其复制到 Tomcat 安装中的 /webapps/ 目录，然后启动 Tomcat 服务器。

4. 部署完文件后，使用浏览器转到 https://localhost:8443/msal4jsample 。


> [!IMPORTANT]
> 本快速入门应用程序使用客户端机密将自己标识为机密客户端。 由于客户端机密是以纯文本形式添加到项目文件的，因此为了安全起见，建议在生产环境中使用应用程序之前，使用证书来代替客户端机密。 有关如何使用证书的详细信息，请参阅[用于应用程序身份验证的证书凭据](./active-directory-certificate-credentials.md)。

## <a name="more-information"></a>详细信息

### <a name="how-the-sample-works"></a>示例工作原理
![显示本快速入门生成的示例应用程序的工作原理的示意图。](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>获取 MSAL

MSAL for Java (MSAL4J) 是一个 Java 库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft 标识平台保护的 API。

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

### <a name="initialize-msal"></a>初始化 MSAL

通过将以下代码添加到要在其中使用 MSAL4J 的文件的开头，来添加对 MSAL for Java 的引用：

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

若要深入了解如何生成在 Microsoft 标识平台上将用户登录的 Web 应用，请参阅多部分方案系列：

> [!div class="nextstepaction"]
> [场景：可将用户登录的 Web 应用](scenario-web-app-sign-user-overview.md?tabs=java)
