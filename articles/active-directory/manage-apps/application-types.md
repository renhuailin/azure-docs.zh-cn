---
title: 查看使用租户进行标识管理的应用
titleSuffix: Azure AD
description: 了解如何查看使用 Azure Active Directory 租户进行标识管理的所有应用程序。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: a2030df0bd70781f299ee7db60df0e3ae49b8568
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620709"
---
# <a name="viewing-apps-using-your-azure-active-directory-tenant-for-identity-management"></a>查看使用 Azure Active Directory 租户进行标识管理的应用

[应用程序管理快速入门系列](view-applications-portal.md)介绍了基础知识。 其中介绍了如何查看使用 Azure AD 租户进行标识管理的所有应用。 本文将更深入地介绍你会找到的应用类型。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>为什么在所有应用程序列表中出现某个特定应用程序？

筛选为“所有应用程序”时，“所有应用程序列表” 会显示租户中的每个服务主体对象。 服务主体对象以多种方式出现在此列表中：

- 添加应用程序库的任何应用程序时，包括：

  - “Azure AD 企业应用程序” – 使用 Azure AD 门户上的“企业应用程序”选项添加到租户的应用。  通常使用 SAML 标准集成的应用。
  - “Azure AD - 应用注册” – 使用 Azure AD 门户上的“应用注册”选项添加到租户的应用。  通常，使用 Open ID Connect 和 OAuth 标准自定义开发的应用。
  - **应用程序代理应用程序** - 要从外部向其提供单一登录且在本地环境中运行的应用程序
- 注册或登录与 Azure Active Directory 集成的第三方应用程序时。 [Smartsheet](https://app.smartsheet.com/b/home) 或 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx) 就是一个示例。
- Microsoft 应用，如 Microsoft 365。
- 使用 Azure 资源托管标识时。 有关详细信息，请参阅[托管标识类型](../managed-identities-azure-resources/overview.md#managed-identity-types)。
- 当通过使用[应用程序注册表](../develop/quickstart-register-app.md)创建以自定义方式开发的应用程序，来添加新应用程序注册之时
- 当通过使用 [V2.0 应用程序注册门户](../develop/quickstart-register-app.md)创建以自定义方式开发的应用程序，来添加新应用程序注册之时
- 添加使用 Visual Studio 的 [ASP.NET 身份验证方法](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)或[连接的服务](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)开发的应用程序时
- 使用 [Azure AD PowerShell 模块](/powershell/azure/active-directory/install-adv2)创建服务主体对象时
- 以管理员身份[同意某应用程序](../develop/howto-convert-app-to-be-multi-tenant.md)使用租户中的数据时
- [用户同意某应用程序](../develop/howto-convert-app-to-be-multi-tenant.md)使用租户中的数据时
- 启用某些在租户中存储数据的服务时。 相应的一个示例是密码重置，密码重置是作为服务主体进行建模的，以便安全存储密码重置策略。

若要详细了解如何以及为何将应用添加到目录，请参阅[如何将应用程序添加到 Azure AD](../develop/active-directory-how-applications-are-added.md)。

## <a name="next-steps"></a>后续步骤

[使用 Azure Active Directory 管理应用程序](what-is-application-management.md)
