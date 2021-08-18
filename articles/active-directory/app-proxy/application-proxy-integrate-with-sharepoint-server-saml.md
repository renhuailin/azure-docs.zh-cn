---
title: 使用 Azure Active Directory 应用程序代理发布本地 SharePoint 场
description: 介绍如何将本地 SharePoint 场与用于 SAML 的 Azure Active Directory 应用程序代理相集成的基础知识。
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
ms.openlocfilehash: 9ef88340c7bac7f09a532c12ba5a2ab69248046e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727861"
---
# <a name="integrate-azure-active-directory-application-proxy-with-sharepoint-saml"></a>将 Azure Active Directory 应用程序代理与 SharePoint (SAML) 集成

本分步指南介绍了如何使用 Azure AD 应用程序代理来保护对 [Azure Active Directory 集成本地 SharePoint (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) 的访问，在此情况下，组织中的用户（Azure AD、B2B）通过 Internet 连接到 SharePoint。

> [!NOTE]
> 如果你不熟悉 Azure AD 应用程序代理，希望了解详细信息，请参阅[通过 Azure AD 应用程序代理远程访问本地应用程序](./application-proxy.md)。

此设置具有以下三个主要优势：

- Azure AD 应用程序代理确保经过身份验证的流量可以访问你的内部网络和 Sharepoint。
- 你的用户可以照常访问 SharePoint 站点，而无需使用 VPN。
- 你可以通过 Azure AD 应用程序代理级别的用户分配来控制访问，并且可以通过条件访问和多重身份验证 (MFA) 等 Azure AD 功能来提高安全性。

此过程需要两个企业应用程序。 一个是从库中发布到托管的 SaaS 应用列表的 SharePoint 本地实例。 另一个是将用于发布第一个企业库应用程序的本地应用程序（非库应用程序）。

## <a name="prerequisites"></a>先决条件

若要完成此配置，需要以下资源：
 - 一个 SharePoint 2013 场或更高版本的场。 SharePoint 场必须[与 Azure AD 集成](../saas-apps/sharepoint-on-premises-tutorial.md)。
 - Azure AD 租户及计划，其中包含应用程序代理。 详细了解 [Azure AD 计划和定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。
 - Azure AD 租户中[已验证的自定义域](../fundamentals/add-custom-domain.md)。 已验证的域必须与 SharePoint URL 后缀相匹配。
 - 需要 SSL 证书。 请参阅[自定义域发布](./application-proxy-configure-custom-domain.md)中的详细信息。
 - 本地 Active Directory 用户必须与 Azure AD Connect 同步，并且必须配置为[登录到 Azure](../hybrid/plan-connect-user-signin.md)。 
 - 对于仅限云的 B2B 来宾用户，需要[在 Azure 门户中向来宾帐户授予对本地 SharePoint 的访问权限](../saas-apps/sharepoint-on-premises-tutorial.md#manage-guest-users-access)。
 - 在企业域中的计算机上安装并运行的应用程序代理连接器。


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>步骤 1：将本地 SharePoint 与 Azure AD 集成

1. 配置本地 SharePoint 应用。 有关详细信息，请参阅[教程：Azure Active Directory 单一登录与本地 SharePoint 的集成](../saas-apps/sharepoint-on-premises-tutorial.md)。
2. 在转到下一步之前验证配置。 若要进行验证，请尝试从内部网络访问本地 SharePoint，并确认它可在内部访问。


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>步骤 2：通过应用程序代理发布本地 SharePoint 应用程序

在此步骤中，你可在使用应用程序代理的 Azure AD 租户中创建应用程序。 可设置外部 URL，并指定内部 URL，以便稍后用于 SharePoint。

> [!NOTE]
> 内部和外部 URL 必须与步骤 1 中基于 SAML 的应用程序配置中的登录 URL 相匹配。

   ![显示“登录 URL”值的屏幕截图。](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. 使用自定义域创建新的 Azure AD 应用程序代理应用程序。 有关分步说明，请参阅 [Azure AD 应用程序代理中的自定义域](./application-proxy-configure-custom-domain.md)。

    - 内部 URL： https://portal.contoso.com/
    - 外部 URL： https://portal.contoso.com/
    - 预身份验证：Azure Active Directory
    - 转换标头中的 URL：否
    - 转换应用程序主体中的 URL：否

        ![显示用于创建应用的选项的屏幕截图。](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. 分配你之前分配给本地 SharePoint 库应用程序的[相同的组](../saas-apps/sharepoint-on-premises-tutorial.md#grant-permissions-to-a-security-group)。

3. 最后，转到“属性”部分，并将“对用户可见?”设置为“否”  。 此选项可确保仅第一个应用程序的图标显示在“我的应用门户”上 (https://myapplications.microsoft.com) 。

   ![显示在何处设置“对用户可见?”选项的屏幕截图。](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>第 3 步：测试应用程序

从外部网络上的计算机使用浏览器，导航到在发布步骤中配置的链接。 请确保可以用设置的测试帐户进行登录。
