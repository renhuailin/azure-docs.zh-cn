---
title: 注册应用程序以使用 Azure Active Directory | Microsoft Docs
description: 本文专门为 IT 专业人员编写，提供有关将 Azure 应用程序与 Active Directory 集成的指导。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: de16c947c59f5a0111b9325dbefe7daf1268fb40
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649154"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>为 Azure Active Directory 开发业务线应用
本指南简要介绍了如何开发用于 Azure Active Directory (AD) 的业务线 (LoB) 应用程序，目标受众为 Active Directory/Microsoft 365 全局管理员。

## <a name="overview"></a>概述
通过构建与 Azure AD 集成的应用程序，可让组织的用户使用 Microsoft 365 进行单一登录。 在 Azure AD 中拥有应用程序可让用户控制应用程序的身份验证策略。 若要详细了解条件访问以及如何使用多重身份验证 (MFA) 保护应用，请参阅[配置访问规则](../authentication/tutorial-enable-azure-mfa.md)。

注册应用程序以使用 Azure Active Directory。 注册应用程序意味着开发人员可以使用 Azure AD 对用户进行身份验证并请求访问用户资源（如电子邮件、日历和文档）。

目录的任何成员（不是来宾）都可以注册应用程序，也称为 *创建应用程序对象*。 如果无法注册应用程序，则表示目录的全局管理员限制了此功能，因此你可能需要与他们联系以 [获取正确的权限](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) 才能注册应用程序。 若要详细了解如何限制用户的详细信息，请参阅 [Azure Active Directory 中的委派应用注册权限](../roles/delegate-app-roles.md#restrict-who-can-create-applications)。

注册应用程序后，可让任何用户执行以下操作：

* 获取 Azure AD 识别的应用程序标识
* 获取应用程序可用于向 AD 验证其身份的一个或多个机密/密钥
* 在 Azure 门户中使用自定义名称、徽标等指定应用程序的品牌。
* 对应用应用 Azure AD 授权功能，包括：

  * 基于角色的访问控制 (RBAC)
  * 使用 Azure Active Directory 作为 oAuth 授权服务器（保护应用程序公开的 API）
* 声明让应用程序按预期运行所需的权限，包括：

     - 应用权限（仅限全局管理员）。 例如：另一个 Azure AD 应用程序中的角色成员身份，或相对于 Azure 资源、资源组或订阅的角色成员身份
     - 委派的权限（任何用户）。 例如：Azure AD、登录和读取配置文件

> [!NOTE]
> 默认情况下，任何成员都可以注册应用程序。 要了解如何限定只有特定成员拥有注册应用程序的权限，请参阅 [How applications are added to Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)（如何将应用程序添加到 Azure AD）。
>
>

下面是全局管理员需要执行哪些操作，才能帮助开发人员将其应用程序投放到生产环境：

* 配置访问规则（访问策略/MFA）
* 将应用程序配置为要求用户分配，并分配用户
* 抑制默认的用户同意体验

## <a name="configure-access-rules"></a>配置访问规则
在 SaaS 应用中配置基于应用程序的访问规则。 例如，这可能要求 MFA，或只允许受信任网络上的用户访问。 [配置访问规则](../authentication/tutorial-enable-azure-mfa.md)文档中提供了相关详细信息。

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>将应用程序配置为要求用户分配，并分配用户
默认情况下，用户无需分配即可访问应用程序。 不过，如果应用程序公开角色或者希望应用程序显示在用户的“我的应用”上，则应该要求用户分配。

如果是 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 的订阅者，我们强烈建议使用组。 将组分配到应用程序可让你将持续进行的访问管理委派给组所有者。 可以创建组，或使用组管理功能请求组织中负责人创建组。

[将用户和组分配到应用程序](./assign-user-or-group-access-portal.md)  


## <a name="suppress-user-consent"></a>抑制用户同意
默认情况下，每个用户通过同意体验进行登录。 对于不太熟悉做出此类决定的用户而言，同意体验（请求用户授予对应用程序的权限）可能会令其不安。

对于信任的应用程序，可以代表组织来同意应用程序，以简化用户体验。

有关 Azure 中的用户同意和同意体验的详细信息，请参阅 [Integrating Applications with Azure Active Directory](../develop/quickstart-register-app.md)（将应用程序与 Azure Active Directory 集成）。

## <a name="related-articles"></a>相关文章
* [使用 Azure AD 应用程序代理启用对本地应用程序的安全远程访问](application-proxy.md)
* [使用 Azure AD 管理对应用的访问](what-is-access-management.md)