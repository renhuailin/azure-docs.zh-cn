---
title: 通过阶层配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 whoIam 集成以进行用户验证
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c7f7f162355b919c395dd0ee6d03b2bc5526e3da
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936686"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>本教程介绍如何使用阶层扩展 Azure AD B2C 以保护本地应用程序

在此示例教程中，了解如何通过阶层的 [Maverics 标识协调](https://www.strata.io/maverics-identity-orchestrator/)器将 AZURE ACTIVE DIRECTORY (AD) B2C 集成。
Maverics Identity Orchestrator 扩展 Azure AD B2C 以保护本地应用程序。 它连接到任何标识系统，以透明方式迁移用户和凭据，同步策略和配置，以及抽象化身份验证和会话管理。 使用阶层企业可以从旧到 Azure AD B2C 快速转换，而无需重写应用程序。 该解决方案具有以下优点：

- **客户单一 Sign-On (SSO) 本地混合应用**： Azure AD B2C 支持 Maverics 标识 Orchestrator 的客户 SSO。 用户以 Azure AD B2C 或社交标识提供者中承载的帐户登录 (IdP) 。 Maverics 扩展了 SSO，使其一直由旧式标识系统（如 Symantec SiteMinder）保护的应用程序。

- **在不重写的情况下将基于标准的 SSO 扩展到应用程序**：使用 Azure AD B2C 管理用户访问权限，并使用 Maverics IDENTITY Orchestrator SAML 或 OIDC 连接器启用 SSO。

- **轻松配置**： Azure AD B2C 提供了一个简单的分步用户界面，用于将 Maverics IDENTITY Orchestrator SAML 或 OIDC 连接器连接到 Azure AD B2C。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md) 。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)的实例，用于存储 Maverics 标识 Orchestrator 使用的机密。 它用于连接到 Azure AD B2C 或其他属性提供程序，例如轻型目录访问协议 (LDAP) 目录或数据库。

