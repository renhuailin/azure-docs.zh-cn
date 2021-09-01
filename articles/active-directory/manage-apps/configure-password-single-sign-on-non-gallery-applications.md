---
title: 了解 Azure Active Directory 中应用的基于密码的单一登录 (SSO)
description: 了解 Azure Active Directory 中应用的基于密码的单一登录 (SSO)
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 40c2e4ae14e2b50a26ed3ff25846d15b129e2916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738810"
---
# <a name="understand-password-based-single-sign-on"></a>了解基于密码的单一登录

在应用程序管理的[快速入门系列](view-applications-portal.md)中，你已了解如何使用 Azure AD 作为应用程序的标识提供者 (IdP)。 在快速入门指南中，你将配置基于 SAML 或基于 OIDC 的 SSO。 另一种方法是基于密码的 SSO。 本文更详细地介绍了基于密码的 SSO 选项。

此选项可用于包含 HTML 登录页的任何网站。 基于密码的 SSO 也称为密码保管。 基于密码的 SSO，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。

基于密码的 SSO 是将应用程序快速集成到 Azure AD 中的绝佳方式，并且可以：

- 通过安全地存储和重播用户名和密码，启用用户单一登录

- 支持需要多个登录字段的应用程序，这适用于不只需要用户名和密码字段才能登录的应用程序

- 自定义用户在输入其凭据时在[我的应用](../user-help/my-apps-portal-end-user-access.md)看到的“用户名”和“密码”字段的标签

- 允许用户为手动输入的任何现有应用程序帐户提供自己的用户名和密码。

- 允许业务组的成员使用[自助应用程序访问](./manage-self-service-access.md)功能指定分配给用户的用户名和密码。

- 允许管理员指定个人或组在使用更新凭据功能登录到应用程序时使用的用户名和密码。

## <a name="before-you-begin"></a>在开始之前

使用 Azure AD 作为标识提供者 (IdP) 和配置单一登录 (SSO) 时，操作可能简单也可能复杂，具体取决于所使用的应用程序。 配置某些应用程序只需几个操作就能搞定。 而其他则需要深度配置。 要快速提升知识水平，请浏览有关应用程序管理的[快速入门系列](view-applications-portal.md)。 如果要添加的应用程序很简单，则可能不需要阅读本文。 如果要添加的应用程序需要自定义配置，并且你需要使用基于密码的 SSO，则本文适用于你。

> [!IMPORTANT]
> 在某些情况下，“单一登录”选项将不会出现在“企业应用程序”中的应用程序的导航中。
>
> 如果使用“应用注册”注册了应用程序，则默认情况下，单一登录功能将配置为使用 OIDC OAuth。 在这种情况下，“企业应用程序”下的导航中将不会显示“单一登录”选项 。 使用“应用注册”添加自定义应用时，将在清单文件中配置选项。 要详细了解清单文件，请参阅 [Azure Active Directory 应用部件清单 (manifest)](../develop/reference-app-manifest.md)。 要了解有关 SSO 标准的详细信息，请参阅[使用 Microsoft 标识平台进行身份验证和授权](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)。
>
> 导航中将缺少“单一登录”的另一种场景是：应用程序托管在另一个租户中，或者如果你的帐户没有所需权限（全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者）。 权限还可能导致出现你可打开“单一登录”，但无法保存它的情况。 详细了解 Azure AD 管理角色，请参阅（ https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

## <a name="basic-configuration"></a>基本配置

在[快速入门系列](view-applications-portal.md)中，你已了解如何将应用添加到你的租户，以便 Azure AD 知道该应用程序是否被用作应用程序的标识提供者 (IdP)。 某些应用已预先配置，它们显示在 Azure AD 库中。 其他应用不在库中，你必须创建一个通用应用并进行手动配置。 基于密码的 SSO 选项可能不可用，具体取决于应用程序。 如果在应用的“单一登录”页上看不到“基于密码的选项”列表，则表示选项不可用。

> [!IMPORTANT]
> 基于密码的 SSO 需要“我的应用”浏览器扩展。 若要了解详细信息，请参阅[计划我的应用部署](my-apps-deployment-plan.md)。

基于密码的 SSO 的配置页非常简单。 配置页仅包含应用程序使用的登录页的 URL。 此字符串必须是包含“用户名”输入字段的页面。

输入 URL 后，选择“保存”。 Azure AD 分析登录页的 HTML 以获得用户名和密码输入字段。 如果尝试成功，则已完成。
下一步是[将用户或组分配到应用程序](./assign-user-or-group-access-portal.md)。 分配用户和组后，你可以提供凭据，以便在用户登录到应用程序时使用。 选择“用户和组”，选中用户或组所在行的复选框，然后选择“更新凭据”。 最后，输入要用于该用户或组的用户名和密码。 否则，系统将在启动时提示用户输入凭据。

## <a name="manual-configuration"></a>手动配置

如果 Azure AD 的分析尝试失败，你可以手动配置登录。

1. 在“\<application name> 配置”下，选择“配置 \<application name> 密码单一登录设置”以显示“配置登录”页。

2. 选择“手动检测登录字段”。 将显示说明手动检测登录字段的其他说明。

   ![手动配置基于密码的单一登录](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 选择“捕获登录字段”。 此时将在新选项卡中打开“捕获状态”页，显示消息“元数据捕获当前正在进行”。

4. 如果新选项卡中显示了“所需的我的应用扩展”框，请选择“立即安装”以安装“我的应用安全登录扩展”浏览器扩展。 （浏览器扩展要求使用的浏览器是 Microsoft Edge、Chrome 或 Firefox。）接下来，安装、启动和启用该扩展，并刷新捕获状态页。

   然后，浏览器扩展将打开一个显示所输入 URL 的选项卡。
5. 在包含所输入 URL 的选项卡中，完成登录过程。 填写“用户名”和“密码”字段，然后尝试登录。 （不必提供正确的密码。）

   会出现一个提示，要求你保存捕获的登录字段。
6. 选择“确定”。 浏览器扩展更新“捕获状态”页，其中包含“应用程序已更新元数据”的消息。 浏览器选项卡关闭。

7. 在 Azure AD “配置登录”页上，选择“确定，我可以成功登录到应用”。

8. 选择“确定”  。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](./assign-user-or-group-access-portal.md)
- [配置用户帐户自动预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
