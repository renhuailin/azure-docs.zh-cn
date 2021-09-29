---
title: 使用 Azure Active Directory 应用程序代理的本地应用的 SAML 单一登录
description: 了解如何提供对使用 SAML 身份验证保护的本地应用程序的单一登录。 通过应用程序代理提供对本地应用的远程访问。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 6fbffc3b9a8c45aca7fa61b45dd6f34b300864e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773961"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>使用应用程序代理的本地应用程序的 SAML 单点登录

可以为使用 SAML 身份验证保护的本地应用程序提供单一登录 (SSO) 并通过应用程序代理提供对这些应用程序的远程访问。 通过 SAML 单一登录，Azure Active Directory (Azure AD) 使用用户的 Azure AD 帐户对应用程序进行身份验证。 Azure AD 通过连接协议将登录信息传递给应用程序。 你还可以根据在 SAML 声明中定义的规则将用户映射到特定的应用程序角色。 通过启用除 SAML SSO 之外的应用程序代理，用户可以对应用程序和无缝 SSO 体验进行外部访问。

应用程序必须能够使用 Azure Active Directory 颁发的 SAML 令牌。 此配置不适用于使用本地标识提供者的应用程序。 对于这些情况，建议查看[用于将应用程序迁移到 Azure AD 的资源](../manage-apps/migration-resources.md)。

使用应用程序代理的 SAML SSO 还适用于 SAML 令牌加密功能。 有关详细信息，请参阅[配置 Azure AD SAML 令牌加密](../manage-apps/howto-saml-token-encryption.md)。

下面的协议关系图描述了服务提供者启动的（SP 启动的）流以及标识提供者启动的（IdP 启动的）流的单一登录顺序。 应用程序代理通过在本地应用程序中缓存 SAML 请求和响应来与 SAML SSO 配合使用。

  ![下图显示了用于 SP 启动的单一登录的应用程序、应用程序代理、客户端和 Azure AD 的交互。](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![下图显示了用于 IdP 启动的单一登录的应用程序、应用程序代理、客户端和 Azure AD 的交互。](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>创建应用程序并设置 SAML SSO

1. 在 Azure 门户中，选择“Azure Active Directory”>“企业应用程序”，然后选择“新建应用程序” 。

2. 输入新应用程序的显示名称，选择“集成库中未发现的任何其他应用程序”，然后选择“创建” 。

3. 在应用的“概述”页上，选择“单一登录” 。

4. 选择“SAML”作为单一登录方法。

5. 首先设置 SAML SSO 以在公司网络上运行，若要为应用程序配置基于 SAML 的身份验证，请参阅[配置基于 SAML 的单一登录](../manage-apps/configure-saml-single-sign-on.md)的基本 SAML 配置部分。

6. 至少将一个用户添加到应用程序，并确保测试帐户有权访问应用程序。 连接到公司网络时，使用测试帐户查看是否可以单一登录到应用程序。 

   > [!NOTE]
   > 设置应用程序代理后，返回并更新 SAML“回复 URL”。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>使用应用程序代理发布本地应用程序

需要先启用应用程序代理并安装连接器，才能为本地应用程序提供 SSO。 请参阅教程[添加本地应用程序以通过 Azure AD 中的应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)，了解如何准备本地环境、安装和注册连接器以及测试连接器。 然后按照下列步骤使用应用程序代理发布新应用程序。 对于下面未提及的其他设置，请参阅教程中的[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) 部分。

1. 在 Azure 门户中，如果应用程序仍处于打开状态，请选择“应用程序代理”。 提供应用程序的内部 URL。 如果使用的是自定义域，则还需要为应用程序上传 TLS/SSL 证书。 
   > [!NOTE]
   > 最佳做法是尽可能使用自定义域来获取优化的用户体验。 详细了解[使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。

2. 选择“Azure Active Directory”作为应用程序的“预身份验证”方法 。

3. 复制应用程序的“外部 URL”。 需要此 URL 来完成 SAML 配置。

4. 通过使用测试帐户，尝试使用“外部 URL”打开应用程序以验证是否正确设置了应用程序代理。 如果出现问题，请参阅[应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)。

## <a name="update-the-saml-configuration"></a>更新 SAML 配置

1. 在 Azure 门户中，如果应用程序仍处于打开状态，请选择“单一登录”。 

2. 在“设置 SAML 单一登录”页上，转到“基本 SAML 配置”标题并选择其“编辑”图标（铅笔）  。 确保在“标识符”、“回复 URL”和“注销 URL”字段中填充了在应用程序代理中配置的“外部 URL”   。 为使应用程序代理正常工作，这些 URL 是必需的。 

3. 编辑前面配置的“回复 URL”，使其域可通过应用程序代理在 Internet 上访问。 例如，如果“外部 URL”为 `https://contosotravel-f128.msappproxy.net`，原始“回复 URL”为 `https://contosotravel.com/acs`，则需要将原始“回复 URL”更新为 `https://contosotravel-f128.msappproxy.net/acs`  。

    ![输入基本 SAML 配置数据](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 选中已更新的“回复 URL”旁边的复选框以将其标记为默认值。

   * 将所需的“回复 URL”标记为默认值后，还可以删除以前配置的使用内部 URL 的“回复 URL” 。

   * 对于 SP 启动的流，请确保后端应用程序指定正确的“回复 URL”或断言使用者服务 URL 以接收身份验证令牌。

    > [!NOTE]
    > 如果后端应用程序需要“回复 URL”作为内部 URL，则需要使用[自定义域](application-proxy-configure-custom-domain.md)来匹配内部和外部 URL，或在用户的设备上安装“我的应用”安全登录扩展。 此扩展将自动重定向到相应的应用程序代理服务。 若要安装该扩展，请参阅[“我的应用”安全登录扩展](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510#download-and-install-the-my-apps-secure-sign-in-extension)。
    
## <a name="test-your-app"></a>测试应用

完成所有这些步骤后，应用应会启动并运行。 测试应用：

1. 打开浏览器并导航到发布应用时创建的外部 URL。 
1. 使用分配给应用的测试帐户登录。 应能够加载应用程序并单一登录到应用程序。

## <a name="next-steps"></a>后续步骤

- [Azure AD 应用程序代理如何提供单一登录？](../manage-apps/what-is-single-sign-on.md)
- [应用程序代理故障排除](application-proxy-troubleshoot.md)