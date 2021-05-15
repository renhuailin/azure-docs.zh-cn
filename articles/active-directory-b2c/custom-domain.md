---
title: 启用 Azure AD B2C 自定义域
titleSuffix: Azure AD B2C
description: 了解如何在重定向 URL 中为 Azure Active Directory B2C 启用自定义域。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580158"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>为 Azure Active Directory B2C 启用自定义域

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

本文介绍如何在重定向 URL 中为 Azure Active Directory B2C (Azure AD B2C) 启用自定义域。 在应用程序中使用自定义域可提供更加优质的无缝用户体验。 从用户角度来看，他们会在登录过程中保留在域中，而不是重定向到 Azure AD B2C 默认域 <tenant-name>.b2clogin.com。

![自定义域用户体验](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>自定义域概述

可以使用 [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) 为 Azure AD B2C 启用自定义域。 Azure Front Door 是可缩放的全局入口点，它使用 Microsoft 全局边缘网络来创建快速、安全且可大规模缩放的 Web 应用程序。 可以在 Azure Front Door 后面呈现 Azure AD B2C 内容，然后在 Azure Front Door 中配置一个选项，以通过应用程序 URL 中的自定义域来提供内容。

下图说明了 Azure Front Door 的集成方式：

1. 在应用程序中，用户单击“登录”按钮将转到 Azure AD B2C 登录页。 在此页指定自定义域名。
1. Web 浏览器将自定义域名解析到 Azure Front Door IP 地址。 在 DNS 解析过程中，一个带有自定义域名的规范名称 (CNAME) 记录将指向 Front Door 的默认前端主机（例如 `contoso.azurefd.net`）。 
1. 发至自定义域（例如 `login.contoso.com`）的流量将被路由到指定的 Front Door 默认前端主机 (`contoso.azurefd.net`)。
1. Azure Front Door 使用 Azure AD B2C `<tenant-name>.b2clogin.com` 默认域调用 Azure AD B2C 内容。 针对 Azure AD B2C 终结点的请求包含一个自定义 HTTP 标头，其中包含原始自定义域名。
1. Azure AD B2C 通过显示相关内容和原始自定义域来响应请求。

![自定义域网络示意图](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> 从浏览器到 Azure Front Door 的连接应始终使用 IPv4，而不是 IPv6。

使用自定义域时，请注意以下事项：

- 可以设置多个自定义域。 有关最多支持的自定义域数，请参阅适用于 Azure AD B2C 的 [Azure AD 服务限制和约束](../active-directory/enterprise-users/directory-service-limits-restrictions.md)以及适用于 Azure Front Door 的 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)。
- Azure Front Door 是单独的 Azure 服务，因此会产生额外费用。 有关详细信息，请参阅 [Front Door 定价](https://azure.microsoft.com/pricing/details/frontdoor)。
- 要使用 Azure Front Door [Web 应用程序防火墙](../web-application-firewall/afds/afds-overview.md)，需要确认你的防火墙配置和规则是否可与 Azure AD B2C 用户流正常配合运行。
- 配置自定义域后，用户仍可以访问 Azure AD B2C 默认域名 <tenant-name>.b2clogin.com（除非你使用自定义策略并且[阻止访问](#block-access-to-the-default-domain-name)）。
- 如果有多个应用程序，请将它们全部迁移到自定义域，因为浏览器会在当前使用的域名下存储 Azure AD B2C 会话。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>为租户添加自定义域名

请按照如何[在 Azure AD 中添加和验证自定义域](../active-directory/fundamentals/add-custom-domain.md)的指导进行操作。 验证域后，删除创建的 DNS TXT 记录。

> [!IMPORTANT]
> 对于这些步骤，请确保登录到 Azure AD B2C 租户，并选择“Azure Active Directory”服务。

验证计划使用的每个子域。 仅验证顶级域并不够。 例如，要能够通过 login.contoso.com 和 account.contoso.com 登录，需要验证这两个子域，而不只是顶级域 contoso.com。  

## <a name="create-a-new-azure-front-door-instance"></a>创建新的 Azure Front Door 实例

请按照使用前端主机和路由规则的默认设置[为应用程序创建 Front Door](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) 的步骤执行操作。 

> [!IMPORTANT]
> 对于这些步骤，请在步骤 1 中登录到 Azure 门户后，选择“目录 + 订阅”，然后选择包含要用于 Azure Front Door 的 Azure 订阅的目录。 不应是包含 Azure AD B2C 租户的目录。 

在“添加后端”步骤中，使用以下设置：

* 对于“后端主机类型”，选择“自定义主机” 。  
* 对于“后端主机名”，选择 Azure AD B2C 终结点的主机名：<tenant-name>.b2clogin.com。 例如，contoso.b2clogin.com。 
* 对于“后端主机头”，选择为“后端主机名”选择的相同值。

![添加后端](./media/custom-domain/add-a-backend.png)

将该后端添加到后端池后，禁用“运行状况探测”。

![添加后端池](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>在 Azure Front Door 上设置自定义域

按照步骤[将自定义域添加到 Front Door](../frontdoor/front-door-custom-domain.md)。 为自定义域创建 `CNAME` 记录时，使用前面在“[将自定义域名添加到 Azure AD](#add-a-custom-domain-name-to-your-tenant)”步骤中验证的自定义域名。 

验证自定义域名后，选择“自定义域名 HTTPS”。 然后，在“证书管理类型”下，选择 [Front Door 管理](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)或[使用我自己的证书](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate)。 

以下屏幕截图显示了如何添加自定义域和使用 Azure Front Door 证书启用 HTTPS。

![设置 Azure Front Door 自定义域](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>配置 CORS

如果使用 HTML 模板[自定义 Azure AD B2C 用户界面](customize-ui-with-html.md)，则需要[配置 CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) 以使用自定义域。

请按照以下步骤配置 Azure Blob 存储以进行跨域资源共享：

1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. 在菜单中，选择“CORS”。
1. 对于“允许的源”，请输入 `https://your-domain-name`。 将 `your-domain-name` 替换为你的自定义域名。 例如，`https://login.contoso.com`。 输入租户名称时全部使用小写字母。
1. 对于“允许的方法”，请同时选择 `GET` 和 `OPTIONS`。
1. 对于“允许的标头”，请输入一个星号 (*)。
1. 对于“公开的标头”，请输入一个星号 (*)。
1. 对于“最大期限”，请输入 200。
1. 选择“保存”。

## <a name="configure-your-identity-provider"></a>配置标识提供者

当用户选择使用社交标识提供者登录时，Azure AD B2C 会启动授权请求，并将用户转到所选的标识提供者来完成登录过程。 授权请求将为 `redirect_uri` 指定 Azure AD B2C 默认域名： 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

如果已将策略配置为允许使用外部标识提供者登录，请将 OAuth 重定向 URI 更新为自定义域。 大多数标识提供者都允许注册多个重定向 URI。 建议添加重定向 URI，而不是替换它们，这样既可以测试自定义策略，又不会影响使用 Azure AD B2C 默认域名的应用程序。 

在以下重定向 URI 中：

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- 将 <custom-domain-name> 替换为你的自定义域名。
- 将 <tenant-name> 替换为租户的名称或租户 ID。

以下示例显示了一个有效的 OAuth 重定向 URI：

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

如果选择使用[租户 ID](#optional-use-tenant-id)，有效的 OAuth 重定向 URI 将如下所示：

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML 标识提供者](saml-identity-provider-technical-profile.md)元数据将如下所示：

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>测试自定义域

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择一个用户流，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“复制到剪贴板”。

    ![复制授权请求 URI](./media/custom-domain/user-flow-run-now.png)

1. 在“运行用户流终结点”URL 中，将 Azure AD B2C 域 (<tenant-name>.b2clogin.com) 替换为你的自定义域。  
    例如，不是：

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    而是使用：

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. 选择“运行用户流”。 应会加载你的 Azure AD B2C 策略。
1. 使用本地和社交帐户登录。
1. 对于其余策略重复此测试。

## <a name="configure-your-application"></a>配置应用程序 

在配置并测试自定义域后，可以更新应用程序来加载将自定义域指定为主机名（而不是 Azure AD B2C 域）的 URL。 

自定义域集成适用于身份验证终结点，它们使用 Azure AD B2C 策略（用户流或自定义策略）对用户进行身份验证。 这些终结点可能如下所示：

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

将：
- custom-domain 替换为你的自定义域名
- tenant-name 替换为租户名称或租户 ID
- policy-name 替换为你的策略名称。 [详细了解 Azure AD B2C 策略](technical-overview.md#identity-experiences-user-flows-or-custom-policies)。 


[SAML 服务提供商](./saml-service-provider.md)的元数据可能如下所示： 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>（可选）使用租户 ID

可以将 URL 中的 B2C 租户名称替换为你的租户 ID GUID，以便删除对 URL 中“B2C”的所有引用。 在“Azure 门户”的“B2C 概述”页中可找到你的租户 ID GUID。
例如，将 `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` 更改为 `https://account.contosobank.co.uk/<tenant ID GUID>/`

如果选择使用租户 ID 而不是租户名称，请确保相应地更新标识提供者的 **OAuth 重定向 URI**。 有关详细信息，请参阅[配置标识提供者](#configure-your-identity-provider)。

### <a name="token-issuance"></a>令牌颁发

令牌颁发者名称 (iss) 声明根据所使用的自定义域而进行变化。 例如：

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>阻止访问默认域名

在添加自定义域和配置应用程序后，用户仍能够访问 <tenant-name>.b2clogin.com 域。 要阻止访问，可以将策略配置为对照允许的域列表检查授权请求的“主机名”。 主机名是指 URL 中显示的域名。 通过`{Context:HostName}`[声明解析器](claim-resolver-overview.md)可获取主机名。 然后，则可以显示自定义错误消息。 

1. 获取检查来自 [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name) 的主机名的条件访问策略的示例。
1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。
1. 按以下顺序上传策略文件：先是 `B2C_1A_TrustFrameworkExtensions_HostName.xml`，`B2C_1A_signup_signin_HostName.xml` 次之。

::: zone-end

## <a name="troubleshooting"></a>疑难解答

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C 返回“未找到页面”错误

- 症状 - 配置自定义域后，在尝试使用自定义域登录时，收到 HTTP 404 错误消息。
- 可能的原因 - 此问题可能与 DNS 配置或 Azure Front Door 后端配置相关。 
- **解决方法**：  
    1. 请确保该自定义域在 Azure AD B2C 租户中[已注册并成功通过验证](#add-a-custom-domain-name-to-your-tenant)。
    1. 请确保该[自定义域](../frontdoor/front-door-custom-domain.md)配置正确。 自定义域的 `CNAME` 记录必须指向 Azure Front Door 默认的前端主机（例如 contoso.azurefd.net）。
    1. 确保 [Azure Front Door 后端池配置](#set-up-your-custom-domain-on-azure-front-door)指向从中设置自定义域名以及存储用户流或自定义策略的租户。

### <a name="identify-provider-returns-an-error"></a>标识提供者返回错误

- 症状 - 配置自定义域后，可以使用本地帐户登录。 但在使用外部[社交或企业标识提供者](add-identity-provider.md)的凭据登录时，标识提供者会显示错误消息。
- 可能的原因 - 当 Azure AD B2C 引导用户使用联合标识提供者登录时，会指定重定向 URI。 重定向 URI 是标识提供者返回令牌的终结点。 重定向 URI 与应用程序对授权请求使用的域相同。 如果重定向 URI 尚未在标识提供者处注册，则它可能不信任新的重定向 URI，从而导致错误消息。 
- 解决方法 - 按照[配置标识提供者](#configure-your-identity-provider)中的步骤添加新的重定向 URI。 


## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>能否使用 Azure Front Door 高级配置，例如 Web 应用程序防火墙规则？ 
  
虽然 Azure Front Door 高级配置设置不受官方支持，但你可以使用它们，不过风险自担。 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>在使用“立即运行”尝试运行我的策略时，为什么看不到自定义域？

复制 URL，手动更改域名，然后将其粘贴回你的浏览器。

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>对于 Azure AD B2C 显示哪个 IP 地址？ 用户的 IP 地址还是 Azure Front Door IP 地址？

Azure Front Door 传递用户的原始 IP 地址。 这就是你在审核报表或自定义策略中看到的 IP 地址。

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>是否可以对 B2C 使用第三方 Web 应用程序防火墙 (WAF)？

要在 Azure Front Door 之前使用自己的 Web 应用程序防火墙，需要配置并验证所有设置是否可与你的 Azure AD B2C 用户流正常配合运行。

## <a name="next-steps"></a>后续步骤

了解 [OAuth 身份验证请求](protocols-overview.md)。