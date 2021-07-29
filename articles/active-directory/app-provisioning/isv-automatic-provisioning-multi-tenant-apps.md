---
title: 在 Azure Active Directory 中为多租户应用程序启用自动用户预配
description: 面向在 Azure Active Directory 中启用自动预配的独立软件供应商的指南
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: zhchia, arvinh
ms.openlocfilehash: 03f2f06e29f3641d44be9c4f5bd6e8c58671653f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109782840"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application-in-azure-active-directory"></a>在 Azure Active Directory 中为你的多租户应用程序启用自动用户预配

自动用户预配是在目标系统（如软件即服务应用程序）中自动创建、维护和删除用户标识的过程。

## <a name="why-enable-automatic-user-provisioning"></a>为什么要启用自动预配？

在用户首次登录前就要求应用程序中存在用户记录，因此应用程序需要进行用户预配。 这不仅对服务提供商有好处，客户也能因此受益。

### <a name="benefits-to-you-as-the-service-provider"></a>作为服务提供商获得的权益

* 通过使用 Microsoft 标识平台增强应用程序的安全性。

* 减少采用你的应用程序所需的实际和感知的客户工作量。

* 通过基于跨域身份管理系统 (SCIM) 的预配，为自动用户预配降低与多个标识提供者 (IdP) 集成的成本。

* 通过提供丰富的日志来帮助客户解决用户预配问题，降低支持成本。

* 提高应用程序在 [Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps)中的可见性。

* 获取应用教程页面中的采用优先级的列表。

### <a name="benefits-to-your-customers"></a>作为客户获得的权益

* 通过为更改角色或将组织留给应用程序的用户自动删除对应用程序的访问权限，提高安全性。

* 通过避免人为错误和与手动预配相关的重复工作，简化应用程序的用户管理。

* 降低托管和维护自定义开发的预配解决方案的成本。

## <a name="choose-a-provisioning-method"></a>选择预配方法

Azure AD 提供了多个集成路径，以便为应用程序启用自动用户预配。

* [Azure AD 预配服务](../app-provisioning/user-provisioning.md)管理从 Azure AD 到应用程序（出站设置）以及从应用程序到 Azure AD（入站设置）的用户预配和取消预配。 服务将连接到应用程序提供的跨域身份管理系统 (SCIM) 用户管理 API 终结点。

* 使用 [Microsoft Graph](/graph/) 时，应用程序通过查询 Microsoft Graph API 来管理从 Azure AD 到应用程序的用户和组的入站和出站预配。

* 如果你的应用程序使用 SAML 进行联合身份验证，则可以启用安全断言标记语言即时 (SAML JIT) 用户预配。 它使用在 SAML 令牌中发送的声明信息来预配用户。

若要帮助确定要用于应用程序的集成选项，请参阅高级对比表，然后查看每个选项的详细信息。

| 自动预配启用或增强的功能| Azure AD 预配服务 (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Azure AD 中的用户和组管理| √| √| 仅限用户 |
| 管理从本地 Active Directory 同步的用户和组| √*| √*| 仅限用户* |
| 在预配对 Microsoft 365 数据（Teams、SharePoint、电子邮件、日历和文档等）的访问期间，访问用户和组以外的数据。| X+| √| X |
| 基于业务规则创建、读取和更新用户| √| √| √ |
| 基于业务规则删除用户| √| √| X |
| 从 Azure 门户管理所有应用程序的自动用户预配| √| X| √ |
| 支持多个标识提供者| √| X| √ |
| 支持来宾帐户 (B2B)| √| √| √ |
| 支持非企业帐户 (B2C)| X| √| √ |

<sup>*</sup> – 将用户从 AD 同步到 Azure AD 需要 Azure AD Connect 安装程序。  
<sup>+</sup >– 使用 SCIM 进行预配不会阻止你将应用程序与用于其他目的的 Microsoft Graph 集成。

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 预配服务 (SCIM)

Azure AD 预配服务使用 [SCIM](https://aka.ms/SCIMOverview)，这是许多标识提供者 (IdP) 和应用程序（例如，Slack、G Suite、Dropbox）支持的预配行业标准。 如果除了 Azure AD 之外还需要支持 IdP，则建议使用 Azure AD 预配服务，因为任何符合 SCIM 要求的 IdP 都可以连接到 SCIM 终结点。 生成简单的/用户终结点，你可以启用预配，而不必维护自己的同步引擎。 

有关 Azure AD 预配服务如何使用 SCIM 的详细信息，请参阅： 

* [了解有关 SCIM 标准的详细信息](https://aka.ms/SCIMOverview)

* [使用跨域标识管理系统 (SCIM) 将用户和组从 Azure Active Directory 自动预配到应用程序](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [了解 Azure AD SCIM 实现](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>使用 Microsoft Graph 预配

使用 Microsoft Graph 进行预配时，你有权访问 Graph 中提供的所有富格式用户数据。 除了用户和组的详细信息外，还可以获取其他信息，如用户的角色、经理和直接下属、拥有和注册的设备，以及 [Microsoft Graph](/graph/api/overview) 中提供的数百个其他数据片段。 

超过 1500 万家组织和 90% 的财富 500 强公司使用 Azure AD，同时订阅 Microsoft 365、Microsoft Azure 或企业移动性套件等 Microsoft 云服务。 你可以使用 Microsoft Graph 将你的应用与管理工作流，如员工入职（和离职）、配置文件维护等。 

详细了解如何使用 Microsoft Graph 预配：

* [Microsoft Graph 主页](https://developer.microsoft.com/graph)

* [Microsoft Graph 概述](/graph/overview)

* [Microsoft Graph 身份验证概述](/graph/auth/)

* [Microsoft Graph 入门](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>使用 SAML JIT 预配

如果只想在首次登录应用程序时预配用户，而不需要自动取消对用户的预配，则 SAML JIT 不失为一个选择。 应用程序必须支持 SAML 2.0 作为联合身份验证协议，才能使用 SAML JIT。

SAML JIT 使用 SAML 令牌中的声明信息来创建和更新应用程序中的用户信息。 客户可根据需要在 Azure AD 应用程序中配置这些必需的声明。 有时需要从应用程序端启用 JIT 预配，以便客户可以使用此功能。 SAML JIT 对创建和更新用户很有用，但它不能删除或停用应用程序中的用户。

## <a name="next-steps"></a>后续步骤

* [为应用程序启用单一登录](../develop/v2-howto-app-gallery-listing.md)

* [提交应用程序列表](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)并与 Microsoft 合作，以便在 Microsoft 网站上创建文档。

* [加入 Microsoft 合作伙伴网络（免费），并创建市场推广计划](https://partner.microsoft.com/explore/commercial)。
