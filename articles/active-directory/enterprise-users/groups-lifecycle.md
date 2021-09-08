---
title: 为 Microsoft 365 组设置过期 - Azure Active Directory | Microsoft Docs
description: 如何在 Azure Active Directory 中为 Microsoft 365 组设置过期
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a68955cda6fce5fca89603aa6d498efca8ffa94d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427329"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>为 Microsoft 365 组配置过期策略

本文介绍如何通过为 Microsoft 365 组设置过期策略来管理这些组的生命周期。 仅可以为 Azure Active Directory (Azure AD) 中的 Microsoft 365 组设置过期策略。

将某个组设置为过期后：

- 具有用户活动的组在快要过期时自动续订。
- 如果组未自动续订，则会通知组的所有者续订组。
- 未续订的任何组将被删除。
- 组所有者或管理员可在 30 日内还原任何被删除的 Microsoft 365 组。

目前，只能为 Azure AD 组织中的所有 Microsoft 365 组配置一个过期策略。

> [!NOTE]
> 配置和使用 Microsoft 365 组的过期策略需要你拥有为应用过期策略的所有组的成员使用的 Azure AD Premium 许可证（但不一定要为其分配这些许可证）。

有关如何下载和安装 Azure AD PowerShell cmdlet 的信息，请参阅 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="activity-based-automatic-renewal"></a>基于活动的自动续订

借助 Azure AD 智能，系统可根据最近是否使用过相关组来自动续订这些组。 使用此功能，组所有者无需再进行手动操作，因为该功能基于组中的用户在 Outlook、SharePoint 或 Teams 等 Microsoft 365 服务中的活动情况。 例如，如果某个所有者或组成员执行了在 SharePoint 中上传文档、访问某个 Teams 频道或将电子邮件发送到 Outlook 中的组这样的操作，系统会在该组过期约 35 天前自动续订该组，并且其所有者不会收到任何续订通知。

### <a name="activities-that-automatically-renew-group-expiration"></a>会自动续订组过期的活动

以下用户操作会导致组自动续订：

- SharePoint：查看、编辑、下载、移动、共享或上传文件
- Outlook：加入组、通过组空间读取/写入组消息、点赞某条消息（如 Outlook Web 访问中的消息）
- Teams：访问 Teams 频道

### <a name="auditing-and-reporting"></a>审核和报告

管理员可以从 Azure AD 中的活动审核日志获取自动续订的组的列表。

