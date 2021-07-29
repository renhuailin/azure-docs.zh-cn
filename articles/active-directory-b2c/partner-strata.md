---
title: 为 Azure Active Directory B2C 配置 Strata 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 whoIam 集成以进行用户验证
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a58042c7098116a2800453dcca46cae62011dfac
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570477"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>本教程介绍如何使用 Strata 扩展 Azure AD B2C 以保护本地应用程序

在此示例教程中，了解如何将 Azure Active Directory (AD) B2C 与 Strata 的 [Maverics 标识业务流程协调程序](https://www.strata.io/maverics-identity-orchestrator/)集成。
Maverics 标识业务流程协调程序会扩展 Azure AD B2C 来保护本地应用程序。 它会连接到任何标识系统，以透明方式迁移用户和凭据，同步策略和配置，并抽象身份验证和会话管理。 使用 Strata，企业可以迅速从旧系统转换到 Azure AD B2C，无需重写应用程序。 该解决方案具有以下优点：

- 客户单一登录 (SSO) 到本地混合应用：Azure AD B2C 支持 Maverics 标识业务流程协调程序的客户 SSO。 用户使用 Azure AD B2C 或社交标识提供者 (IdP) 中承载的帐户登录。 Maverics 会将 SSO 扩展到一直由旧标识系统（如 Symantec SiteMinder）保护的应用。

- 在不重写的情况下将基于标准的 SSO 扩展到应用：使用 Azure AD B2C 来管理用户访问权限，并使 Maverics 标识业务流程协调程序 SAML 或 OIDC 连接器能够使用 SSO。

- 轻松配置：Azure AD B2C 提供了简单的分步用户界面，用于将 Maverics 标识业务流程协调程序 SAML 或 OIDC 连接器连接到 Azure AD B2C。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 的实例，用于存储由 Maverics 标识业务流程协调程序使用的机密。 它用于连接到 Azure AD B2C 或其他属性提供程序，例如轻型目录访问协议 (LDAP) 目录或数据库。

- 在 Azure 虚拟机或你选择的本地服务器中安装并运行的 [Maverics 标识业务流程协调程序](https://www.strata.io/maverics-identity-orchestrator/)的实例。 若要了解如何获取软件以及如何访问安装和配置文档，请联系 [Strata](https://www.strata.io/contact/)

- 你将要从旧标识系统转换到 Azure AD B2C 的本地应用程序。

## <a name="scenario-description"></a>方案描述

Strata 的 Maverics 集成包括以下组件：

- Azure AD B2C：负责验证用户凭据的授权服务器。 经过身份验证的用户可以使用 Azure AD B2C 目录中存储的本地帐户来访问本地应用。

- 外部社交或企业 IdP：可以是任何 OpenID Connect 提供程序、Facebook、Google 或 GitHub。 有关详细信息，请参阅[添加标识提供者](./add-identity-provider.md)。  

- Strata 的 Maverics 标识业务流程协调程序：该服务协调用户登录并以透明方式将标识通过 HTTP 标头传递到应用。

以下体系结构图体现了实施详情。

![图中显示 Azure AD B2C 的体系结构与 Strata Maverics 集成，以便能够访问混合应用](./media/partner-strata/strata-architecture-diagram.png)

| 步骤 | 说明 |
|:-------|:---------------|
| 1. | 用户发出访问本地托管应用的请求。 Maverics 标识业务流程协调程序将用户发出的请求代理到该应用程序。|
| 2. | 业务流程协调程序检查用户的身份验证状态。 如果未收到会话令牌，或者提供的会话令牌无效，它会将该用户发送到 Azure AD B2C 进行身份验证。|
| 3. | Azure AD B2C 会将身份验证请求发送到配置的社交 IdP。|
| 4. | 该 IdP 会质询用户，要求提供凭据。 根据该 IdP，用户可能需要执行多重身份验证 (MFA)。|
| 5. | 该 IdP 会将身份验证响应发送回 Azure AD B2C。 （可选）在此步骤中，用户可以在 Azure AD B2C 目录中创建本地帐户。|
| 6. | Azure AD B2C 会将用户请求发送到业务流程协调程序应用在 Azure AD B2C 租户中注册的过程中指定的终结点。|
| 7. | 业务流程协调程序会评估访问策略，并计算要在转发到该应用的 HTTP 标头中包含的属性值。 在此步骤中，业务流程协调程序可能会调用其他属性提供程序，以检索正确设置标头值所需的信息。 业务流程协调程序会设置标头值并将请求发送到该应用。|
| 8. | 现在，用户经过了身份验证，并且具有了该应用的访问权限。|

## <a name="get-maverics-identity-orchestrator"></a>获取 Maverics 标识业务流程协调程序
若要获取该软件，以便用于将旧的本地应用与 Azure AD B2C 集成，请联系 [Strata](https://www.strata.io/contact/)。 在获取该软件后，请按下述步骤来确定特定于业务流程协调程序的先决条件，并执行所需安装和配置步骤。

## <a name="configure-your-azure-ad-b2c-tenant"></a>配置 Azure AD B2C 租户

1. **注册应用程序**

   a. 在 Azure AD B2C 租户中[将该业务流程协调程序注册为应用程序](./tutorial-register-applications.md?tabs=app-reg-ga)。
   >[!Note]
   >稍后，在配置业务流程协调程序实例时，将会需要租户名称和标识符、客户端 ID、客户端机密、已配置的声明和重定向 URI。

   b. 向 Microsoft MS Graph API 授予访问你的应用程序的权限。 你的应用程序将需要以下权限：`offline_access`、`openid`。

   c. 为应用程序添加重定向 URI。 此 URI 将会匹配业务流程协调程序的 Azure AD B2C 连接器配置的 `oauthRedirectURL` 参数，例如 `https://example.com/oidc-endpoint`。

2. 创建用户流：创建[注册和登录用户流](./tutorial-create-user-flows.md)。

3. 添加 IdP：选择使用本地帐户或社交或企业 [IdP](./add-identity-provider.md) 来登录你的用户。

4. 定义用户属性：定义要在注册期间收集的属性。

5. 指定应用程序声明：指定要通过业务流程协调程序实例返回到应用程序的属性。 业务流程协调程序使用由 Azure AD B2C 返回的声明中的属性，并且可以从其他连接的标识系统（如 LDAP 目录和数据库）检索其他属性。 那些属性是在 HTTP 标头中设置的，并且会发送到上游本地应用程序。

## <a name="configure-maverics-identity-orchestrator"></a>配置 Maverics 标识业务流程协调程序

在以下部分中，我们将引导你完成配置业务流程协调程序实例所需的步骤。 有关其他支持和文档，请联系 [Strata](https://www.strata.io/contact/)。

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics 标识业务流程协调程序服务器要求

你可以在任何服务器上运行业务流程协调程序实例，无论是在本地还是在 Azure、AWS 或 GCP 等提供商的公共云基础结构中。

- OS：REHL 7.7 或更高版本，CentOS 7 +

- 磁盘：10 GB（小）

- 内存：16 GB

- 端口：22 (SSH/SCP)、443、80

- 安装/管理任务的根访问

- Maverics 标识业务流程协调程序以 `systemd` 下用户 `maverics` 的身份运行

- 来自承载 Maverics 标识业务流程协调程序的服务器的并且能够访问 Azure AD 租户的网络流出量。

### <a name="install-maverics-identity-orchestrator"></a>安装 Maverics Identity Orchestrator

1. 获取最新的 Maverics RPM 包。 将该包置于要在其中安装 Maverics 的系统中。 如果要将文件复制到远程主机，[SCP](https://www.ssh.com/ssh/scp/) 是个非常有用的工具。

2. 若要安装 Maverics 包，请运行以下命令，将文件名替换为 `maverics.rpm`。

   `sudo rpm -Uvf maverics.rpm`

   默认情况下，Maverics 安装在 `/usr/local/bin` 目录中。

3. 安装 Maverics 后，它将作为服务在 `systemd` 下运行。  若要验证 Maverics 服务是否正在运行，请运行以下命令：

   `sudo service maverics status`

  如果业务流程协调程序安装成功，应该会显示类似于下面的消息：

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

4. 如果 Maverics 服务启动失败，请执行以下命令来调查问题：

   `journalctl --unit=maverics.service --reverse`

   最新日志项将会出现在输出的开头。

安装 Maverics 后，将在 `/etc/maverics` 目录中创建默认的 `maverics.yaml` 文件。

将业务流程协调程序配置为保护该应用程序。 与 Azure AD B2C 集成，存储，并从 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9) 检索机密。 定义业务流程协调程序应从中读取其配置的位置。

### <a name="supply-configuration-using-environment-variables"></a>使用环境变量提供配置

通过环境变量向业务流程协调程序实例提供配置。

`MAVERICS_CONFIG`

此环境变量告诉业务流程协调程序实例在启动或重启过程中要使用哪些 YAML 配置文件以及在哪里找到这些文件。 在 `/etc/maverics/maverics.env` 中设置该环境变量。

### <a name="create-the-orchestrators-tls-configuration"></a>创建业务流程协调程序的 TLS 配置

`maverics.yaml` 中的 `tls` 字段声明业务流程协调程序实例将要使用的传输层安全性配置。 连接器可以使用 TLS 对象和业务流程协调程序服务器。

`maverics` 密钥是为 Orchestrator 服务器保留的。 其他所有密钥均可用，并且可用于将 TLS 对象注入到给定连接器中。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>配置 Azure AD B2C 连接器

业务流程协调程序使用连接器与身份验证和属性提供程序集成。 在这种情况下，此业务流程协调程序应用程序网关使用 Azure AD B2C 连接器作为身份验证和属性提供程序。 Azure AD B2C 使用社交 IdP 进行身份验证，然后充当业务流程协调程序的属性提供程序，在 HTTP 标头中设置的声明中传递属性。  

此连接器的配置对应于在 Azure AD B2C 租户中注册的应用。

1. 从租户中的应用配置复制客户端 ID、机密和重定向 URI。

2. 为连接器指定名称，此处显示为 `azureADB2C`，并将该连接器 `type` 设置为 `azure`。 记下连接器名称，因为在下面的其他配置参数中还要使用此值。

3. 对于此集成，`authType` 应设置为 `oidc`。

4. 将步骤 1 中复制的客户端 ID 设置为 `oauthClientID` 参数的值。

5. 将步骤 1 中复制的客户端密码设置为 `oauthClientSecret` 参数的值。

6. 将步骤 1 中复制的重定向 URI 设置为 `oauthRedirectURL` 参数的值。

7. Azure AD B2C OIDC 连接器使用已知的 OIDC 终结点来发现元数据，包括 URL 和签名密钥。 将 `oidcWellKnownURL` 的值设置为租户的终结点。

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

身份验证提供程序确定如何针对未在应用资源请求中提供有效会话的用户执行身份验证。 Azure AD B2C 租户中的配置确定如何质询用户的凭据并应用其他身份验证策略。 例如，若需要另一个因素来完成身份验证过程，并决定在身份验证成功后应将哪些声明返回到业务流程协调程序应用程序网关。

`authProvider` 的值必须与连接器的 `name` 值匹配。

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>使用业务流程协调程序应用程序网关保护本地应用

业务流程协调程序的应用程序网关配置声明 Azure AD B2C 应如何保护你的应用程序以及用户应如何访问该应用。

1. 为应用程序网关创建名称。 可以使用易记名称或完全限定的主机名作为应用的标识符。

2. 设置 `location`。 此处的示例使用该应用的根 `/`，不过它可以是应用程序的任何 URL 路径。

3. 使用 host:port 约定 `https://example.com:8080` 来定义 `upstream` 中受保护的应用程序。

4. 设置错误和未授权页面的值。

5. 定义 HTTP 标头名称和属性值，这些内容必须向应用程序提供，以建立身份验证并控制应用的访问权限。 标头名称是任意的，通常对应于该应用的配置。 属性值由提供这些值的连接器来设置命名空间。 在下面的示例中，从 Azure AD B2C 返回的值以连接器名称 `azureADB2C` 为前缀，而后缀则是包含所需值的属性的名称，例如 `given_name`。

6. 设置要评估和强制执行的策略。 定义了三个操作：`allowUnauthenticated`、`allowAnyAuthenticated` 和 `allowIfAny`。 每个操作都与 `resource` 相关联，并且该策略会接受 `resource` 方面的评估。

>[!NOTE]
>`headers` 和 `policies` 都使用 JavaScript 或 GoLang 服务扩展来实现可显著增强默认功能的任意逻辑。

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

请务必保护好业务流程协调程序用于连接到 Azure AD B2C 和任何其他标识系统的机密。 在默认情况下，Maverics 将以纯文本格式从 `maverics.yaml` 中加载机密，但在本教程中，你将使用 Azure Key Vault 作为机密提供程序。

请按照说明[新建 Key Vault](../key-vault/secrets/quick-create-portal.md)，业务流程协调程序实例将用它作机密提供程序。 将机密添加到保管库，并记下为每个机密提供的 `SECRET NAME`。 例如，`AzureADB2CClientSecret`。

若要在 `maverics.yaml` 配置文件中将值声明为机密，请用尖括号将机密括起来：

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

尖括号中指定的值必须对应于为 Azure Key Vault 中机密提供的 `SECRET NAME`。

若要从 Azure KeyVault 加载机密，请使用以下模式，利用在 azure-credentials.json 文件中找到的凭据来设置 `/etc/maverics/maverics.env` 文件中的环境变量 `MAVERICS_SECRET_PROVIDER`：

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>将所有内容放在一起

下面介绍在完成上述配置后，该业务流程协调程序的配置将如何显示。

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

1. 导航到本地应用程序 URL `https://example.com/sonar/dashboard`。

2. 该业务流程协调程序应该会重定向到在用户流中配置的页面。

3. 在该页面上从列表中选择 IdP。

4. 在重定向到该 IdP 后，请根据请求提供凭据，包括 MFA 令牌（如果该 IdP 需要）。

5. 在成功进行身份验证后，你应该会重定向到 Azure AD B2C，这样就会将应用请求转发到业务流程协调程序重定向 URI。

6. 业务流程协调程序将评估策略，计算标头，并将用户发送到上游应用程序。  

7. 你应该会看到请求的应用程序。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
