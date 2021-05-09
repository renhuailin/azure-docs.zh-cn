---
title: 通过 Azure AD 应用代理对本地应用使用基于标头的单一登录
description: 了解如何为使用基于标头的身份验证保护的本地应用程序提供单一登录。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 813e360ffc6c78b52151af5b9867560ae6d58f2f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108168988"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy"></a>通过 Azure AD 应用代理对本地应用使用基于标头的单一登录

Azure Active Directory (Azure AD) 应用程序代理原生支持对使用标头进行身份验证的应用程序进行单一登录访问。 你可以在 Azure AD 中配置应用程序所需的标头值。 标头值将通过应用程序代理发送到应用程序。 使用原生支持对应用程序代理进行基于标头的身份验证的一些好处包括：  

* **简化提供对本地应用程序的远程访问** - 应用代理允许简化现有远程访问体系结构。 可以替换对这些应用的 VPN 访问。 你还可以删除本地标识解决方案的依赖关系，以便进行身份验证。 用户登录后使用企业应用程序时，感觉不到任何差异。 他们仍可在任何位置的任何设备上工作。  

* **无需其他软件或对应用进行更改** - 你可以使用现有的应用程序代理连接器，而无需安装任何其他软件。  

* **广泛的属性和转换列表可用** - 所有可用的标头值均基于 Azure AD 发出的标准声明。 可用于[为 SAML 或 OIDC 应用程序配置声明](../develop/active-directory-saml-claims-customization.md#attributes)的所有属性和转换也可用作标头值。 

## <a name="pre-requisites"></a>先决条件
开始为基于标头的身份验证应用使用单一登录之前，请确保已在环境中完成以下设置和配置：
- 你需要启用应用程序代理，并安装一个连接器，该连接器具有指向应用程序的站点线路。 请参阅教程[添加本地应用程序以通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)，了解如何准备本地环境、安装和注册连接器以及测试连接器。 

## <a name="supported-capabilities"></a>支持的功能

下表列出了应用程序代理支持的基于标头的身份验证应用程序所需的常见功能。 

|要求   |说明|
|----------|-----------|
|联合 SSO |在预身份验证模式下，所有应用程序均受到 Azure AD 身份验证的保护，并使用户能够进行单一登录。 |
|远程访问 |应用程序代理可实现对应用的远程访问。 用户可以使用外部 URL 在任何浏览器上通过 Internet 访问该应用程序。 应用程序代理不适用于企业访问。 |
|基于标头的集成 |应用程序代理与 Azure AD 进行 SSO 集成，然后将标识或其他应用程序数据作为 HTTP 标头传递给应用程序。 |
|应用程序授权 |可以根据所访问的应用程序、用户的组成员身份和其他策略指定常用策略。 在 Azure AD 中，策略是通过[条件访问](../conditional-access/overview.md)实现的。 应用程序授权策略仅适用于初始身份验证请求。 |
|升级身份验证 |可以定义策略以强制执行已添加的身份验证，例如获取对敏感资源的访问权限。 |
|精细授权 |提供 URL 级别的访问控制。 根据所访问的 URL，可以强制执行已添加的策略。 为应用配置的内部 URL 定义了策略所应用到的应用的作用域。 强制执行为最精细路径配置的策略。  |

> [!NOTE] 
> 本文介绍使用应用程序代理将基于标头的身份验证应用程序连接到 Azure AD，并推荐使用这种模式。 另外，还有一种集成模式，该模式使用 PingAccess 和 Azure AD 来启用基于标头的身份验证。 有关详细信息，请参阅[使用应用程序代理和 PingAccess 通过基于标头的身份验证进行单一登录](application-proxy-ping-access-publishing-guide.md)。

## <a name="how-it-works"></a>工作原理

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="基于标头的单一登录如何与应用程序代理配合使用。" lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. 管理员自定义 Azure AD 门户中应用程序所需的属性映射。 
2. 用户访问应用时，应用程序代理可确保用户通过 Azure AD 进行身份验证 
3. 应用程序代理云服务识别所需的属性。 因此，该服务将从身份验证期间接收的 ID 令牌中获取相应的声明。 然后，该服务会将值转换为所需的 HTTP 标头，作为对连接器的请求的一部分。 
4. 然后将该请求传递到连接器，连接器随后将该请求传递给后端应用程序。 
5. 应用程序接收标头，并可根据需要使用这些标头。 

## <a name="publish-the-application-with-application-proxy"></a>使用应用程序代理发布应用程序

1. 根据 [使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)中的说明发布应用程序。  
    - 内部 URL 值决定了应用程序的作用域。 如果在应用程序的根路径上配置内部 URL 值，则根目录下的所有子路径将接收相同的标头配置和其他应用程序配置。 
    - 创建一个新的应用程序，为比已配置的应用程序更精细的路径设置不同的标头配置或用户分配。 在新应用程序中，使用所需的特定路径配置内部 URL，然后配置此 URL 所需的特定标头。 应用程序代理始终会将配置设置与为应用程序设置的最精细路径相匹配。 

2. 选择 Azure Active Directory 作为预身份验证方法 ****   **** 。 
3. 通过导航到“用户和组”并分配适当的用户和组来分配测试用户。 
4. 打开浏览器并从应用程序代理设置导航到“外部 URL” ****  。 
5. 验证是否可以连接到应用程序。 即使可以连接，也无法访问该应用，因为尚未配置标头。 

## <a name="configure-single-sign-on"></a>配置单一登录 
开始为基于标头的应用程序使用单一登录之前，应该已经安装了应用程序代理连接器，并且该连接器可以访问目标应用程序。 否则，请遵循 [教程：Azure AD 应用程序代理](application-proxy-add-on-premises-application.md) 中的步骤，再返回到此处。 

1. 应用程序显示在企业应用程序列表中之后，选择该应用程序并选择“单一登录” **** 。 
2. 将单一登录模式设置为“基于标头”。 
3. 在“基本配置”中，默认选择 Azure Active Directory。 
4. 在“标头”中选择编辑铅笔，以配置要发送到应用程序的标头。 
5. 选择“添加新标头”。 为标头提供名称，选择“属性”或“转换”，然后从下拉列表中选择应用程序需要的标头  。  
    - 若要详细了解可用的属性列表，请参阅[声明自定义 - 属性](../develop/active-directory-saml-claims-customization.md#attributes)。 
    - 若要详细了解可用的转换列表，请参阅[声明自定义 - 声明转换](../develop/active-directory-saml-claims-customization.md#claim-transformations)。 
    - 你还可以添加“组标头”，以发送用户所属的全部组或作为标头分配给应用程序的组。 若要详细了解如何将组配置为值，请参阅：[为应用程序配置组声明](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)。 
6. 选择“保存”。 

## <a name="test-your-app"></a>测试应用程序 

完成所有这些步骤后，应用应会运行并可使用。 测试应用： 
1. 打开新的浏览器或专用浏览器窗口，以确保清除以前缓存的标头。 然后从应用程序代理设置导航到“外部 URL” ****  。
2. 使用分配给应用的测试帐户登录。 如果可以使用 SSO 加载并登录到应用程序，则表示成功！ 

## <a name="considerations"></a>注意事项

- 应用程序代理用于提供对本地或私有云上的应用的远程访问。 不建议使用应用程序代理来处理来自公司网络的内部流量。
- 对基于标头的身份验证应用程序的访问应仅限于来自连接器或其他允许的基于标头的身份验证解决方案的流量。 这通常通过在应用服务器上使用防火墙或 IP 限制以限制对应用程序的网络访问来实现。

## <a name="next-steps"></a>后续步骤

- [什么是单一登录？](what-is-single-sign-on.md)
- [什么是应用程序代理？](what-is-application-proxy.md)
- [应用程序管理的快速入门系列](view-applications-portal.md)
