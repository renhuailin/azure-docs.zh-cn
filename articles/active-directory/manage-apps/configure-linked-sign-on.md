---
title: 了解 Azure Active Directory 中的链接登录
description: 了解 Azure Active Directory 中的链接登录。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: dd5421eaf8dbb251c5df5bc4e1fd92fb8ed25243
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738818"
---
# <a name="understand-linked-sign-on"></a>了解链接登录

在应用程序管理的[快速入门系列](view-applications-portal.md)中，你已了解如何使用 Azure AD 作为标识提供程序 (IdP) 应用程序。 在快速入门指南中，你将配置基于 SAML 或基于 OIDC 的 SSO。 另一个选项是“链接”。 本文详细介绍了链接选项。

利用“链接”选项，你可以配置当用户在组织的 [我的应用](https://myapps.microsoft.com/) 或“Office 365 门户”中选择应用时目标位置。

在下面这些常见场景中，“链接”选项很有价值：

- 添加一个链接，这个连接需指向当前使用联合的自定义 Web 应用程序，如 Active Directory 联合身份验证服务 (AD FS)。
- 添加特定 SharePoint 页面或想要在用户访问面板上显示的其他网页的深层链接。
- 添加一个指向不需要身份验证的应用程序的链接。
 “链接”选项不会通过 Azure AD 凭据提供登录功能。 但你仍可以使用 **企业应用程序** 的某些其他功能。 例如，可以使用审核日志并可添加自定义徽标和应用名称。

## <a name="before-you-begin"></a>在开始之前

要快速提升知识水平，请浏览有关应用程序管理的[快速入门系列](view-applications-portal.md)。 在快速入门中，配置单一登录的情况下，你还会找到“链接”选项。

“链接”选项不会通过 Azure AD 凭据提供登录功能。 此选项只是设置当用户在 [我的应用](https://myapps.microsoft.com/) 或 Microsoft 365 应用启动器中选择应用时将用户发送到的位置。  由于登入不会通过 Azure AD 提供登录功能，因此，使用链接的单一登录配置的应用程序不能使用条件访问。

> [!IMPORTANT]
> 在某些情况下，“单一登录”选项将不会出现在“企业应用程序”中的应用程序的导航中。 
>
> 如果使用“应用注册”注册了应用程序，则默认情况下，单一登录功能将设置为使用 OIDC OAuth。 在这种情况下，“企业应用程序”下的导航中将不会显示“单一登录”选项。 使用“应用注册”添加自定义应用时，将在清单文件中配置选项。 要详细了解清单文件，请参阅 [Azure Active Directory 应用部件清单 (manifest)](../develop/reference-app-manifest.md)。 要了解有关 SSO 标准的详细信息，请参阅[使用 Microsoft 标识平台进行身份验证和授权](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)。
>
> 导航中将缺少“单一登录”的另一种场景是：应用程序托管在另一个租户中，或者如果你的帐户没有所需权限（全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者）。 权限还可能导致出现你可打开“单一登录”，但无法保存它的情况。 要详细了解 Azure AD 管理角色，请参阅 [Azure AD 内置角色](../roles/permissions-reference.md)。

### <a name="configure-link"></a>配置链接

要设置应用的链接，请在“单一登录”页上选择“链接”。 然后输入链接，并选择“保存”。 想知道在何处可以找到这些选项？ 请查看[快速入门系列](view-applications-portal.md)。

配置某个应用后，将用户和组分配给应用。 分配用户时，可以控制应用程序在 [我的应用](https://myapps.microsoft.com/)或“Microsoft 365”应用启动器上出现的时间。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](./assign-user-or-group-access-portal.md)
- [配置用户帐户自动预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)