- 在 Azure 虚拟机或所选的本地服务器中安装并运行的 [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) 的实例。 有关如何获取软件和访问安装和配置文档的信息，请联系 [阶层](https://www.strata.io/contact/)

- 要从旧标识系统转换到 Azure AD B2C 的本地应用程序。

## <a name="scenario-description"></a>方案描述

阶层的 Maverics 集成包括以下组件：

- **Azure AD B2C**：负责验证用户凭据的授权服务器。 经过身份验证的用户可以使用 Azure AD B2C 目录中存储的本地帐户访问本地应用。

- **外部社交或企业 IdP**：可以是任何 OpenID connect 提供程序、Facebook、Google 或 GitHub。 请参阅有关在 Azure AD B2C 中使用 [External idp](./technical-overview.md#external-identity-providers) 的信息。  

- **阶层的 Maverics Identity Orchestrator**：协调用户登录并以透明方式通过 HTTP 标头将标识传递到应用的服务。

下面的体系结构关系图显示了实现。

![Image 显示了与阶层 Maverics 集成的 Azure AD B2C 的体系结构，以便能够访问混合应用程序](./media/partner-strata/strata-architecture-diagram.png)

| 步骤 | 说明 |
|:-------|:---------------|
| 1. | 用户发出访问本地托管应用程序的请求。 Maverics Identity Orchestrator 将用户发出的请求代理到应用程序。|
| 2. | Orchestrator 检查用户的身份验证状态。 如果未收到会话令牌，或者提供的会话令牌无效，则它会将用户发送到 Azure AD B2C 进行身份验证。|
| 3. | Azure AD B2C 将身份验证请求发送到配置的社交 IdP。|
| 4. | IdP 为用户提供凭据。 根据 IdP，用户可能需要执行多重身份验证， (MFA) 。|
| 5. | IdP 将身份验证响应发送回 Azure AD B2C。 （可选）在此步骤中，用户可以在 Azure AD B2C 目录中创建本地帐户。|
| 6. | Azure AD B2C 将用户请求发送到在 Azure AD B2C 租户中的 Orchestrator 应用注册过程中指定的终结点。|
| 7. | Orchestrator 会评估访问策略，并计算要包含在转发到应用的 HTTP 标头中的属性值。 在此步骤中，协调器可能会调用其他属性提供程序，以检索正确设置标头值所需的信息。 Orchestrator 设置标头值并将请求发送到应用。|
| 8. | 用户现在已经过身份验证，并且有权访问该应用程序。|

## <a name="get-maverics-identity-orchestrator"></a>获取 Maverics 标识 Orchestrator
若要获取将用于将旧的本地应用与 Azure AD B2C 集成的软件，请联系 [阶层](https://www.strata.io/contact/)。 获取软件后，请按照以下步骤来确定特定于 Orchestrator 的系统必备组件，并执行所需的安装和配置步骤。

## <a name="configure-your-azure-ad-b2c-tenant"></a>配置 Azure AD B2C 租户

1. **注册应用程序**

   a. [将 Orchestrator 注册为](./tutorial-register-applications.md?tabs=app-reg-ga) Azure AD B2C 租户中的应用程序。
   >[!Note]
   >稍后在配置 Orchestrator 实例时，你将需要租户名称和标识符、客户端 ID、客户端机密、配置的声明和重定向 URI。

   b. 向 Microsoft MS 图形 API 授予你的应用程序的权限。 你的应用程序将需要以下权限： `offline_access` 、 `openid` 。

   c. 添加应用程序的重定向 URI。 此 URI 将匹配 `oauthRedirectURL` Orchestrator 的 Azure AD B2C 连接器配置的参数，例如 `https://example.com/oidc-endpoint` 。

2. **创建用户流**：创建 [注册和登录用户流](./tutorial-create-user-flows.md)。

3. **添加 IdP**：选择使用本地帐户或社交或企业 [IdP](./tutorial-add-identity-providers.md)登录用户。

4. **定义用户属性**：定义要在注册期间收集的属性。

5. **指定应用程序声明**：指定要通过 Orchestrator 实例返回到应用程序的属性。 Orchestrator 使用 Azure AD B2C 返回的声明中的属性，并且可以从其他连接的标识系统（如 LDAP 目录和数据库）中检索其他属性。 在 HTTP 标头中设置这些属性，并将其发送到上游本地应用程序。

## <a name="configure-maverics-identity-orchestrator"></a>配置 Maverics Identity Orchestrator

在以下部分中，我们将引导你完成配置 Orchestrator 实例所需的步骤。 有关其他支持和文档，请联系 [阶层](https://www.strata.io/contact/)。

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Identity Orchestrator 服务器要求

你可以在任何服务器上运行 Orchestrator 实例，无论是在本地还是在公共云基础结构中通过 Azure、AWS 或 GCP 等提供程序运行。

- OS： REHL 7.7 或更高版本，CentOS 7 +

- 磁盘： 10 GB (small) 

- 内存： 16 GB

- 端口： 22 (SSH/SCP) ，443，80

- 安装/管理任务的根访问权限

- Maverics Identity Orchestrator 在下以用户身份运行 `maverics``systemd`

- 从托管 Maverics Identity Orchestrator 的服务器传出的网络，能够访问 Azure AD 租户。

### <a name="install-maverics-identity-orchestrator"></a>安装 Maverics Identity Orchestrator

1. 获取最新的 Maverics RPM 包。 将包放置在要安装 Maverics 的系统上。 如果要将文件复制到远程主机， [SCP](https://www.ssh.com/ssh/scp/) 是一个非常有用的工具。

2. 若要安装 Maverics 包，请运行以下命令，将文件名替换为 `maverics.rpm` 。

   `sudo rpm -Uvf maverics.rpm`

   默认情况下，Maverics 安装在 `/usr/local/bin` 目录中。

3. 安装 Maverics 后，它将作为服务在 `systemd` 下运行。  若要验证 Maverics 服务是否正在运行，请运行以下命令：

   `sudo service maverics status`

  如果 Orchestrator 安装成功，您应看到类似于下面的消息：

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. 如果 Maverics 服务无法启动，请执行以下命令来调查该问题：

   `journalctl --unit=maverics.service --reverse`

   最新日志项将出现在输出的开头。

安装 Maverics 后，将在 `/etc/maverics` 目录中创建默认的 `maverics.yaml` 文件。

将 Orchestrator 配置为保护应用程序。 与 Azure AD B2C、存储和检索 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9)的机密相集成。 定义 Orchestrator 应该从中读取其配置的位置。

### <a name="supply-configuration-using-environment-variables"></a>使用环境变量提供配置

通过环境变量向 Orchestrator 实例提供配置。

`MAVERICS_CONFIG`

此环境变量告诉 Orchestrator 实例 YAML 要使用的配置文件，以及在启动或重新启动过程中查找这些文件的位置。 在中设置环境变量 `/etc/maverics/maverics.env` 。

### <a name="create-the-orchestrators-tls-configuration"></a>创建 Orchestrator 的 TLS 配置

`tls`中的字段 `maverics.yaml` 声明 Orchestrator 实例将使用的传输层安全性配置。 连接器可以使用 TLS 对象和 Orchestrator 服务器。

`maverics` 密钥是为 Orchestrator 服务器保留的。 其他所有密钥均可用，并且可用于将 TLS 对象注入到给定连接器中。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>配置 Azure AD B2C 连接器

协调器使用连接器与身份验证和属性提供程序集成。 在这种情况下，此协调器应用程序网关使用 Azure AD B2C 连接器作为身份验证和属性提供程序。 Azure AD B2C 使用社交 IdP 进行身份验证，然后充当 Orchestrator 的属性提供程序，在 HTTP 标头中的声明集中传递属性。  

此连接器的配置对应于在 Azure AD B2C 租户中注册的应用。

1. 从租户中的应用配置中复制客户端 ID、机密和重定向 URI。

2. 为连接器指定名称，此处显示为 `azureADB2C` ，并将连接器设置 `type` 为 `azure` 。 记下连接器名称，因为此值用于以下其他配置参数。

3. 对于此集成， `authType` 应将设置为 `oidc` 。

4. 将在步骤1中复制的客户端 ID 设置为参数的值 `oauthClientID` 。

5. 将你在步骤1中复制的客户端密码设置为参数的值 `oauthClientSecret` 。

6. 将步骤1中复制的 "重定向 URI" 设置为参数的值 `oauthRedirectURL` 。

7. Azure AD B2C OIDC 连接器使用众所周知的 OIDC 终结点来发现元数据，包括 Url 和签名密钥。 将的值设置 `oidcWellKnownURL` 为租户的终结点。

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>将 Azure AD B2C 定义为身份验证提供程序

身份验证提供程序确定如何针对未在应用程序资源请求中提供有效会话的用户执行身份验证。 Azure AD B2C 租户中的配置确定如何质询用户的凭据并应用其他身份验证策略。 例如，要需要另一个因素来完成身份验证过程，并决定在身份验证成功后应将哪些声明返回到 Orchestrator 应用网关。

的值 `authProvider` 必须与连接器的 `name` 值匹配。

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>使用 Orchestrator 应用程序网关保护本地应用

Orchestrator 的应用网关配置声明 Azure AD B2C 应如何保护你的应用程序以及用户应如何访问应用。

1. 创建应用程序网关的名称。 可以使用友好名称或完全限定的主机名作为应用的标识符。

2. 设置 `location` 。 此处的示例使用应用程序的根目录 `/` ，但也可以是应用程序的任何 URL 路径。

3. `upstream`使用主机：端口约定在中定义受保护的应用程序 `https://example.com:8080` 。

4. 设置 "错误" 和 "未授权" 页的值。

5. 定义要为应用程序提供身份验证和控制对应用程序的访问权限的 HTTP 标头名称和属性值。 标头名称是任意的，通常对应于应用程序的配置。 属性值由提供这些值的连接器带命名空间。 在下面的示例中，从 Azure AD B2C 返回的值以连接器名称为前缀， `azureADB2C` 其中后缀是包含所需值的属性的名称，例如 `given_name` 。

6. 设置要评估和强制执行的策略。 定义了三个操作： `allowUnauthenticated` 、 `allowAnyAuthenticated` 和 `allowIfAny` 。 每个操作都与相关联 `resource` ，并对该策略进行 `resource` 评估。

>[!NOTE]
>`headers`和都 `policies` 使用 JavaScript 或 GoLang 服务扩展来实现可显著提高默认功能的任意逻辑。

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>使用 Azure 密钥保管库作为机密提供程序

务必保护 Orchestrator 用于连接到 Azure AD B2C 和任何其他标识系统的机密。 默认情况下，Maverics 将以纯文本格式加载机密 `maverics.yaml` ，但在本教程中，你将使用 Azure Key Vault 作为机密提供程序。

按照说明 [创建一个新的 Key Vault](../key-vault/secrets/quick-create-portal.md) ，Orchestrator 实例会将其用作机密提供程序。 将你的机密添加到保管库，并记下 `SECRET NAME` 为每个机密提供的。 例如 `AzureADB2CClientSecret`。

若要在 `maverics.yaml` 配置文件中将值声明为机密，请用尖括号将机密括起来：

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

尖括号中指定的值必须对应于 `SECRET NAME` Azure Key Vault 中的机密。

若要从 Azure Key Vault 加载机密，请使用以下模式在文件中设置环境变量， `MAVERICS_SECRET_PROVIDER` `/etc/maverics/maverics.env` 并在 azure-credentials.js文件中设置凭据：

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>将所有内容放在一起

下面介绍完成上述配置后，Orchestrator 的配置将如何显示。

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>测试流

1. 导航到 "本地应用程序 url" `https://example.com/sonar/dashboard` 。

2. Orchestrator 应重定向到用户流中配置的页面。

3. 从页面上的列表中选择 "IdP"。

4. 一旦重定向到 IdP，请根据请求提供凭据，并在该 IdP 需要时提供 MFA 令牌。

5. 成功进行身份验证后，应重定向到 Azure AD B2C，这会将应用请求转发到 Orchestrator 重定向 URI。

6. 业务流程协调程序将评估策略，计算标头，并将用户发送到上游应用程序。  

7. 应会看到所请求的应用程序。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)
