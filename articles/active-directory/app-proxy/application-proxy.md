---
title: 远程访问本地应用 - Azure AD 应用程序代理
description: Azure Active Directory 应用程序代理提供对本地 Web 应用程序的安全远程访问。 单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云端和本地的应用程序。 例如，应用程序代理可以为远程桌面、SharePoint、Teams、Tableau、Qlik 和业务线 (LOB) 应用程序提供远程访问和单一登录功能。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 609c5adaf6a368d6c5032b1051ded51b60030bae
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232891"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理远程访问本地应用程序

Azure Active Directory 应用程序代理提供对本地 Web 应用程序的安全远程访问。 单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云端和本地的应用程序。 例如，应用程序代理可以为远程桌面、SharePoint、Teams、Tableau、Qlik 和业务线 (LOB) 应用程序提供远程访问和单一登录功能。

Azure AD 应用程序代理的特性：

- **简单易用**。 用户可像访问 Microsoft 365 以及其他与 Azure AD 集成的 SaaS 应用一样访问你的本地应用程序。 无需更改或更新应用程序即可使用应用程序代理。

- **安全**。 本地应用程序可以使用 Azure 的授权控制和安全分析功能。 例如，本地应用程序可使用条件访问和双重验证。 应用程序代理不要求通过防火墙打开入站连接。

- **经济高效**。 本地解决方案通常需要设置和维护隔离区 (DMZ)、边缘服务器或其他复杂的基础结构。 应用程序代理在云中运行，易于使用。 若要使用应用程序代理，无需更改网络基础结构或在本地环境中安装其他设备。

## <a name="what-is-application-proxy"></a>什么是应用程序代理？
应用程序代理是 Azure AD 的一项功能，它使用户能够从远程客户端访问本地 Web 应用程序。 应用程序代理包括在云中运行的应用程序代理服务和在本地服务器上运行的应用程序代理连接器。 Azure AD、应用程序代理服务和应用程序代理连接器协同工作，将用户登录令牌从 Azure AD 安全地传递到 Web 应用程序。

应用程序代理适用于：

* 使用[集成 Windows 身份验证](./application-proxy-configure-single-sign-on-with-kcd.md)进行身份验证的 Web 应用程序
* 使用基于窗体或[基于标头](./application-proxy-configure-single-sign-on-with-headers.md)的访问的 Web 应用程序
* 想要公开给不同设备上丰富应用程序的 Web API
* 托管在[远程桌面网关](./application-proxy-integrate-with-remote-desktop-services.md)后面的应用程序
* 与 Microsoft 身份验证库 (MSAL) 集成的丰富客户端应用

应用程序代理支持单一登录。 有关支持的方法的详细信息，请参阅[选择单一登录方法](../manage-apps/sso-options.md#choosing-a-single-sign-on-method)。

建议使用应用程序代理为远程用户提供对内部资源的访问权限。 应用程序代理取代了对 VPN 或反向代理的需要。 它并不适合企业网络中的内部用户。  这些用户不必要地使用应用程序代理可能会意外地造成不利的性能问题。

## <a name="how-application-proxy-works"></a>应用程序代理的工作原理

下图显示了 Azure AD 和应用程序代理如何共同向本地应用程序提供单一登录。

![AzureAD 应用程序代理关系图](./media/application-proxy/azureappproxxy.png)

1. 在用户通过终结点访问应用程序后，将其定向到 Azure AD 登录页面。
2. 成功登录后，Azure AD 向用户的客户端设备发送令牌。
3. 客户端将令牌发送到应用程序代理服务，该服务检索令牌中的用户主体名称 (UPN) 和安全主体名称 (SPN)。 然后，应用程序代理将请求发送到应用程序代理连接器。
4. 如果已配置单一登录，则连接器代表用户执行所需的任何其他身份验证。
5. 连接器将请求发送到本地应用程序。
6. 通过连接器和应用程序代理服务将响应发送给用户。

> [!NOTE]
> 与大多数 Azure AD 混合代理一样，应用程序代理连接器不要求你通过防火墙打开入站连接。 第 3 步中的用户流量在应用程序代理服务处（在 Azure AD 中）终止。 应用程序代理连接器（本地）负责其余的通信。
>


| 组件 | 说明 |
| --------- | ----------- |
| 端点  | 终结点是 URL 或[最终用户门户](../manage-apps/end-user-experiences.md)。 用户可通过访问外部 URL 访问位于你网络外部的应用程序。 网络内的用户可以通过 URL 或最终用户门户访问应用程序。 当用户转到其中一个终结点时，将在 Azure AD 中进行身份验证，并通过连接器路由到本地应用程序。|
| Azure AD | Azure AD 使用存储在云端的租户目录执行身份验证。 |
| 应用程序代理服务 | 应用程序代理服务作为 Azure AD 的一部分在云中运行。 它将登录令牌从用户传递到应用程序代理连接器。 应用程序代理在收到请求时转发任何可访问的标头，并根据其协议将标头设置为客户端 IP 地址。 如果传入代理的请求已有该标头，则将客户端 IP 地址添加到逗号分隔列表的末尾，该地址为标头的值。|
| 应用程序代理连接器 | 连接器是可在网络内的 Windows Server 上运行的轻型代理。 连接器管理云端应用程序代理服务与本地应用程序之间的通信。 它只使用出站连接，因此不需要开放任何入站端口或在 DMZ 中放置任何对象。 连接器是无状态的，可根据需要从云中提取信息。 有关连接器的详细信息（例如，它们如何均衡负载和执行身份验证），请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)。|
| Active Directory (AD) | Active Directory 在本地运行，对域帐户执行身份验证。 配置单一登录后，连接器会与 AD 通信以执行所需的任何其他身份验证。
| 本地应用程序 | 最后，用户便可以访问本地应用程序。

## <a name="next-steps"></a>后续步骤
若要开始使用应用程序代理，请参阅[教程：添加本地应用程序以通过应用程序代理远程访问](application-proxy-add-on-premises-application.md)。