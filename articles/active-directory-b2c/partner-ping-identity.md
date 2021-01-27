---
title: 配置具有 Ping 标识 Azure Active Directory B2C 的教程
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
ms.openlocfilehash: 430629f94695f0689422434c8d80fe4e1876e5dd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900109"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>教程：使用 Azure Active Directory B2C 配置 Ping 标识以实现安全的混合访问

在此示例教程中，了解如何使用  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 和 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) 扩展 AZURE ACTIVE DIRECTORY (AD) B2C 以实现安全的混合访问。

许多现有的 web 属性（如电子商务站点和向 internet 公开的 web 应用程序）都部署在代理系统之后，有时称为反向代理系统。 这些代理系统提供各种功能，包括预身份验证、策略强制和流量路由。 示例用例包括保护 web 应用程序的入站 web 流量，并跨分布式服务器部署提供统一的会话管理。

在大多数情况下，此配置包括本次 web 应用程序的身份验证的身份验证转换层。 反向代理反过来，将经过身份验证的用户的上下文提供给 web 应用程序，这是一个更简单的形式，如以明文形式或摘要形式标头值。 在此类配置中，应用程序不使用任何行业标准令牌（如安全断言标记语言 (SAML) 、OAuth 或 Open ID Connect (OIDC) ），而是依赖于代理提供身份验证上下文并维护与最终用户代理（如浏览器或本机应用程序）的会话。 作为在 "中间人" 中运行的服务，代理可以提供终极会话控制。 这也意味着，代理服务应该非常高效且可缩放，而不会成为代理服务后面的应用程序的瓶颈或单点故障。 关系图是典型的反向代理实现和通信流的描述。

![图像显示反向代理实现](./media/partner-ping/reverse-proxy.png)

如果要在此类配置中使标识平台现代化，则会引发以下问题。

- 如何将应用程序现代化的工作量与标识平台现代化进行分离？

- 使用现代化 identity service 提供程序时，如何使用新式和旧身份验证建立共存环境？

  a. 如何提高最终用户体验的一致性？

  b. 如何在共存的应用程序中提供单一登录体验？

我们介绍了一种方法来解决此类问题，方法是将 Azure AD B2C 与 [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 和 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) 技术集成。

## <a name="coexistence-environment"></a>共存环境

在技术上可行的简单解决方案是将反向代理系统配置为使用现代化标识系统，委派身份验证。  
在这种情况下，代理将支持新式身份验证协议，并使用基于重定向 (被动) 身份验证 (IdP) 将用户发送到新的标识提供者。

### <a name="azure-ad-b2c-as-an-identity-provider"></a>作为标识提供者 Azure AD B2C

Azure AD B2C 可以定义用于驱动不同用户体验和行为的 **策略** ，这些行为和行为也称为 **用户旅程** ，这与服务器端的协调方式相同。 每个此类策略都公开一个协议终结点，该终结点可以执行身份验证，就像它是 IdP。 对于特定策略，应用程序端不需要特殊处理。 应用程序只是向所需策略公开的特定于协议的身份验证终结点发出行业标准身份验证请求。  
可以将 Azure AD B2C 配置为在多个策略或每个策略的唯一颁发者之间共享相同的颁发者。 每个应用程序都可以通过建立协议本机身份验证请求并驱动所需的用户行为（如登录、注册和配置文件编辑）来指向这些策略中的一个或多个。 此图显示了 OIDC 和 SAML 应用程序工作流。

![图像显示 OIDC 和 SAML 实现](./media/partner-ping/azure-ad-identity-provider.png)

虽然提到的方案非常适合用于现代化应用程序，但在应用程序的访问请求可能不包含用户体验的上下文时，旧应用程序可以更难地将用户重定向到应用程序。 在大多数情况下，代理层或 web 应用程序上的集成代理会截获访问请求。

### <a name="pingaccess-as-a-reverse-proxy"></a>作为反向代理的 PingAccess

许多客户已将 PingAccess 部署为反向代理，以播放一项或多项角色，如本文前面所述。 PingAccess 可以通过作为中间人的方式来截获直接请求，或者通过在 web 应用程序服务器上运行的代理来截获直接请求。

可以使用 OIDC、OAuth2 或 SAML 来配置 PingAccess，以便对上游身份验证提供程序执行身份验证。 可以在 PingAccess 服务器上为此目的配置单个上游 IdP。 下图显示了此配置。

![image 显示了具有 OIDC 实现的 PingAccess](./media/partner-ping/authorization-flow.png)