![基于活动自动续订组](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>角色和权限

以下角色可在 Azure AD 中为 Microsoft 365 组配置和使用过期。

角色 | 权限
-------- | --------
全局管理员、组管理员或用户管理员 | 可以创建、读取、更新或删除 Microsoft 365 组过期策略设置<br>可以续订任何 Microsoft 365 组
User | 可以续订他们拥有的 Microsoft 365 组<br>可以恢复他们拥有的 Microsoft 365 组<br>可以读取过期策略设置

有关还原已删除组的权限的详细信息，请参阅[在 Azure Active Directory 中还原已删除的 Microsoft 365 组](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>设置组过期

1. 使用 Azure AD 组织中的全局管理员帐户打开 [Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择“组”，然后选择“过期”以打开过期设置 。
  
   ![组的过期设置](./media/groups-lifecycle/expiration-settings.png)

3. 在“过期”页中，可以：

    - 设置组的生存期（天）。 可以从预设值中任选其一，或自定义一个值（应为 30 天或以上）。
    - 指定当组没有所有者时续订和过期通知应发送到的电子邮件地址。
    - 选择会过期的 Microsoft 365 组。 可以为以下项设置过期时间：
      - **所有** Microsoft 365 组
      - **所选** 的一系列 Microsoft 365 组
      - **无** 过期时间，任何组都不过期
    - 设置完成后，选择“保存”来保存设置。

> [!NOTE]
> - 首次设置过期时，会将任何早于过期时间间隔的组设置为 35 天后过期，除非自动续订了这些组或所有者续订了这些组。
> - 当删除并还原动态组时，会将视其为一个新组并根据规则重新填充。 此过程最多可能需要 24 小时。
> - Teams 中使用的组的过期通知会显示在“Teams 所有者”源中。
> - 为选定的组启用到期时间后，最多可以将 500 个组添加到列表中。 如果需要添加的组超过 500 个，则可以为所有组启用到期时间。 在这种情况下，500 个组的限制不适用。

## <a name="email-notifications"></a>电子邮件通知

如果组未自动续订，则分别会在 Microsoft 365 组过期前 30 天、15 天和 1 天向该组所有者发送类似于此通知的电子邮件通知。 电子邮件的语言由组所有者的首选语言或 Azure AD 语言设置确定。 如果组所有者定义了首选语言，或多个所有者具有相同首选语言，则使用该语言。 在所有其他情况下均使用 Azure AD 语言设置。

![过期电子邮件通知](./media/groups-lifecycle/expiration-notification.png)

组所有者可以从“续订组”通知电子邮件直接访问“[访问面板](https://account.activedirectory.windowsazure.com/r#/applications)”中的组详细信息页。 在此处，用户可以获取更多关于组的信息，例如该组的描述、上次续订时间、到期时间，并且还能续订组。 组详细信息页面现在还包括 Microsoft 365 组资源的链接，因此组所有者可以在组中轻松查看内容和活动。

当某个组过期时，它将在到期日期的后一天被删除。 将向 Microsoft 365 组所有者发送如下电子邮件通知，通知其关于 Microsoft 365 组过期及后续删除的信息。

![组删除电子邮件通知](./media/groups-lifecycle/deletion-notification.png)

可以通过选择“还原组”或使用 PowerShell cmdlet 在删除组后的 30 天内还原组，如[在 Azure Active Directory 中还原已删除的 Microsoft 365 组](groups-restore-deleted.md)中所述。 请注意，30 天的组还原期不可自定义。

如果所还原的组包含文档、SharePoint 站点或其他持久对象，可能需要最多 24 小时才能完全还原该组及其内容。

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>如何检索 Microsoft 365 组过期日期

除了访问面板（用户可在其中查看包括到期日期和上次续订日期在内的组详细信息），还可以从 Microsoft Graph REST API Beta 版中检索 Microsoft 365 组的到期日期。 Microsoft Graph Beta 中启用了作为组属性的 expirationDateTime。 可以使用 GET 请求来检索它。 如需更多详细信息，请参阅[此示例](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true)。

> [!NOTE]
> 若要在“访问面板”上管理组成员身份，需要在 Azure Active Directory 组常规设置中将“限制访问‘访问面板’中的组”设置为“否”。

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>如何结合使用 Microsoft 365 组过期与合法保留邮箱

组到期并删除后，应用（例如 Planner、站点或 Teams）中删除的组数据将会在 30 天后永久删除，但合法保留的组邮箱将会保留下来并且不会永久删除。 管理员可以使用 Exchange cmdlets 来恢复邮箱以提取数据。

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>如何结合使用 Microsoft 365 组过期与保留策略

保留策略是通过安全和合规中心配置的。 如果为 Microsoft 365 组设置了保留策略，组到期并被删除后，组邮箱中的组对话和组站点中的文件将保留在保留容器中，保留的特定天数取决于保留策略中的定义。 到期后，用户无法查看组或其中的内容，但可以通过电子发现恢复站点和邮箱数据。

## <a name="powershell-examples"></a>PowerShell 示例

以下是如何使用 PowerShell cmdlets 来为 Azure AD 组织中的 Microsoft 365 组配置过期设置的示例：

1. 安装 PowerShell v2.0 模块并在 PowerShell 提示符处登录：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 配置过期设置“使用 New-AzureADMSGroupLifecyclePolicy cmdlet”，将 Azure AD 组织中所有 Microsoft 365 组的生命周期设置为 365 天。 没有所有者的 Microsoft 365 组的续订通知将发送到“emailaddress@contoso.com”
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 检索现有策略 Get-AzureADMSGroupLifecyclePolicy：此 cmdlet 检索当前已配置的 Microsoft 365 组过期设置。 在此示例中，可以看到：

   - 策略 ID
   - Azure AD 组织中所有 Microsoft 365 组的生命周期设置为 365 天
   - 没有所有者的 Microsoft 365 组的续订通知将发送到“emailaddress@contoso.com”。
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. 更新现有策略 Set-AzureADMSGroupLifecyclePolicy：此 cmdlet 用于更新现有策略。 在以下示例中，现有策略中的组生存期从 365 天更改为 180 天。
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. 将特定组添加到策略 Add-AzureADMSLifecyclePolicyGroup：此 cmdlet 将组添加到生命周期策略。 示例：
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. 删除现有策略 Remove-AzureADMSGroupLifecyclePolicy：此 cmdlet 会删除 Microsoft 365 组过期设置，但需要策略 ID。 此 cmdlet 将禁用 Microsoft 365 组的过期。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
以下 cmdlet 可用于更详细地配置策略。 有关详细信息，请参阅 [PowerShell 文档](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)。

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>后续步骤

以下文章提供有关 Azure AD 组的更多信息。

- [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理组的成员](../fundamentals/active-directory-groups-members-azure-portal.md)
- [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-dynamic-membership.md)
