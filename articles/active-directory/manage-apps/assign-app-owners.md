---
title: 分配企业应用程序所有者 - Azure AD | Microsoft Docs
description: 在 Azure Active Directory 中将所有者分配给应用程序
services: active-directory
documentationcenter: ''
author: davidmu1
manager: celesteDG
ms.service: active-directory
ms.workload: identity
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 08/03/2021
ms.author: davidmu
ms.openlocfilehash: 18649f5c597be73e9f14abf00a2df584c56df5d4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446608"
---
# <a name="assign-enterprise-application-owners"></a>分配企业应用程序所有者

分配所有者是授予全方面管理特定应用程序注册或企业应用程序的 Azure AD 配置权限的一种简单方法。 身为所有者的用户可以管理企业应用程序的组织特定配置，例如单一登录配置、预配和用户分配。 所有者还可以添加或删除其他所有者。 与全局管理员不同，所有者只能管理他们拥有的企业应用程序。

只有用户才能是企业应用程序的所有者。 不能将组分配为所有者。 所有者可将凭据添加到应用程序，并使用这些凭据来模拟应用程序的标识。 应用程序拥有的权限可能多于所有者，因此，与身为用户或服务主体的所有者的访问权限相比，应用程序的特权将会提升。 

在模拟应用程序时，根据应用程序的权限，应用程序所有者可能会创建或更新用户或其他对象。 应用程序的所有者权限与限定于单个应用程序的应用程序管理员权限相同。 有关详细信息，请参阅 [Azure AD 内置角色](../roles/permissions-reference.md#application-administrator)。 

## <a name="assign-an-owner"></a>分配所有者

向企业应用程序分配所有者：

1. 使用符合组织中“应用程序管理员”角色或“云应用程序管理员”角色条件的帐户登录 [Azure AD 组织](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 选择“企业应用程序”，然后选择要在其中添加所有者的应用程序。
3. 选择“所有者”，然后选择“添加”，以获取可在其中选择所有者的用户帐户列表。
4. 搜索并选择要成为应用程序所有者的用户帐户。
5. 单击“选择”以添加选为应用程序所有者的用户帐户。

> [!NOTE]
> 如果用户设置“限制访问 Azure AD 管理门户”设置为“`Yes`”，则非管理员用户无法使用 Azure 门户管理其拥有的应用程序。 有关可在拥有的企业应用程序上执行的操作详细信息，请参阅[拥有的企业应用程序](../fundamentals/users-default-permissions.md#owned-enterprise-applications)。 

## <a name="next-steps"></a>后续步骤

- [在 Azure Active Directory 中委托应用注册权限](../roles/delegate-app-roles.md)
