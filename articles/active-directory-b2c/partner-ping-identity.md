---
title: 有关为 Azure Active Directory B2C 配置 Ping 标识的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 Ping 标识集成
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 04aa6f6a7f99eed32c12cf5459c7a7e49089ce6c
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962312"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>教程：为 Azure Active Directory B2C 配置 Ping 标识以实现安全的混合访问

此示例教程介绍如何使用 [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 和 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) 扩展 Azure Active Directory (AD) B2C 以实现安全的混合访问。

许多现有的 Web 资产（如电子商务站点和向 Internet 公开的 Web 应用程序）都部署在代理系统（有时称为反向代理系统）的后面。 这些代理系统提供各种功能，包括预身份验证、强制实施策略和路由流量。 示例用例包括保护 Web 应用程序避免入站 Web 流量以及在分布式服务器部署中提供统一的会话管理。

在大多数情况下，此配置会包括身份验证转换层，该转换层从 Web 应用程序将身份验证外部化。 反向代理进而会将经过身份验证的用户的上下文提供到 Web 应用程序，采用的形式更加简单（如采用明文或摘要形式标头值）。 在此类配置中，应用程序不使用任何行业标准令牌（如安全断言标记语言 (SAML)、OAuth 或 Open ID Connect (OIDC)），而是依赖于代理来提供身份验证上下文并维护与最终用户代理（如浏览器或本机应用程序）的会话。 作为以“中间人”形式运行的服务，代理可以提供最终的会话控制。 这也意味着，代理服务应该会非常高效并且是可缩放的，不会成为代理服务后面的应用程序的瓶颈或单一故障点。 此图描述了典型的反向代理实现和通信流。

![显示反向代理实现的图片](./media/partner-ping/reverse-proxy.png)

如果要在此类配置中使标识平台现代化，需要考虑以下问题。

- 如何将应用程序现代化的工作与标识平台现代化分离？

- 如何建立一个在使用现代化标识服务提供程序时新式和旧式身份验证共存的环境？

  a. 如何提高最终用户体验的一致性？

  b. 如何在共存的应用程序间提供单一登录体验？

我们介绍一种通过将 Azure AD B2C 与 [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 和 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) 技术集成来解决这些问题的方法。

## <a name="coexistence-environment"></a>共存环境

在技术上切实可行同时也经济高效的简单解决方案是将反向代理系统配置为使用现代化标识系统，委托身份验证。  
在这种情况下，代理将支持新式身份验证协议，并使用会将用户发送到新标识提供者 (IDP) 的基于重定向（被动）的身份验证。

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C 作为标识提供者

Azure AD B2C 能够定义用于驱动不同用户体验和行为（也称为“用户旅程”）的策略，就像从服务器端进行协调 。 这种策略中的每一个都会公开一个协议终结点，该终结点可以执行身份验证，就像它是 IDP 一样。 对于特定策略，在应用程序端不需要进行特殊处理。 应用程序只是向由所需策略公开的特定于协议的身份验证终结点发出行业标准身份验证请求。  
可以将 Azure AD B2C 配置为在多个策略之间共享同一颁发者或每个策略一个独一无二的颁发者。 每个应用程序都可以通过发出协议本机身份验证请求来指向这些策略中的一个或多个，并且可以驱动所需用户行为，如登录、注册和编辑配置文件。 此图显示了 OIDC 和 SAML 应用程序工作流。

![显示 OIDC 和 SAML 实现的图片](./media/partner-ping/azure-ad-identity-provider.png)

虽然提到的方案非常适用于现代化的应用程序，但对旧应用程序而言可能很难正确地重定向用户，因为对这些应用程序的访问请求可能不包含用户体验的上下文。 在大多数情况下，代理层或 Web 应用程序上的集成代理会截获访问请求。

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess 作为反向代理

如本文前面所述，许多客户都部署了 PingAccess 作为反向代理来发挥一种或多种作用。 PingAccess 可以通过作为中间人的方式，或作为来自 Web 应用程序服务器上运行的代理的重定向，来截获直接请求。

可以使用 OIDC、OAuth2 或 SAML 来配置 PingAccess，以便针对上游身份验证提供程序执行身份验证。 为此，可以在 PingAccess 服务器上配置单个上游 IdP。 下图显示了此配置。

![显示实现了 OIDC 的 PingAccess 的图片](./media/partner-ping/authorization-flow.png)