在一个典型的 Azure AD B2C 部署中，有多个策略公开多个 **idp**，这会带来挑战。 由于 PingAccess 只能配置单个上游 IdP。  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate 作为联合代理

PingFederate 是一个企业标识桥，可以根据需要将其完全配置为身份验证提供程序或其他多个上游 Idp 的代理。 下图显示了此功能。

![图像显示 PingFederate 实现](./media/partner-ping/pingfederate.png)

此功能可用于根据上下文/动态或以声明方式将入站请求切换到特定的 Azure AD B2C 策略。 下面是此配置的协议序列流的描述。

![image 显示了 PingAccess 和 PingFederate 工作流](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>必备条件

若要开始，你将需要：

- Azure 订阅。 如果没有，请获取一个 [免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的 [Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 。

- PingAccess 和 PingFederate 部署在 Docker 容器中或直接位于 Azure Vm 上。

## <a name="connectivity"></a>连接性

检查以下是否已连接。

- **PingAccess 服务器** –能够与 PingFederate 服务器、客户端浏览器、OIDC、Azure AD B2C 服务和 PingFederate 服务器发布的 OAuth 众所周知的密钥发现通信。

- **PingFederate 服务器** –能够与 PingAccess 服务器、客户端浏览器、OIDC、OAuth 众所周知的 Azure AD B2C 服务发布的密钥发现通信。

- **旧版或基于标头的身份验证应用程序** -能够与 PingAccess 服务器通信。

- **SAML 信赖方应用程序** –能够从客户端访问浏览器流量。 能够访问 Azure AD B2C 服务发布的 SAML 联合元数据。

- **新式应用程序** –能够从客户端访问浏览器流量。 能够访问 Azure AD B2C 服务发布的 OIDC、OAuth 众所周知的和密钥发现。

- **REST API** –能够从本机或 web 客户端访问流量。 能够访问 Azure AD B2C 服务发布的 OIDC、OAuth 众所周知的和密钥发现。

## <a name="configure-azure-ad-b2c"></a>配置 Azure AD B2C

你可以使用基本用户流或高级标识企业框架 (IEF) 策略来实现此目的。 PingAccess 使用基于 [WebFinger](https://tools.ietf.org/html/rfc7033)的发现约定基于 **颁发者** 值生成元数据终结点。
若要遵循此约定，请使用用户流中的策略属性更新 Azure AD B2C 颁发者更新。

![图像显示标记设置](./media/partner-ping/token-setting.png)

在高级策略中，可以使用 **IssuanceClaimPattern** metadata 元素对 [JWT 令牌颁发者技术配置文件](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile)中的 **AuthorityWithTfp** 值进行配置。

## <a name="configure-pingaccesspingfederate"></a>配置 PingAccess/PingFederate

以下部分介绍所需的配置。
此图说明了集成的总体用户流。

![image 显示了 PingAccess 和 PingFederate 集成](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>将 PingFederate 配置为令牌提供程序

若要将 PingFederate 配置为 PingAccess 的令牌提供程序，请确保已建立从 PingFederate 到 PingAccess 的连接，后跟从 PingAccess 到 PingFederate 的连接。  
有关配置步骤，请参阅 [此文](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) 。

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>为基于标头的身份验证配置 PingAccess 应用程序

必须为用于基于标头的身份验证的目标 web 应用程序创建 PingAccess 应用程序。 请执行以下步骤。

#### <a name="step-1--create-a-virtual-host"></a>步骤1–创建虚拟主机

>[!IMPORTANT]
>若要为此解决方案配置，需要为每个应用程序创建虚拟主机。 有关配置注意事项及其影响的详细信息，请参阅 [关键注意事项](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)。

按照以下步骤创建虚拟主机：

1. 中转到 "**设置**" "  >  **访问**  >  **虚拟主机**"

2. 选择 "**添加虚拟主机**"

3. 在 "主机" 字段中，输入应用程序 URL 的 FQDN 部分

4. 在 "端口" 字段中，输入 **443**

5. 选择“保存”

#### <a name="step-2--create-a-web-session"></a>步骤 2-创建 web 会话

按照以下步骤创建 web 会话：

1. 导航到 "**设置**" "  >  **访问**  >  **Web 会话**"

2. 选择 "**添加 Web 会话**"

3. 提供 web 会话的 **名称** 。

4. 选择 **Cookie 类型**，即 **签名的 jwt** 或 **加密的 jwt**

5. 为 **受众** 提供唯一值

6. 在 " **客户端 id** " 字段中，输入 **AZURE AD 应用程序 id**

7. 在 " **客户端密码** " 字段中，输入您在 Azure AD 中为应用程序生成的 **密钥** 。

8. 可选-可以使用 Microsoft Graph API 创建和使用自定义声明。 如果选择这样做，请选择 " **高级** "，并取消选择 " **请求配置文件** " 和 " **刷新用户属性** " 选项。 有关使用自定义声明的详细信息，请参阅 [使用自定义声明](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#optional---use-a-custom-claim)。

9. 选择“保存”

#### <a name="step-3--create-identity-mapping"></a>步骤3–创建标识映射

>[!NOTE]
>如果标头中有多个应用程序需要相同的数据，则可以将标识映射用于多个应用程序。

按照以下步骤创建标识映射：

1. 中转到 "**设置**" "  >  **访问**  >  **标识映射**"

2. 选择 "**添加标识映射**"

3. 指定 **名称**

4. 选择 **标头标识映射的** 标识映射类型

5. 在 " **属性映射** " 表中，指定所需的映射。 例如，

   属性名称 | 标头名称 |
   |-------|--------|
   |upn | x-userprinciplename |
   |电子邮件   |    x 电子邮件  |
   |oid   | x-oid  |
   |scp   |     x 范围 |
   |amr    |    x-amr    |

6. 选择“保存”

#### <a name="step-4--create-a-site"></a>步骤 4-创建站点

>[!NOTE]
>在某些配置中，站点可能包含多个应用程序。 站点可用于多个应用程序（如果适用）。

按照以下步骤创建网站：

1. 中转到 **主**  >  **站点**

2. 选择 "**添加站点**"

3. 指定站点的 **名称**

4. 输入站点 **目标**。 目标是承载应用程序的服务器的主机名：端口对。 不要在此字段中输入应用程序的路径。 例如，上的应用程序的 https://mysite:9999/AppName 目标值将为 "我的目标"：9999

5. 指示目标是否需要安全连接。

6. 如果目标需要安全连接，请将受信任的证书组设置为 " **信任**"。

7. 选择“保存”

#### <a name="step-5--create-an-application"></a>步骤5–创建应用程序

按照以下步骤为 Azure 中要保护的每个应用程序创建 PingAccess 中的应用程序。

1. 中转到 **主**  >  **应用程序**

2. 选择 "**添加应用程序**"

3. 指定应用程序的 **名称**

4. （可选）输入应用程序的 **说明**

5. 为应用程序指定 **上下文根目录** 。 例如，上的应用程序 https://mysite:9999/AppName 将具有/AppName. 的上下文根 上下文根必须以斜杠 (/) 开头，不能以斜杠 (/) 结尾，也可以是一个多层深度（例如/Apps/MyApp.）。

6. 选择所创建的 **虚拟主机**

   >[!NOTE]
   >虚拟主机和上下文根的组合在 PingAccess 中必须是唯一的。

7. 选择创建的 **web 会话**

8. 选择您创建的包含该应用程序的 **站点**

9. 选择创建的 **标识映射**

10. 选择 " **已启用** " 以在保存时启用站点

11. 选择“保存”

### <a name="configure-the-pingfederate-authentication-policy"></a>配置 PingFederate authentication 策略

将 PingFederate authentication 策略配置为联合到 Azure AD B2C 租户提供的多个 Idp

1. 创建一个用于在 Idp 与 SP 之间桥接属性的协定。 有关详细信息，请参阅 [联合中心和身份验证策略协定](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)。 你可能只需要一个协定，除非 SP 需要每个 IdP 中有一组不同的属性。

2. 对于每个 IdP，在 IdP 和 PingFederate 之间创建 IdP 连接，联合中心为 SP。

3. 在 " **目标会话映射** " 窗口中，向 IdP 连接添加适用的身份验证策略约定。

4. 在 " **选择器** " 窗口中，配置身份验证选择器。 例如，请参阅 **第一个标识符** 的实例，以将每个 IdP 映射到身份验证策略中的相应 IdP 连接。

5. 在 PingFederate 和 IdP 之间创建 SP 连接，并在 SP 上创建 sp 连接。

6. 将相应的身份验证策略协定添加到 " **身份验证源映射** " 窗口上的 "SP 连接"。

7. 处理每个 IdP 以连接到 PingFederate （作为 SP 的联合身份验证中心）。

8. 使用 SP 连接到 PingFederate，联合中心为 IdP。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
