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
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ceb265ef339d39f14dbc042914e471c692ae6420
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128568552"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>为 Azure Active Directory B2C 启用自定义域

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

本文介绍如何在重定向 URL 中为 Azure Active Directory B2C (Azure AD B2C) 启用自定义域。 在应用程序中使用自定义域可提供更加优质的无缝用户体验。 从用户角度来看，他们会在登录过程中保留在域中，而不是重定向到 Azure AD B2C 默认域 &lt;tenant-name&gt;.b2clogin.com。

![屏幕截图演示了 Azure AD B2C 自定义域用户体验。](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>自定义域概述

可以使用 [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) 为 Azure AD B2C 启用自定义域。 Azure Front Door 是一个全局入口点，它使用 Microsoft 全局边缘网络来创建快速、安全且可大规模缩放的 Web 应用程序。 可以在 Azure Front Door 后面呈现 Azure AD B2C 内容，然后在 Azure Front Door 中配置一个选项，以通过应用程序 URL 中的自定义域来提供内容。

下图说明了 Azure Front Door 的集成方式：

1. 在应用程序中，用户选择“登录”按钮将转到 Azure AD B2C 登录页。 在此页指定自定义域名。
1. Web 浏览器将自定义域名解析到 Azure Front Door IP 地址。 在 DNS 解析过程中，一个带有自定义域名的规范名称 (CNAME) 记录将指向 Front Door 的默认前端主机（例如 `contoso-frontend.azurefd.net`）。 
1. 发至自定义域（例如 `login.contoso.com`）的流量将被路由到指定的 Front Door 默认前端主机 (`contoso-frontend.azurefd.net`)。
1. Azure Front Door 使用 Azure AD B2C `<tenant-name>.b2clogin.com` 默认域调用 Azure AD B2C 内容。 针对 Azure AD B2C 终结点的请求包含原始自定义域名。
1. Azure AD B2C 通过显示相关内容和原始自定义域来响应请求。

![关系图显示了自定义域网络流。](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> 从浏览器到 Azure Front Door 的连接应始终使用 IPv4，而不是 IPv6。

使用自定义域时，请注意以下事项：

- 可以设置多个自定义域。 有关最多支持的自定义域数，请参阅适用于 Azure AD B2C 的 [Azure AD 服务限制和约束](../active-directory/enterprise-users/directory-service-limits-restrictions.md)以及适用于 Azure Front Door 的 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)。
- Azure Front Door 是单独的 Azure 服务，因此会产生额外费用。 有关详细信息，请参阅 [Front Door 定价](https://azure.microsoft.com/pricing/details/frontdoor)。
- 要使用 Azure Front Door [Web 应用程序防火墙](../web-application-firewall/afds/afds-overview.md)，需要确认你的防火墙配置和规则是否可与 Azure AD B2C 用户流正常配合运行。
- 配置自定义域后，用户仍可以访问 Azure AD B2C 默认域名 &lt;tenant-name&gt;.b2clogin.com（除非你使用自定义策略并且[阻止访问](#block-access-to-the-default-domain-name)）。
- 如果有多个应用程序，请将它们全部迁移到自定义域，因为浏览器会在当前使用的域名下存储 Azure AD B2C 会话。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant"></a>步骤 1。 为 Azure AD B2C 租户添加自定义域名

每个新的 Azure AD B2C 租户都附带了初始域名 &lt;domainname&gt;.onmicrosoft.com。 无法更改或删除初始域名，但可添加自定义域。 

按照以下步骤将自定义域添加到 Azure AD B2C 租户：

1. [将自定义域名添加到 Azure AD](../active-directory/fundamentals/add-custom-domain.md#add-your-custom-domain-name-to-azure-ad)。

    > [!IMPORTANT]
    > 对于这些步骤，请确保登录到 Azure AD B2C 租户，并选择“Azure Active Directory”服务。

1. [将 DNS 信息添加到域注册机构](../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)。 将自定义域名添加到 Azure AD 后，为域创建一个 DNS `TXT` 或 `MX` 记录。 为域创建此 DNS 记录可以验证域名的所有权。

    以下示例演示了 login.contoso.com 和 account.contoso.com 的 TXT 记录 ：

    |名称（主机名）  |类型  |数据  |
    |---------|---------|---------|
    |login   | TXT  | MS=ms12345678  |
    |account | TXT  | MS=ms87654321  |
    
    TXT 记录必须与子域或域的主机名相关联。 例如，contoso.com 域中的 login 部分 。 如果主机名为空或为 `@`，则 Azure AD 将无法验证你添加的自定义域。 在以下示例中，两个记录都未正确配置。
    
    |名称（主机名）  |类型  |数据  |
    |---------|---------|---------|
    | | TXT  | MS=ms12345678  |
    | @ | TXT  | MS=ms12345678  | 
    
    > [!TIP]
    > 可使用任何公开提供的 DNS 服务（例如 GoDaddy）来管理自定义域。 如果没有 DNS 服务器，可使用 [Azure DNS 区域](../dns/dns-getstarted-portal.md)或[应用服务域](../app-service/manage-custom-dns-buy-domain.md)。

1. [验证自定义域名](../active-directory/fundamentals/add-custom-domain.md#verify-your-custom-domain-name)。 验证每个子域或你计划使用的主机名。 例如，要能够通过 login.contoso.com 和 account.contoso.com 进行登录，需要验证这两个子域，而不是顶级域 contoso.com  。 

    验证域后，删除创建的 DNS TXT 记录。

    
## <a name="step-2-create-a-new-azure-front-door-instance"></a>步骤 2. 创建新的 Azure Front Door 实例

按照以下步骤创建 Azure AD B2C 租户的 Front Door。 有关详细信息，请参阅[创建应用程序的 Front Door](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application)。
  

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 要选择包含要用于 Azure Front Door 的 Azure 订阅的目录，而不是包含 Azure AD B2C 租户的目录，请在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录 + 订阅”页上，在“目录名称”列表中找到 Azure AD 目录，然后选择“切换”。 
1. 在主页或 Azure 菜单中，选择“创建资源”。 选择“网络” > “查看全部” > “Front Door”。
1. 在“创建 Front Door”页的“基本信息”选项卡中，输入或选择以下信息，然后选择“下一步:  配置”。

    | 设置 | 值 |
    | --- | --- |
    | **订阅** | 选择 Azure 订阅。 |    
    | **资源组** | 选择现有资源组，或选择“新建”，创建新资源组。|
    | **资源组位置** | 选择资源组的位置。 例如“美国中部”。 |

### <a name="21-add-frontend-host"></a>2.1 添加前端主机

前端主机是应用程序使用的域名。 创建 Front Door 时，默认前端主机是 `azurefd.net` 的子域。

Azure Front Door 提供用于将自定义域与前端主机相关联的选项。 使用此选项，可将 Azure AD B2C 用户界面与 URL 中的自定义域（而不是 Front Door 拥有的域名）相关联。 例如，`https://login.contoso.com`。

若要添加前端主机，请执行以下步骤：

1. 在“前端/域”中，选择“+”以打开“添加前端主机”  。
1. 对于“主机名”，请输入全局唯一的主机名。 主机名不是自定义域。 本示例使用 contoso-frontend。 选择“添加”。

    ![屏幕截图演示了添加前端主机的方法。](./media/custom-domain/add-frontend-host-azure-front-door.png)

### <a name="22-add-backend-and-backend-pool"></a>2.2 添加后端和后端池

后端指 [Azure AD B2C 租户名称](tenant-management.md#get-your-tenant-name)：`tenant-name.b2clogin.com`。 若要添加后端池，请执行以下步骤：

1. 仍在“创建 Front Door”中，在“后端池”中选择“+”以打开“添加后端池”   。

1. 输入“名称”。 例如，myBackendPool。 选择“添加后端”。
    
    以下屏幕截图演示了创建后端池的方法：
    
    ![屏幕截图演示了添加后端池的方法。](./media/custom-domain/front-door-add-backend-pool.png)

1. 在“添加后端”边栏选项卡中，选择以下信息，然后选择“添加” 。

    | 设置 | 值 |
    | --- | --- |
    | 后端主机类型| 选择“自定义主机”。| 
    | 后端主机名| 选择你的 [Azure AD B2C](tenant-management.md#get-your-tenant-name) 的名称：`<tenant-name>.b2clogin.com`。 例如，contoso.b2clogin.com。|
    | 后端主机头| 选择为“后端主机名”选择的相同值。|
    
    将所有其他字段保留为默认值。*
    
    以下屏幕截图演示了如何创建一个与 Azure AD B2C 租户相关联的自定义主机后端：
    
    ![屏幕截图演示了添加自定义主机后端的方法。](./media/custom-domain/add-a-backend.png)

1. 若要完成后端池的配置，在“添加后端池”边栏选项卡上，选择“添加” 。

1. 将该后端添加到后端池后，禁用“运行状况探测”。

    ![屏幕截图演示了如何添加后端池和禁用运行状况探测。](./media/custom-domain/add-a-backend-pool.png)

### <a name="23-add-a-routing-rule"></a>2.3 添加传递规则

最后，添加一个路由规则。 传递规则将前端主机映射到后端池。 该规则将[前端主机](#21-add-frontend-host)的请求转发到 Azure AD B2C [后端](#22-add-backend-and-backend-pool)。 若要添加传递规则，请执行以下步骤：

1. 在“添加规则”中，对于“名称”，请输入 LocationRule 。 接受所有默认值，然后选择“添加”以添加路由规则。
1. 选择“查看 + 创建”，然后选择“创建” 。
    
     ![屏幕截图演示了创建 Azure Front Door 的方法。](./media/custom-domain/configuration-azure-front-door.png)


## <a name="step-3-set-up-your-custom-domain-on-azure-front-door"></a>步骤 3. 在 Azure Front Door 上设置自定义域

在此步骤中，将[步骤 1](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) 中注册的自定义域添加到 Front Door。 

### <a name="31-create-a-cname-dns-record"></a>3.1 创建 CNAME DNS 记录

在将自定义域与 Front Door 结合使用之前，必须先在域提供商的配合下创建一个规范名称 (CNAME) 记录，使之指向 Front Door 的默认前端主机（例如 contoso-frontend.azurefd.net）。

CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名（别名）。 对于 Azure Front Door，源域名是自定义域名，而目标域名是你在[步骤 2.1](#21-add-frontend-host) 中配置的 Front Door 默认主机名。 

在 Front Door 验证你创建的 CNAME 记录后，发往源自定义域（如 login.contoso.com）的流量将路由到指定的目标 Front Door 默认前端主机（如 `contoso-frontend.azurefd.net`）。 有关详细信息，请参阅[将自定义域添加到 Front Door](../frontdoor/front-door-custom-domain.md)。 

若要创建自定义域的 CNAME 记录，请执行以下操作：

1. 登录到你的自定义域的域提供商的网站。

1. 查阅提供商的文档，或者在网站中搜索标有“域名”、“DNS”或“名称服务器管理”的区域，找到用于管理 DNS 记录的页面。    

1. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | 源          | 类型  | 目标           |
    |-----------------|-------|-----------------------|
    | `<login.contoso.com>` | CNAME | `contoso-frontend.azurefd.net` |

   - 源：输入自定义域名（例如 login.contoso.com）。

   - 键入：输入 *CNAME*。

   - 目标：输入你在[步骤 2.1](#21-add-frontend-host) 中创建的默认 Front Door 前端主机。 必须采用 &lt;主机名&gt;.azurefd.net 格式  。 例如，`contoso-frontend.azurefd.net`。

1. 保存所做更改。

### <a name="32-associate-the-custom-domain-with-your-front-door"></a>3.2 将自定义域与 Front Door 相关联

注册自定义域以后，即可将其添加到 Front Door。
    
1. 在“Front Door 设计器”页的“前端/域”下，选择“+”以添加自定义域  。

    ![屏幕截图演示了添加自定义域的方法。](./media/custom-domain/azure-front-door-add-custom-domain.png) 
    
1. 对于“前端主机”，用作 CNAME 记录的目标域的前端主机已预先填充，并派生自 Front Door ：&lt;默认主机名&gt;.azurefd.net   。 无法进行更改。

1. 对于 **自定义主机名**，请输入自定义域（包括子域），以用作 CNAME 记录的源域。 例如，login.contoso.com。

    ![屏幕截图演示了验证自定义域的方法。](./media/custom-domain/azure-front-door-add-custom-domain-verification.png)

    Azure 会验证所输入的自定义域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。

    
1. 验证自定义域名后，在“自定义域名 HTTPS”下，选择“启用” 。 
    
    ![屏幕截图显示了如何使用 Azure Front Door 证书启用 HTTPS。](./media/custom-domain/azure-front-door-add-custom-domain-https-settings.png)

1. 对于“证书管理类型”，选择 [Front Door 管理](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)或[使用我自己的证书](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate)。 如果选择“托管的 Front Door”选项，请等待证书完全预配完毕。

1. 选择 **添加** 。

### <a name="33-update-the-routing-rule"></a>3.3 更新传递规则

1. 在“传递规则”中，选择你在[步骤 2.3](#23-add-a-routing-rule) 中创建的传递规则。

    ![屏幕截图演示了选择传递规则的方法。](./media/custom-domain/select-routing-rule.png)

1. 在“前端/域”下，选择你的自定义域名。
    
    ![屏幕截图演示了如何更新 Azure Front Door 传递规则。](./media/custom-domain/update-routing-rule.png)

1. 选择“更新”。
1. 在主窗口中，选择“保存”。

## <a name="step-4-configure-cors"></a>步骤 4. 配置 CORS

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

## <a name="test-your-custom-domain"></a>测试自定义域

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择一个用户流，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 复制“运行用户流终结点”下的 URL。

    ![屏幕截图演示了复制授权请求 URI 的方法。](./media/custom-domain/user-flow-run-now.png)

1. 若要使用自定义域模拟登录，请打开 Web 浏览器并使用复制的 URL。 将 Azure AD B2C 域（&lt;tenant-name&gt;.b2clogin.com）替换为你的自定义域。   

    例如，不是：

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    而是使用：

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```

1. 验证是否已正确加载 Azure AD B2C。 然后，使用本地帐户登录。
1. 对于其余策略重复此测试。

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

- 将 &lt;custom-domain-name&gt; 替换为你的自定义域名。
- 将 &lt;tenant-name&gt; 替换为你的租户名称或租户 ID。

以下示例显示了一个有效的 OAuth 重定向 URI：

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

如果选择使用[租户 ID](#optional-use-tenant-id)，有效的 OAuth 重定向 URI 将如以下示例所示：

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML 标识提供者](saml-identity-provider-technical-profile.md)元数据将如以下示例所示：

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="configure-your-application"></a>配置应用程序 

在配置并测试自定义域后，可以更新应用程序来加载将自定义域指定为主机名（而不是 Azure AD B2C 域）的 URL。 

自定义域集成适用于身份验证终结点，它们使用 Azure AD B2C 策略（用户流或自定义策略）对用户进行身份验证。 这些终结点可能如以下示例所示：

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

将：
- custom-domain 替换为你的自定义域名
- tenant-name 替换为租户名称或租户 ID
- policy-name 替换为你的策略名称。 [详细了解 Azure AD B2C 策略](technical-overview.md#identity-experiences-user-flows-or-custom-policies)。 


[SAML 服务提供商](./saml-service-provider.md)元数据可能如以下示例所示： 

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

在添加自定义域和配置应用程序后，用户仍能访问 &lt;tenant-name&gt;.b2clogin.com 域。 要阻止访问，可以将策略配置为对照允许的域列表检查授权请求的“主机名”。 主机名是指 URL 中显示的域名。 通过`{Context:HostName}`[声明解析器](claim-resolver-overview.md)可获取主机名。 然后，则可以显示自定义错误消息。 

1. 获取检查来自 [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name) 的主机名的条件访问策略的示例。
1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。
1. 按以下顺序上传策略文件：先是 `B2C_1A_TrustFrameworkExtensions_HostName.xml`，`B2C_1A_signup_signin_HostName.xml` 次之。

::: zone-end

## <a name="troubleshooting"></a>疑难解答

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C 返回“未找到页面”错误

- 症状 - 配置自定义域后，在尝试使用自定义域登录时，收到 HTTP 404 错误消息。
- 可能的原因 - 此问题可能与 DNS 配置或 Azure Front Door 后端配置相关。 
- **解决方法**：  
    1. 请确保该自定义域在 Azure AD B2C 租户中[已注册并成功通过验证](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant)。
    1. 请确保该[自定义域](../frontdoor/front-door-custom-domain.md)配置正确。 自定义域的 `CNAME` 记录必须指向 Azure Front Door 默认的前端主机（例如 contoso-frontend.azurefd.net）。
    1. 确保 [Azure Front Door 后端池配置](#22-add-backend-and-backend-pool)指向从中设置自定义域名以及存储用户流或自定义策略的租户。


### <a name="azure-ad-b2c-returns-the-resource-you-are-looking-for-has-been-removed-had-its-name-changed-or-is-temporarily-unavailable"></a>Azure AD B2C 返回“你查找的资源已被删除，已更改名称或暂不可用”。

- 症状 - 配置自定义域后，尝试使用它登录时，收到“你查找的资源已被删除、已更改名称或暂不可用”的错误消息。
- 可能的原因 - 此问题可能与 Azure AD 自定义域验证相关。 
- 解决方法：确保该自定义域在 Azure AD B2C 租户中[已注册并“成功通过验证”](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) 。

### <a name="identify-provider-returns-an-error"></a>标识提供者返回错误

- 症状 - 配置自定义域后，可以使用本地帐户登录。 但若使用来自外部[社交或企业标识提供者](add-identity-provider.md)的凭据登录，标识提供者会显示错误消息。
- 可能的原因 - 当 Azure AD B2C 引导用户使用联合标识提供者登录时，会指定重定向 URI。 重定向 URI 是标识提供者返回令牌的终结点。 重定向 URI 与应用程序对授权请求使用的域相同。 如果重定向 URI 尚未在标识提供者处注册，则它可能不信任新的重定向 URI，从而导致错误消息。 
- 解决方法 - 按照[配置标识提供者](#configure-your-identity-provider)中的步骤添加新的重定向 URI。 

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>能否使用 Azure Front Door 高级配置，例如 Web 应用程序防火墙规则？ 
  
虽然 Azure Front Door 高级配置设置不受官方支持，但你可以使用它们，不过风险自担。 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>在使用“立即运行”尝试运行我的策略时，为什么看不到自定义域？

复制 URL，手动更改域名，然后将其粘贴回你的浏览器。

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>对于 Azure AD B2C 显示哪个 IP 地址？ 用户的 IP 地址还是 Azure Front Door IP 地址？

Azure Front Door 传递用户的原始 IP 地址。 这就是你将在审核报告或自定义策略中要看到的 IP 地址。

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>是否可以对 B2C 使用第三方 Web 应用程序防火墙 (WAF)？

要在 Azure Front Door 之前使用自己的 Web 应用程序防火墙，需要配置并验证所有设置是否可与你的 Azure AD B2C 用户流或自定义策略正常配合运行。  

### <a name="can-my-azure-front-door-instance-be-hosted-in-a-different-subscription-than-my-azure-ad-b2c-tenant"></a>我的 Azure Front Door 实例是否可以托管在与 Azure AD B2C 租户不同的订阅中？
    
是的，Azure Front Door 可以在不同的订阅中。
    
## <a name="next-steps"></a>后续步骤

了解 [OAuth 身份验证请求](protocols-overview.md)。
