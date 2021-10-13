---
title: Azure Active Directory 与应用集成入门
description: 本文是一篇入门指南，介绍如何将 Azure Active Directory (AD) 与本地应用程序和云应用程序集成。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: a19a0cebe3323e16b8f5cc7744c9e4db79393073
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615764"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>将 Azure Active Directory 与应用程序集成入门指南

本主题概述了将应用程序与 Azure Active Directory (AD) 进行集成的流程。 以下各部分包含对一个更详细主题的简要汇总，方便用户确定此入门指南的哪些部分属于相关内容。

若要下载详细的部署计划，请参阅[后续步骤](#next-steps)。

## <a name="take-inventory"></a>获取清单

在将应用程序与 Azure AD 进行集成之前，必须知道你所在的位置，以及要前往的位置。  以下问题旨在帮助你思考 Azure AD 应用程序集成项目。

### <a name="application-inventory"></a>应用程序盘点

* 所有应用程序的所在位置？ 其所有者是谁？
* 应用程序需要哪种身份验证？
* 谁需要访问哪些应用程序？
* 是否要部署新应用程序？
  * 是否要在公司内部构建应用程序并将其部署在 Azure 计算实例上？
  * 是否要使用 Azure 应用程序库中提供的某个应用程序？

### <a name="user-and-group-inventory"></a>用户和组盘点

* 用户帐户位于哪个位置？
  * 本地 Active Directory
  * Azure AD
  * 在拥有的独立应用程序数据库中
  * 在未经认可的应用程序中
  * 以上都是
* 各个用户当前有哪些权限和角色分配？ 是否需要审查其访问权限，或者是否确定用户当前的访问和角色分配适当？
* 是否已在本地 Active Directory 中建立组？
  * 组的组织方式如何？
  * 有哪些组成员？
  * 组当前有哪些权限/角色分配？
* 是否需要在集成之前清理用户/组数据库？  （垃圾进来、垃圾出去。 相当重要的问题。）

### <a name="access-management-inventory"></a>访问管理盘点

* 当前如何管理用户对应用程序的访问？ 是否需要做出更改？  是否考虑过使用其他方式来管理访问，例如使用 [Azure RBAC](../../role-based-access-control/role-assignments-portal.md)？
* 谁需要访问哪些应用程序？

可能最初没有所有这些问题的答案，但没有关系。  本指南可帮助你回答其中一些问题并做出一些明智的决策。

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>使用 Cloud Discovery 查找未批准的云应用程序

如上所述，可能有些应用程序到目前为止仍不受组织的管理。  在盘点过程中，可以查找未经认可的云应用程序。 请参阅[设置 Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)。

## <a name="integrating-applications-with-azure-ad"></a>将应用程序与 Azure AD 集成

以下文章介绍了将应用程序与 Azure AD 集成的不同方法，并提供了一些指导。

* [确定要使用的 Active Directory](../fundamentals/active-directory-whatis.md)
* [使用 Azure 应用程序库中的应用程序](what-is-single-sign-on.md)
* [集成 SaaS 应用程序教程列表](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Azure AD 库中未列出的应用功能

可以添加已存在于组织中的任何应用程序或来自供应商的任何第三方应用程序（尚不在 Azure AD 库中）。 根据[许可协议](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)，可以使用以下功能：

* 通过自助方式集成支持[安全断言标记语言 (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) 标识提供者的任何应用程序（SP 发起或 IdP 发起）
* 通过自助方式集成包含 HTML 登录页并使用[基于密码的 SSO](sso-options.md#password-based-sso) 的任何 Web 应用程序
* 以自助方式连接使用[跨域身份管理系统 (SCIM) 协议进行用户预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)的应用程序
* 可在 [Office 365 应用启动器](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)或[我的应用](https://myapplications.microsoft.com/)中添加任何应用程序的链接

如果要查找有关如何将自定义应用与 Azure AD 集成的开发人员指南，请参阅 [Azure AD 的身份验证方案](../develop/authentication-vs-authorization.md)。 开发使用新式协议（如 [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)）对用户进行身份验证的应用时，可以使用 Azure 门户中的[应用注册](../develop/quickstart-register-app.md)体验将应用注册到 Microsoft 标识平台。

### <a name="authentication-types"></a>身份验证类型

每个应用程序可能有不同的身份验证要求。 借助 Azure AD，可对使用 SAML 2.0、WS 联合身份验证或 OpenID Connect 协议和密码单一登录的应用程序使用签名证书。 有关应用程序身份验证类型的详细信息，请参阅[在 Azure Active Directory 中管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)和[基于密码的单一登录](what-is-single-sign-on.md)。

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>使用 Azure AD 应用代理启用 SSO

使用 Microsoft Azure AD 应用程序代理，可以从任何位置和任何设备安全访问专用网络中的应用程序。 在环境中安装应用程序代理连接器后，可以使用 Azure AD 轻松配置该连接器。

### <a name="integrating-custom-applications"></a>集成自定义应用程序

如果要将自定义应用程序添加到 Azure 应用程序库，请参阅[将应用发布到 Azure AD 应用库](../develop/v2-howto-app-gallery-listing.md)。

## <a name="managing-access-to-applications"></a>管理对应用程序的访问

以下文章介绍了在使用 Azure AD 连接器和 Azure AD 将应用程序与 Azure AD 集成之后，如何管理对应用程序的访问。

* [使用 Azure AD 管理对应用的访问](what-is-access-management.md)
* [使用 Azure AD 连接器自动化](../app-provisioning/user-provisioning.md)
* [将用户分配到应用程序](./assign-user-or-group-access-portal.md)
* [将组分配到应用程序](./assign-user-or-group-access-portal.md)
* [共享帐户](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>后续步骤

若要了解详细信息，可以从 [GitHub](../fundamentals/active-directory-deployment-plans.md) 下载 Azure Active Directory 部署计划。 对于库应用程序，你可以通过 [Azure 门户](https://portal.azure.com)下载适用于单一登录、条件性访问和用户预配的部署计划。

若要从 Azure 门户下载部署计划，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“企业应用程序” | “选取应用” | “部署计划”。  
