---
title: 具有 Azure AD 应用 Proxy 的本地应用的基于标头的单一登录
description: 了解如何为基于标头的身份验证保护的本地应用程序提供单一登录。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c30d8a66cf9deffaa32e5ec0356a9067575b77e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660696"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>基于标头的单一登录，适用于具有 Azure AD 应用 Proxy (预览版的本地应用) 

Azure Active Directory (Azure AD) 应用程序代理在本地支持对使用标头进行身份验证的应用程序进行单一登录访问。 你可以在 Azure AD 中配置应用程序所需的标头值。 标头值将通过应用程序代理发送到应用程序。 对应用程序代理使用基于标头的身份验证的本机支持的一些优点包括：  

* **简化提供对本地应用的远程访问** -应用代理允许简化现有远程访问体系结构。 可以替换对这些应用的 VPN 访问。 你还可以删除本地标识解决方案的依赖关系，以便进行身份验证。 用户登录到使用你的企业应用程序时不会发现任何不同的内容。 他们仍可在任何位置的任何设备上工作。  

* **应用无其他软件或更改** -你可以使用现有的应用程序代理连接器，并且无需安装任何其他软件。  

* **可用的属性和转换的宽列表** -所有可用标头值都基于 Azure AD 颁发的标准声明。 可用于为 [SAML 或 OIDC 应用程序配置声明](../develop/active-directory-saml-claims-customization.md#attributes) 的所有属性和转换也可用作标头值。 

## <a name="pre-requisites"></a>先决条件
开始在基于标头的身份验证应用进行单一登录之前，请确保你的环境已准备好以下设置和配置：
- 你需要启用应用程序代理，并将具有站点线路的连接器安装到你的应用程序。 请参阅教程 [通过应用程序代理添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) ，了解如何准备本地环境、安装和注册连接器，以及测试连接器。 

## <a name="supported-capabilities"></a>支持的功能

下表列出了应用程序代理支持的基于标头的身份验证应用程序所需的常见功能。 

|要求   |说明|
|----------|-----------|
|联合 SSO |在预身份验证模式下，所有应用程序都受到 Azure AD 身份验证的保护，并使用户能够使用单一登录。 |
|远程访问 |应用程序代理启用对应用的远程访问。 用户可以使用外部 URL 在任何浏览器上通过 internet 访问应用程序。 应用程序代理不用于公司访问。 |
|基于标头的集成 |应用程序代理执行 SSO 与 Azure AD 的集成，然后将标识或其他应用程序数据作为 HTTP 标头传递到应用程序。 |
|应用程序授权 |可以根据要访问的应用程序、用户的组成员身份和其他策略来指定公用策略。 在 Azure AD 中，使用 [条件访问](../conditional-access/overview.md)来实现策略。 应用程序授权策略仅适用于初始身份验证请求。 |
|单步身份验证 |例如，可以将策略定义为强制添加身份验证，以获取对敏感资源的访问权限。 |
|精细授权 |提供 URL 级别的访问控制。 根据所访问的 URL，可以强制执行已添加的策略。 为应用配置的内部 URL 定义策略所应用到的应用的作用域。 强制执行为最精细路径配置的策略。  |

> [!NOTE] 
> 本文介绍如何使用应用程序代理将基于标头的身份验证应用程序连接到 Azure AD，这是建议的模式。 作为替代方法，还可以使用 PingAccess 与 Azure AD 的集成模式来启用基于标头的身份验证。 有关更多详细信息，请参阅 [应用程序代理和 PingAccess 的单一登录的基于标头的身份验证](application-proxy-ping-access-publishing-guide.md)。

## <a name="how-it-works"></a>工作原理

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="基于标头的单一登录如何与应用程序代理配合使用。" lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. 管理员在 Azure AD 门户中自定义应用程序所需的属性映射。 
2. 当用户访问应用程序时，应用程序代理会确保通过 Azure AD 对用户进行身份验证 
3. 应用程序代理云服务识别所需的属性。 因此，该服务将从身份验证期间接收的 ID 令牌中获取相应的声明。 然后，该服务会将值转换为所需的 HTTP 标头，作为对连接器的请求的一部分。 
4. 然后将该请求传递到连接器，然后将其传递给后端应用程序。 
5. 应用程序接收标头，并可根据需要使用这些标头。 

## <a name="publish-the-application-with-application-proxy"></a>用应用程序代理发布应用程序

1. 按照 [通过应用程序代理发布应用](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)程序中所述的说明发布应用程序。  
    - 内部 URL 值决定了应用程序的作用域。 如果在应用程序的根路径中配置内部 URL 值，则根下的所有子路径都将接收到相同的标头配置和其他应用程序配置。 
    - 创建一个新的应用程序，以将不同的标头配置或用户分配设置为比你配置的应用程序更精细的路径。 在新应用程序中，用所需的特定路径配置内部 URL，然后配置此 URL 所需的特定标头。 应用程序代理始终会将配置设置与为应用程序设置的最精细路径匹配。 

2. 选择 " **Azure Active Directory**   " 作为 **预身份验证方法**。 
3. 通过导航到 " **用户和组** " 并分配适当的用户和组来分配测试用户。 
4. 打开浏览器并导航到 ****   应用程序代理设置中的外部 URL。 
5. 验证是否可以连接到应用程序。 即使你可以连接，但你仍无法访问该应用，因为标头未配置。 

## <a name="configure-single-sign-on"></a>配置单一登录 
开始使用基于标头的应用程序的单一登录之前，应已安装应用程序代理连接器，并且连接器可以访问目标应用程序。 否则，请按照 [教程： Azure AD 应用程序代理](application-proxy-add-on-premises-application.md)中的步骤操作，   然后返回到此处。 

1. 应用程序显示在企业应用程序列表中后，选择它，然后选择 " **单一登录**"。 
2. 将单一登录模式设置为 " **基于标头**"。 
3. 在 "基本配置" 中， **Azure Active Directory** 将选择默认值。 
4. 在标头中选择编辑铅笔，以配置要发送到应用程序的标头。 
5. 选择 " **添加新标头**"。 提供标头的 **名称** ，选择 " **属性** " 或 " **转换** "，然后从应用程序所需的标头中选择。  
    - 若要了解有关可用属性列表的详细信息，请参阅 [声明自定义-特性](../develop/active-directory-saml-claims-customization.md#attributes)。 
    - 若要详细了解可用的转换列表，请参阅 [声明自定义-声明转换](../develop/active-directory-saml-claims-customization.md#claim-transformations)。 
    - 你还可以添加 **组标头**、将用户所属的所有组或作为标头分配给应用程序的组。 若要了解有关将组配置为值的详细信息，请参阅： [为应用程序配置组声明](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)。 
6. 选择“保存”。 

## <a name="test-your-app"></a>测试应用 

完成所有这些步骤后，应用程序应运行并且可用。 测试应用： 
1. 打开新的浏览器或专用浏览器窗口，确保清除以前缓存的标头。 然后导航到 ****   应用程序代理设置中的外部 URL。
2. 使用分配给应用的测试帐户登录。 如果可以使用 SSO 加载并登录到应用程序，那就好了！ 

## <a name="considerations"></a>注意事项

- 应用程序代理用于提供本地或私有云上的应用程序的远程访问。 不建议使用应用程序代理来处理从公司网络内部发起的流量。
- 应将对基于标头的身份验证应用程序的访问限制为仅限来自连接器或其他基于标头的基于标头的身份验证解决方案的流量。 这通常是通过在应用程序服务器上使用防火墙或 IP 限制限制对应用程序的网络访问来完成的。

## <a name="next-steps"></a>后续步骤

- [什么是单一登录？](what-is-single-sign-on.md)
- [什么是应用程序代理？](what-is-application-proxy.md)
- [应用程序管理的快速入门系列](view-applications-portal.md)
