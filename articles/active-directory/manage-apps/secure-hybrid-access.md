---
title: Azure AD 安全混合访问 | Microsoft Docs
description: 本文介绍将旧版本地、公有云或私有云应用程序与 Azure AD 集成的合作伙伴解决方案。 通过将应用交付控制器或网络连接到 Azure AD 保护旧版应用。
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: d7cafd5c3586feb99aeade5af7f858659baf6100
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744098"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全混合访问：使用 Azure Active Directory 保护旧应用

现在可以通过使用以下功能，将本地的和云中的旧身份验证应用程序连接到 Azure Active Directory (AD)，以保护这些应用程序：

- [Azure AD 应用程序代理](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [现有的应用程序传送控制器和网络](#sha-through-networking-and-delivery-controllers)

- [虚拟专用网络 (VPN) 应用程序和软件定义的外围 (SDP) 应用程序](#sha-through-vpn-and-sdp-applications)

可以使用 Azure AD 功能（如 Azure AD [条件访问](../conditional-access/overview.md)和 Azure AD [标识保护](../identity-protection/overview-identity-protection.md)）跨所有应用程序弥补差异并加强安全态势。

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理进行安全混合访问 (SHA)
  
使用[应用程序代理](../app-proxy/what-is-application-proxy.md)，可以提供对本地 Web 应用程序的[安全远程访问](../app-proxy/application-proxy.md)。 用户不需要使用 VPN。 用户获得的好处是：可以在[单一登录](add-application-portal-setup-sso.md)后轻松地从任何设备连接到他们的应用程序。 应用程序代理将远程访问作为服务提供，并且允许向公司网络外部的用户[轻松发布本地应用程序](../app-proxy/application-proxy-add-on-premises-application.md)。 它有助于缩放云访问管理，不需要你修改本地应用程序。 接下来，请[计划 Azure AD 应用程序代理部署](../app-proxy/application-proxy-deployment-plan.md)。

## <a name="azure-ad-partner-integrations"></a>Azure AD 合作伙伴集成

### <a name="sha-through-networking-and-delivery-controllers"></a>通过网络和传送控制器进行 SHA

为了使你能够使用[零信任框架](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)，除了 [Azure AD 应用程序代理](../app-proxy/what-is-application-proxy.md)外，Microsoft 还与第三方提供商合作。 可以使用现有的网络和传送控制器，并可轻松保护对业务流程至关重要但以前无法使用 Azure AD 进行保护的旧应用程序。 你可能已经具备了开始保护这些应用程序所需的全部条件。

![图中显示了使用网络合作伙伴和应用代理的安全混合访问](./media/secure-hybrid-access/secure-hybrid-access.png)

以下网络供应商提供了有关与 Azure AD 集成的预生成解决方案和详细指南。

- [Akamai 企业应用程序访问 (EAA)](../saas-apps/akamai-tutorial.md)

- [Citrix 应用程序传送控制器 (ADC)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](./f5-aad-integration.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Pulse Secure 虚拟流量管理器 (VTM)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>通过 VPN 和 SDP 应用程序进行 SHA

使用 VPN 和 SDP 解决方案，可在保护组织数据的同时，提供随时随地从任何设备对企业网络进行安全访问的功能。 通过将 Azure AD 作为标识提供者 (IDP)，可以使用新式身份验证和授权方法（如 Azure AD [单一登录](./what-is-single-sign-on.md)和[多重身份验证](../authentication/concept-mfa-howitworks.md)）来保护本地旧应用程序。  

![图中显示了使用 VPN 合作伙伴和应用代理的安全混合访问 ](./media/secure-hybrid-access/app-proxy-vpn.png)

以下 VPN 供应商提供了有关与 Azure AD 集成的预生成解决方案和详细指南。

- [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

- [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

- [F5 Big-IP APM](./f5-aad-password-less-vpn.md)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure Pulse Connect Secure (PCS)](../saas-apps/pulse-secure-pcs-tutorial.md)

以下 SDP 供应商提供了有关与 Azure AD 集成的预生成解决方案和详细指南。

- [Datawiza Access Broker](./add-application-portal-setup-oidc-sso.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)

- [Silverfort Authentication Platform](./add-application-portal-setup-oidc-sso.md)

- [Strata](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md)

- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)
