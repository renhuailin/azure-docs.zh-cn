---
title: 应用程序管理：最佳实践和建议 | Microsoft Docs
description: 学习有关在 Azure Active Directory 中管理应用程序的最佳实践和建议。 了解如何使用自动预配并使用应用程序代理发布本地应用。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: napuri
ms.openlocfilehash: f05668e8614178dcc1071d6c1a8b0ea7ca7b2d1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738916"
---
# <a name="application-management-best-practices"></a>应用程序管理最佳做法

本文包含有关使用自动预配来管理 Azure Active Directory (Azure AD) 中的应用程序以及使用应用程序代理发布本地应用的建议和最佳实践。

## <a name="cloud-app-and-single-sign-on-recommendations"></a>云应用和单一登录建议

| 建议 | 注释 |
| --- | --- |
| 检查应用的 Azure AD 应用程序库  | Azure AD 有一个库，其中包含数千个已启用企业单一登录 (SSO) 的预集成应用程序。 有关特定于应用的安装指南，请参阅 [SaaS 应用教程列表](../saas-apps/tutorial-list.md)。  |
| 使用基于 SAML 的联合 SSO  | 如果应用程序支持基于 SAML 的联合 SSO， 则会将基于 SAML 的联合 SSO 用于 Azure AD，取代基于密码的 SSO 和 ADFS。  |
| 使用 SHA-256 进行证书签名  | 默认情况下，Azure AD 使用 SHA-256 算法为 SAML 响应进行签名。 除非应用程序需要 SHA-1，否则使用 SHA-256（参阅[证书签名选项](certificate-signing-options.md)和[应用程序登录问题](application-sign-in-problem-application-error.md)。）  |
| 要求用户分配  | 默认情况下，用户可以访问你的企业应用程序，而无需将这些应用程序分配给他们。 不过，如果应用程序公开角色或者希望应用程序显示在用户的“我的应用”上，则应该要求用户分配。  |
| 将我的应用部署到用户 | `https://myapps.microsoft.com` 中的[我的应用](end-user-experiences.md)是一个基于 Web 的门户，为用户提供了一个单一入口点，用于登录为用户分配的基于云的应用程序。 添加了组管理和自助密码重置等附加功能后，用户可以在“我的应用”中找到这些功能。 请参阅[计划我的应用部署](my-apps-deployment-plan.md)。
| 使用组分配  | 如果订阅中包含组，则将组分配给应用程序，以便可以将正在进行的访问管理委派给组所有者。  |
| 建立管理证书的流程 | 签名证书的最长生存期为三年。 要防止或最大程度地减少因证书过期而导致的中断，请使用角色和电子邮件通讯组列表，以确保密切监视与证书相关的更改通知。 |

## <a name="provisioning-recommendations"></a>预配建议

| 建议 | 注释 |
| --- | --- |
| 使用教程设置云应用的预配 | 请查看 [SaaS 应用教程列表](../saas-apps/tutorial-list.md)，以获取有关如何为要添加的库应用配置预配的详细指导步骤。 |
| 使用预配日志（预览版）监视状态 | [预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)提供了有关预配服务执行的所有操作的详细信息，包括各个用户的状态。 |
| 将通讯组分配给预配通知电子邮件 | 要提高预配服务发送的关键警报的可见性，请将通讯组分配给通知电子邮件设置。 |

## <a name="application-proxy-recommendations"></a>应用程序代理建议

| 建议 | 注释 |
| --- | --- |
| 使用应用程序代理远程访问内部资源 | 建议将应用程序代理用于向远程用户提供对内部资源的访问权限，以此取代对于 VPN 或反向代理的需求。 应用程序代理不适合于从企业网络访问资源，因为这可能会增加延迟。
| 使用自定义域 | 为应用程序设置自定义域 (参阅 [配置自定义域](../app-proxy/application-proxy-configure-custom-domain.md) ) ，以便用户和应用程序之间的 URL 在网络内部或外部均有效。 你还可以控制你的品牌并自定义你的 URL。  使用自定义域名时，请计划从非 Microsoft 可信证书颁发机构获取公共证书。 Azure 应用程序 Proxy 支持标准 ([通配符](../app-proxy/application-proxy-wildcard.md)) 或基于 SAN 的证书。 （请参阅[应用程序代理计划](../app-proxy/application-proxy-deployment-plan.md)。） |
| 在部署应用程序代理之前同步用户 | 在部署应用程序代理之前，请从本地目录同步用户标识，或直接在 Azure AD 中创建用户标识。 标识同步允许 Azure AD 在授予用户对应用代理发布的应用程序的访问权限之前对用户进行预身份验证。 标识同步还提供必要的用户标识符信息以执行单一登录 (SSO)。 （请参阅[应用程序代理计划](../app-proxy/application-proxy-deployment-plan.md)。） |
| 遵循我们的提示以实现高可用性和负载平衡 | 要了解流量在用户、应用程序代理连接器和后端应用服务器之间流动方式，以及如何获取优化性能和负载均衡的技巧，请参阅[应用程序代理连接器和应用程序的高可用性和负载平衡](../app-proxy/application-proxy-high-availability-load-balancing.md)。 |
| 使用多个连接器 | 使用两个或更多应用程序代理连接器以获得更高的复原能力、可用性和规模（请参阅[应用程序代理连接器](../app-proxy/application-proxy-connectors.md)） 创建连接器组，并确保每个连接器组至少具有两个连接器（最好是三个连接器）。 |
| 找到距离应用程序服务器最近的连接器服务器，并确保它们位于同一个域中 | 为优化性能，请将连接器服务器搬到应用程序服务器附近（请参阅[网络拓扑注意事项](../app-proxy/application-proxy-network-topology.md)）。 此外，连接器服务器和 Web 应用程序服务器还应属于同一个 Active Directory 域，或者跨信任域。 要使用集成 Windows 身份验证 (IWA) 和 Kerberos 约束委派 (KCD) 执行 SSO，此配置是必需的。 如果服务器在不同的域中，则需要使用基于资源的委派进行 SSO（请参阅[使用 KCD 执行应用程序代理的单一登录](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md)）。 |
| 为连接器启用自动更新 | 为连接器启用自动更新，以获取最新功能和 bug 修复。 Microsoft 为最新连接器版本和之前的一个版本提供直接支持。 （请参阅[应用程序代理发布版本历史记录](../app-proxy/application-proxy-release-version-history.md)。） |
| 绕过本地代理 | 为了便于维护，请将连接器配置为绕过本地代理，使其直接连接到 Azure 服务。 （请参阅[应用程序代理连接器和代理服务器](../app-proxy/application-proxy-configure-connectors-with-proxy-servers.md)。） |