在有多个策略公开多个 IdP 的典型 Azure AD B2C 部署中，很难采用这种方式。 因为 PingAccess 只能配置一个上游 IdP。  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate 作为联合代理

PingFederate 是企业标识桥，可完全配置为身份验证提供程序，或根据需要配置为其他多个上游 IdP 的代理。 下图显示了这种功能。

![显示实现 PingFederate 的图片](./media/partner-ping/pingfederate.png)

此功能可用于根据上下文/以动态或声明方式将入站请求切换到某个特定的 Azure AD B2C 策略。 下面是此配置的协议序列流的描述。

![显示 PingAccess 和 PingFederate 工作流的图片](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有，可获取[免费帐户](https://azure.microsoft.com/free/)。

- 已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- PingAccess 和 PingFederate 部署在 Docker 容器中或直接位于 Azure VM 上。

## <a name="connectivity"></a>连接

检查以下各项是否已连接。

- PingAccess 服务器 – 能够与 PingFederate 服务器、客户端浏览器、OIDC、OAuth 已知以及 Azure AD B2C 服务和 PingFederate 服务器发布的密钥发现通信。

- PingFederate 服务器 – 能够与 PingAccess 服务器、客户端浏览器、OIDC、OAuth 已知以及 Azure AD B2C 服务发布的密钥发现通信。

- 旧版或基于标头的 AuthN 应用程序 – 能够与 PingAccess 服务器相互通信。

- SAML 依赖方应用程序 – 能够从客户端访问浏览器流量。 能够访问 Azure AD B2C 服务发布的 SAML 联合元数据。

- 新式应用程序 – 能够从客户端访问浏览器流量。 能够访问 OIDC、OAuth 已知和 Azure AD B2C 服务发布的密钥发现。

- REST API – 能够从本机或 Web 客户端访问流量。 能够访问 OIDC、OAuth 已知和 Azure AD B2C 服务发布的密钥发现。

## <a name="configure-azure-ad-b2c"></a>配置 Azure AD B2C

你可以使用基本用户流或高级标识企业框架 (IEF) 策略来实现此目的。 PingAccess 使用基于 [WebFinger](https://tools.ietf.org/html/rfc7033)的发现约定基于 **颁发者** 值生成元数据终结点。
若要遵循此约定，请使用用户流中的策略属性更新 Azure AD B2C 颁发者更新。

![显示标记设置的图像](./media/partner-ping/token-setting.png)

在高级策略中，可以使用 IssuanceClaimPattern 元数据元素将其配置为 [JWT 令牌颁发者技术配置文件](./jwt-issuer-technical-profile.md)中的 AuthorityWithTfp 值 。

## <a name="configure-pingaccesspingfederate"></a>配置 PingAccess/PingFederate

以下部分介绍所需的配置。
此图说明了该集成的整体用户流。

![显示 PingAccess 和 PingFederate 集成的图片](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>将 PingFederate 配置为令牌提供程序

若要将 PingFederate 配置为 PingAccess 的令牌提供程序，请确保已建立从 PingFederate 到 PingAccess 的连接，之后是从 PingAccess 到 PingFederate 的连接。  
有关配置步骤，请参阅[这篇文章](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html)。

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>为基于标头的身份验证配置 PingAccess 应用程序

必须为用于基于标头的身份验证的目标 Web 应用程序创建 PingAccess 应用程序。 请执行以下步骤。

#### <a name="step-1--create-a-virtual-host"></a>步骤 1 – 创建虚拟主机

>[!IMPORTANT]
>若要为此解决方案进行配置，需要为每个应用程序都创建虚拟主机。 若要详细了解配置注意事项及其影响，请参阅[关键注意事项](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)。

请按以下步骤创建虚拟机：

1. 转到“设置” > “访问” > “虚拟主机”  

2. 选择“添加虚拟主机”

3. 在“主机”字段中，输入应用程序 URL 的 FQDN 部分

4. 在“端口”字段中，输入“443”

5. 选择“保存”

#### <a name="step-2--create-a-web-session"></a>步骤 2 – 创建 Web 会话

请按以下步骤创建 Web 会话：

1. 导航到“设置” > “访问” > “Web 会话”  

2. 选择“添加 Web 会话”

3. 为该 Web 应用提供“名称”。

4. 选择“Cookie 类型”，即“签名的 JWT”或“加密的 JWT”  

5. 为“受众”提供唯一名称

6. 在“客户端”字段中，输入 Azure AD 应用程序 ID 

7. 在“客户端密码”字段中，输入你在 Azure AD 中为应用程序生成的密钥 。

8. 可选 - 可以使用 Microsoft Graph API 来创建和使用自定义声明。 如果选择这样做，请选择“高级”，并取消选择“请求配置文件”和“刷新用户属性”选项  。 若要详细了解如何使用自定义声明，请参阅[使用自定义声明](../active-directory/app-proxy/application-proxy-configure-single-sign-on-with-headers.md)。

9. 选择“保存”

#### <a name="step-3--create-identity-mapping"></a>步骤3 – 创建标识映射

>[!NOTE]
>如果多个应用程序需要在标头中有相同数据，标识映射可用于多个应用程序。

按照以下步骤创建标识映射：

1. 转到“设置” > “访问” > “标识映射”  

2. 选择“添加标识映射”

3. 指定“名称”

4. 选择“标头标识映射的类型”标识映射

5. 在“属性映射”表中，指定所需的映射。 例如，

   属性名称 | 标头名称 |
   |-------|--------|
   |upn | x-userprinciplename |
   |电子邮件   |    x-email  |
   |oid   | x-oid  |
   |scp   |     x-scope |
   |amr    |    x-amr    |

6. 选择“保存”

#### <a name="step-4--create-a-site"></a>步骤 4 – 创建站点

>[!NOTE]
>在有些配置中，站点可能包含多个应用程序。 站点可用于多个应用程序（如果适用）。

按照以下步骤创建站点：

1. 转到“主要” > “站点” 

2. 选择“添加站点”

3. 指定站点的“名称”

4. 输入站点“目标”。 目标是承载应用程序的服务器的 hostname:port 对。 请不要在此字段中输入应用程序的路径。 例如，位于 https://mysite:9999/AppName 的应用程序的目标值将为 mysite: 9999

5. 指示该目标是否需要安全连接。

6. 如果该目标需要安全连接，请将受信任的证书组设置为“信任任意”。

7. 选择“保存”

#### <a name="step-5--create-an-application"></a>步骤 5 – 创建应用程序

按照以下步骤为 Azure 中要保护的每个应用程序在 PingAccess 中创建应用程序。

1. 转到“主要” > “应用程序” 

2. 选择“添加应用程序”

3. 指定应用程序的“名称”

4. 输入应用程序的“说明”（可选）

5. 为该应用程序指定“上下文根”。 例如，位于 https://mysite:9999/AppName 的应用程序的上下文根将会是 /AppName。 上下文根必须以斜杠 (/) 开头，不能以斜杠 (/) 结尾，并且可以有多层深度，例如 /Apps/MyApp。

6. 选择你创建的虚拟主机

   >[!NOTE]
   >虚拟主机和上下文根的组合在 PingAccess 中必须是独一无二的。

7. 选择你创建的 Web 会话

8. 选择你创建的包含该应用程序的站点

9. 选择你创建的标识映射

10. 选择“已启用”，以便在保存时启用该站点

11. 选择“保存”

### <a name="configure-the-pingfederate-authentication-policy"></a>配置 PingFederate 身份验证策略

将 PingFederate 身份验证策略配置为联合到由 Azure AD B2C 租户提供的多个 IdP

1. 创建用于在 IdP 与 SP 之间桥接属性的协定。 有关详细信息，请参阅[联合中心和身份验证策略协定](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)。 你可能只需要一个协定，除非 SP 要求每个 IdP 都有一组不同的属性。

2. 对于每个 IdP，在 IdP 和 PingFederate 之间创建 IdP 连接，联合中心作为 SP。

3. 在“目标会话映射”窗口中，将适用的身份验证策略协定添加到 IdP 连接。

4. 在“选择器”窗口中，配置身份验证选择器。 例如，请参阅标识符第一适配器的实例，以将每个 IdP 映射到身份验证策略中相应的 IdP 连接。

5. 在 PingFederate 与 SP 之间创建 SP 连接（联合中心作为 IdP）。

6. 将相应的身份验证策略协定添加到“身份验证源映射”窗口上的 SP 连接。

7. 处理每个 IdP 以连接到 PingFederate，联合中心作为 SP。

8. 处理 SP 以连接到 PingFederate，联合中心作为 IdP。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)