---
title: 发布于 = 具有应用程序代理的本地 SharePoint-Azure AD
description: 介绍有关如何将本地 SharePoint 服务器与 SAML Azure AD 应用程序代理集成的基础知识。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bca7331722640547218ecb6aff7c3c5651efdfd0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098435"
---
# <a name="integrate-with-sharepoint-saml"></a>与 SharePoint (SAML) 集成

此循序渐进指南介绍了如何使用 Azure AD 应用程序代理保护对 [Azure Active Directory 集成本地 Sharepoint (SAML) ](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial) 的访问权限，在该应用程序代理中，你所在组织中的用户 (AZURE AD，B2B) 通过 Internet 连接到 Sharepoint。

> [!NOTE] 
> 如果你不熟悉 Azure AD 应用程序代理并想要了解详细信息，请参阅 [通过 Azure AD 应用程序代理远程访问本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

此设置具有以下三个主要优势：

- Azure AD 应用程序代理可确保经过身份验证的流量可以访问内部网络和 Sharepoint 服务器。
- 你的用户可以像平常一样使用 VPN 访问 Sharepoint 站点。
- 你可以通过 Azure AD 应用程序代理级别上的用户分配来控制访问权限，并且可以通过 Azure AD 功能（如条件访问和多重身份验证 (MFA) ）来提高安全性。

此过程需要两个企业应用程序。 一种是从库发布到托管 SaaS 应用列表的 SharePoint 本地实例。 第二个是 (非库应用程序的本地应用程序) 你将用于发布第一个企业库应用程序。

## <a name="prerequisites"></a>先决条件

若要完成此配置，需要以下资源：
 - 一个 SharePoint 2013 场或更高版本的场。 Sharepoint 场必须 [与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial)。
 - 具有包含应用程序代理的计划的 Azure AD 租户。 了解 [Azure AD 计划和定价](https://azure.microsoft.com/pricing/details/active-directory/)的详细信息。
 - Azure AD 租户中的 [已验证的自定义域](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 。 验证的域必须与 SharePoint URL 后缀相匹配。
 - SSL 证书是必需的。 请参阅自定义域 [发布](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)中的详细信息。
 - 本地 Active Directory 用户必须与 Azure AD Connect 同步，并且必须配置为 [登录到 Azure](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin)。 
 - 对于仅限云的 B2B 来宾用户，需要 [在 Azure 门户中向本地 SharePoint 授予来宾帐户的访问权限](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)。
 - 在企业域中的计算机上安装并运行的应用程序代理连接器。


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>步骤1：将本地 SharePoint 与 Azure AD 集成 

1. 配置 SharePoint 本地应用程序。 有关详细信息，请参阅 [教程： Azure Active Directory 与本地 SharePoint 的单一登录集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial)。
2. 在转到下一步之前验证配置。 若要进行验证，请尝试从内部网络访问本地 SharePoint 并确认它可在内部访问。 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>步骤2：通过应用程序代理发布 Sharepoint 本地应用程序

在此步骤中，会在使用应用程序代理的 Azure AD 租户中创建应用程序。 设置外部 URL，并指定内部 URL，这两个 URL 将在以后的 SharePoint 中使用。

> [!NOTE] 
> 内部和外部 Url 必须与步骤1中基于 SAML 的应用程序配置中的 **登录 URL** 相匹配。

   ![显示 "登录 URL" 值的屏幕截图。](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. 使用自定义域创建新的 Azure AD 应用程序代理应用程序。 有关分步说明，请参阅 [Azure AD 应用程序代理中的自定义域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)。

    - 内部 URL： https://portal.contoso.com/
    - 外部 URL： https://portal.contoso.com/
    - 预身份验证： Azure Active Directory
    - 转换标头中的 Url：否
    - 转换应用程序主体中的 Url：否

        ![显示用于创建应用的选项的屏幕截图。](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. 分配分配给本地 SharePoint 库应用程序的 [相同组](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial#create-an-azure-ad-security-group-in-the-azure-portal) 。

3. 最后，请跳到 " **属性** " 部分，并将 **"用户可见"** 设置为 " **否**"。 此选项可确保在 "我的应用" 门户 (上只显示第一个应用程序的图标 https://myapplications.microsoft.com) 。

   ![显示要将可见的位置设置为用户的屏幕截图选.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>第 3 步：测试应用程序

从外部网络上的计算机使用浏览器，导航到在 https://portal.contoso.com/) 发布步骤中配置的 URL (。 请确保可以用设置的测试帐户登录。

