---
title: 设置自助服务组管理 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中创建和管理安全组或 Microsoft 365 组，并请求安全组或 Microsoft 365 组成员身份
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 07/27/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff97bd767bb9045c5fa018bbcbffc2cb7445ca1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778051"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>在 Azure Active Directory 中设置自助服务组管理 

可以在 Azure Active Directory (Azure AD) 中允许用户创建和管理他们自己的安全组或 Microsoft 365 组。 组的所有者可以批准或拒绝成员身份请求，并可以委托对组成员身份的控制。 自助服务组管理功能不可用于启用了邮件的安全组或通讯组列表。

## <a name="self-service-group-membership-defaults"></a>自助服务组成员身份默认值

如果安全组是在 Azure 门户中或使用 Azure AD PowerShell 创建的，则只有组的所有者可以更新成员身份。 通过自助服务在[访问面板](https://account.activedirectory.windowsazure.com/r#/joinGroups)中创建的安全组和所有 Microsoft 365 组都可供所有用户加入，无论是所有者批准的还是自动批准的。 在访问面板中，你可以在创建组时更改成员身份选项。

组创建于 | 安全组默认行为 | Microsoft 365 组默认行为
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | 只有所有者才能添加成员<br>在访问面板中可见但不可供加入 | 可供所有用户加入
[Azure 门户](https://portal.azure.com) | 只有所有者才能添加成员<br>在访问面板中可见但不可供加入<br>在创建组时不会自动分配所有者 | 可供所有用户加入
[访问面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 可供所有用户加入<br>可以在创建组时更改成员身份选项 | 可供所有用户加入<br>可以在创建组时更改成员身份选项

## <a name="self-service-group-management-scenarios"></a>自助服务组管理方案

* **委托组管理** — 以管理对公司所用 SaaS 应用程序的访问权限的管理员为例。 由于有许多加入者和离开者，管理这些访问权限变得越来越繁琐，因此该管理员要求业务所有者创建一个新组。 管理员将该应用程序的访问权限分配给新组，并向此组添加所有已访问该应用程序的人员。 然后，业务所有者可以添加更多用户，而这些用户会自动预配到该应用程序中。 业务所有者无需等待管理员管理用户的访问权限。 如果管理员将相同的权限授予不同业务组中的经理，则该人员也可以管理自己组成员的访问权限。 企业主和经理无法查看或管理对方的组成员身份。 该管理员仍然可以看到有权访问该应用程序的所有用户，并可根据需要阻止访问权限。
* **自助组管理** — 以下是该方案的一个示例：两个用户都拥有独立设置的 SharePoint Online 站点。 他们想为对方的团队提供对自己站点的访问权限。 要实现此目的，他们可以在 Azure AD 中创建一个组，各自在 SharePoint Online 中选择该组并为该组提供对其站点的访问权限。 当有人想要访问时，他们从访问面板发出请求，获得批准后便可自动访问这两个 SharePoint Online 站点。 后来，他们中的一人决定，允许访问其站点的所有人也访问特定的 SaaS 应用程序。 SaaS 应用程序的管理员可以将此应用程序的访问权限添加到 SharePoint Online 站点。 从那以后，他批准的任何请求都将提供对这两个 SharePoint Online 站点以及该 SaaS 应用程序的访问权限。

## <a name="make-a-group-available-for-user-self-service"></a>使组可用于用户自助服务

1. 使用被分配了目录的“全局管理员”角色或“特权角色管理员”角色的帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 依次选择“组”、“常规”设置。 

    ![Azure Active Directory 组常规设置。](./media/groups-self-service-management/groups-settings-general.png)

1. 将“所有者可以在访问面板中管理组成员资格请求”设置为“是”。 

1. 将“限制用户能够访问‘访问窗格’中的组功能”设为“否”。 

1. 将“用户可以在 Azure 门户、API 或 PowerShell 中创建安全组”设置为“是”或“否”  。

    有关此设置的详细信息，请参阅下一部分[组设置](#group-settings)。

1. 将“用户可以在 Azure 门户、API 或 PowerShell 中创建 Microsoft 365 组”设置为“是”或“否”  。

    有关此设置的详细信息，请参阅下一部分[组设置](#group-settings)。

你还可以使用“可以将成员分配为 Azure 门户中组所有者的所有者”，以实现对用户的自助服务组管理的更精细的访问控制。

当用户可以创建组时，组织中的所有用户均可以创建新组，然后作为默认所有者将成员添加到这些组。 不能指定可以创建自己的组的个人。 指定个人只能用于将其他组成员设为组所有者。

> [!NOTE]
> 必须具备 Azure Active Directory Premium（P1 或 P2）许可证，用户才能请求加入安全组或 Microsoft 365 组，所有者才能批准或拒绝成员身份请求。 如果没有 Azure Active Directory Premium 许可证，用户仍可在访问面板中管理他们的组，但不能在访问面板中创建需要所有者批准的组，也不能请求加入组。

## <a name="group-settings"></a>组设置

组设置可用于控制谁可以创建安全组和 Microsoft 365 组。

![Azure Active Directory 安全组设置更改。](./media/groups-self-service-management/security-groups-setting.png)

> [!NOTE]
> 这些设置的行为最近发生了更改。 请确保为组织配置这些设置。 有关详细信息，请参阅[为什么要更改组设置？](#why-were-the-group-settings-changed)。

 下表可帮助你决定要选择的值。

| 设置 | 值 | 对租户的影响 |
| --- | :---: | --- |
| 用户可以在 Azure 门户、API 或 PowerShell 中创建安全组 | 是 | Azure AD 组织中的所有用户均可在 Azure 门户、API 或 PowerShell 中创建新的安全组，并可将成员添加到这些组。 这些新组也会显示在其他所有用户的“访问面板”中。 如果组的策略设置允许，其他用户可以创建加入这些组的请求。 |
|  | 否 | 用户无法创建安全组，也无法更改其拥有的现有组。 不过，他们仍然可以管理这些组的成员身份，并审批其他用户加入其组的请求。 |
| 用户可以在 Azure 门户、API 或 PowerShell 中创建 Microsoft 365 组 | 是 | Azure AD 组织中的所有用户均可在 Azure 门户、API 或 PowerShell 中创建新的 Microsoft 365 组，并可将成员添加到这些组。 这些新组也会显示在其他所有用户的“访问面板”中。 如果组的策略设置允许，其他用户可以创建加入这些组的请求。 |
|  | 否 | 用户无法创建 Microsoft 365 组，也无法更改其拥有的现有组。 不过，他们仍然可以管理这些组的成员身份，并审批其他用户加入其组的请求。 |

下面是有关这些组设置的其他一些详细信息。

- 这些设置可能需要长达 15 分钟才能生效。
- 如果你想要允许某些（但并非所有）用户创建组，可为这些用户分配一个可以创建组的角色，例如[组管理员](../roles/permissions-reference.md#groups-administrator)。
- 这些设置适用于用户，但不影响服务主体。 例如，如果你的某个服务主体有权创建组，那么，即使将这些设置指定为“否”，该服务主体也仍可创建组。 

### <a name="why-were-the-group-settings-changed"></a>为什么要更改组设置？

之前的组设置实现名为“用户可以在 Azure 门户中创建安全组”和“用户可以在 Azure 门户中创建 Microsoft 365 组” 。 之前的设置仅控制 Azure 门户中的组创建，并不适用于 API 或 PowerShell。 新设置控在 Azure 门户以及 API 和 PowerShell 中的组创建。 新设置更安全。

新设置的默认值已设置为以前的 API 或 PowerShell 值。 新设置的默认值可能不同于以前仅控制 Azure 门户行为的值。 从 2021 年 5 月开始，有几个星期的过渡期，在新设置生效之前，你可以选择自己需要的默认值。 新设置生效后，需要验证是否为组织配置了新设置。

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问权限](../fundamentals/active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](../enterprise-users/groups-settings-cmdlets.md)
* [Azure Active Directory 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [什么是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)
