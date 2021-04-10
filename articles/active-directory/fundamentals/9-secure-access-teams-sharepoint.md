---
title: 使用 Azure Active Directory 安全地从外部访问 Microsoft Teams、SharePoint 和 OneDrive
description: 安全地访问 Microsoft 365 服务是外部访问安全性整体措施的一部分。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565132"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>安全地从外部访问 Microsoft Teams、SharePoint 和 OneDrive for Business 

Microsoft Teams、SharePoint 和 OneDrive for Business 是与外部用户协作和共享内容时最常使用的方法中的三种。 如果“已批准的”方法的限制性太强，用户会通过使用电子邮件发送内容或设置不安全的外部流程和应用程序（例如个人 DropBox 或 OneDrive），来绕开已批准的方法。 你的目标是将安全性需求与易于协作进行平衡。

本文会指导你使用 Microsoft Teams 和 SharePoint 确定并配置外部协作，以满足你的业务目标。

## <a name="governance-begins-in-azure-active-directory"></a>治理从 Azure Active Directory 中开始

Microsoft 365 中的共享有一部分是通过 Azure Active Directory (Azure AD) 中的[外部标识 | 外部协作设置](https://aad.portal.azure.com/)治理的。 如果外部共享在 Azure AD 中被禁用或限制，则它会替代 Microsoft 365 中配置的任何共享设置。 这种情况的一个例外是，如果未启用 Azure AD B2B 集成，则可以将 SharePoint 和 OneDrive 配置为支持通过一次性密码 (OTP) 进行临时共享。

![外部协作设置的屏幕截图](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>来宾用户访问权限

有三个用于来宾用户访问的选项，它们控制来宾用户在受邀后可以看到的内容。 

若要阻止来宾用户查看其他来宾用户的详细信息，并阻止其枚举组成员身份，请选择“来宾用户对目录对象的属性和成员身份的访问权限有限”。

### <a name="guest-invite-settings"></a>来宾邀请设置

这些设置决定了谁可以邀请来宾以及如何邀请这些来宾。 仅当启用了与 B2B 的集成时，才会启用这些设置。

建议启用“管理员和具有‘来宾邀请者’角色的用户可以邀请”。 此设置可用于设置受控协作过程，如以下示例所示。

* 团队所有者提交要求为其分配“来宾邀请者”角色的票证，并

   * 将负责所有来宾邀请。

   * 同意不直接将用户添加到基础 SharePoint

   * 负责执行定期访问评审，并根据需要撤销访问权限。

* 中央 IT 执行以下操作

   * 完成培训后，通过授予所请求的角色来启用外部共享。

   * 将 Azure AD P2 许可证分配给 Microsoft 365 组所有者，以启用访问评审。
   * 创建 Microsoft 365 组访问评审。

   * 确认访问评审正在进行。

   * 删除直接添加到基础 SharePoint 的用户。

 将“为来宾启用电子邮件一次性密码(预览)”和“启用通过用户流进行来宾自助注册(预览)”设置为“是”。 此设置利用与 Azure AD 外部协作设置的集成。

### <a name="collaboration-restrictions"></a>协作限制

协作限制下有三个选择。 你的业务需求决定了你将选择哪个。

* “允许将邀请发送到任何域”意味着可以邀请任何用户进行协作。

* “拒绝向指定域发送邀请”意味着可以邀请这些域外部的任何用户进行协作。

* “仅允许向指定域发送邀请”表示不能邀请这些指定域外部的任何用户。 

## <a name="govern-access-in-teams"></a>在 Teams 中治理访问权限

[Teams 区分外部用户（组织外部的任何人）和来宾用户（具有来宾帐户的用户）](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)。 可以在 [Teams 管理门户](https://admin.teams.microsoft.com/company-wide-settings/external-communications)中的组织级设置下管理协作设置。 

> [!NOTE]
> Azure Active Directory 中的外部标识协作设置控制生效的权限。 你可以在 Teams 中增加限制，但不要从 Azure AD 中设置的限制中减少限制。

* 外部访问设置。 默认情况下，Teams 允许外部访问，这意味着组织可以与所有外部域进行通信。 如果你希望只针对 Teams 来限制或允许特定域，可以在此处执行该操作。

* 来宾访问权限。 来宾访问权限控制来宾用户可以在 Teams 中执行的操作。

若要详细了解如何在 Teams 中管理外部访问，请参阅以下资源。

* [在 Microsoft Teams 中管理外部访问](/microsoftteams/manage-external-access)

* [Microsoft 365 标识模型和 Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Microsoft Teams 的标识模型和身份验证](/MicrosoftTeams/identify-models-authentication)

* [Microsoft Teams 的敏感度标签](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>在 SharePoint 和 OneDrive 中治理访问权限

SharePoint 管理员有许多可用于协作的设置。 组织级设置从 SharePoint 管理中心管理。 可以为每个 SharePoint 站点调整设置。 建议将组织级设置设定为所需的最低安全级别，并根据需要提高特定站点的安全性。 例如，对于高风险项目，你可能希望将用户限定为某些域，并禁用成员邀请来宾的权利。

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>将 SharePoint 和 OneDrive 与 Azure AD B2B 集成

建议你[启用 SharePoint 和 OneDrive 与 Azure AD B2B 的集成的预览版](/sharepoint/sharepoint-azureb2b-integration-preview)，这是用于治理外部协作的总体策略的一部分。

Azure AD B2B 提供了对来宾用户的身份验证和管理。 通过与 SharePoint 和 OneDrive 集成，[Azure AD B2B 一次性密码](../external-identities/one-time-passcode.md)可用于文件、文件夹、列表项、文档库和站点的外部共享。 此功能提供了从现有的[安全外部共享收件人体验](/sharepoint/what-s-new-in-sharing-in-targeted-release)升级的体验。

> [!NOTE]
> 如果你启用 Azure AD B2B 集成的预览版，则 SharePoint 和 OneDrive 共享需遵守 Azure AD 组织关系设置，例如“成员可以邀请”和“来宾可以邀请”。

### <a name="sharing-policies"></a>共享策略

可以为 SharePoint 和 OneDrive 设置外部共享。 OneDrive 限制不能比 SharePoint 设置更宽松。

 ![SharePoint 和 OneDrive 中的外部共享设置的屏幕截图](media/secure-external-access/9-sharepoint-settings.png)

SharePoint 与 Azure AD B2B 的集成更改了控件与帐户进行交互的方式。

* 任何人。 不推荐

   * 无论集成状态如何，启用“任何人”链接都意味着使用此类型的链接时不会应用任何 Azure 策略。 

   * 在进行受治理协作的情况下，不要启用此功能。 
   > [!NOTE]
   > 你可能会发现有时候需要为特定站点启用此设置，在这种情况下，需在此处启用它，并在各个站点上设置更严格的限制。

* 新来宾和现有来宾。 如果启用了集成，建议使用此选项。

   * 启用了 Azure AD B2B 集成时，新来宾和现有来宾将具有可通过 Azure AD 策略管理的 Azure AD B2B 来宾帐户。

   * 未启用 Azure AD B2B 集成时，新来宾不会有创建的 Azure AD B2B 帐户，你无法从 Azure AD 管理新来宾。 现有来宾是否具有 Azure AD B2B 帐户取决于来宾是如何创建的。

* 现有来宾。 如果未启用集成，建议使用此选项。

   * 启用此选项后，用户只能与已在你的目录中的其他用户进行共享。

* 仅限你的组织中的人员。 当需要与外部用户进行协作时，不建议使用此选项。

   * 无论集成状态如何，用户都只能与你的组织中的用户进行共享。 

* 按域限制外部共享。 默认情况下，SharePoint 允许外部访问，这意味着允许与所有外部域共享。 如果你希望只针对 SharePoint 来限制或允许特定域，可以在此处执行该操作。

* 仅允许特定安全组中的用户进行外部共享。  此设置限制谁可以共享 SharePoint 和 OneDrive 中的内容，而 Azure AD 中的设置则应用于所有应用程序。 如果你希望要求你的用户接受有关安全共享的培训，然后在完成时将用户添加到已批准的共享安全组，则限制谁可以共享可能比较有用。 如果你选择了此设置，但用户无法获得成为“已批准的共享者”所需的访问权限，则用户可能会改为寻求未经批准的共享方式。 

* 允许来宾共享不归其拥有的项。 建议让此选项保持禁用状态。

* 使用验证码的用户在此天数(默认为 30)后必须重新进行身份验证。 建议启用此设置。

### <a name="access-controls"></a>访问控制

访问控制设置会影响你的组织中的所有用户。 考虑到你可能无法对外部用户是否拥有兼容的设备进行控制，此处我们不讨论这些控制。 

* 闲置会话注销。建议启用此控制，它允许你对非管理的设备上某段时间不活动的用户发出警告并将其注销。 你可以配置不活动时段和警告。 

* **网络位置**。 设置此控制意味着你只允许从你的组织拥有的 IP 地址进行的访问。 在外部协作方案中，仅当你的所有外部合作伙伴都只从你的网络内访问资源或通过你的 VPN 访问资源时，才设置此选项。

### <a name="file-and-folder-links"></a>文件和文件夹链接

在 SharePoint 管理中心内，你还可以设置如何共享文件和文件夹链接。 你还可以为每个站点配置这些设置。 

 ![文件和文件夹链接设置的屏幕截图](media/secure-external-access/9-file-folder-links.png)

如果你启用了与 Azure AD B2B 的集成，则与组织外部的用户共享文件和文件夹会导致在共享文件和文件夹时创建 B2B 用户。

建议将默认链接类型设置为“仅限你的组织中的人员”，并将默认权限设置为“编辑”。 这样做可确保审慎地共享相关项。 然后，你可以针对每个站点的满足特定协作需求的默认值自定义此设置。

### <a name="anyone-links"></a>“任何人”链接

不建议启用“任何人”链接。 如果启用，建议你设置一个过期时间，并考虑仅向用户授予查看权限。 如果你选择了针对文件或文件夹的“仅查看”权限，则用户将无法通过更改“任何人”链接来包括编辑权限。

若要详细了解如何治理对 SharePoint 的外部访问，请参阅以下内容：

* [SharePoint 外部共享概述](/sharepoint/external-sharing-overview)

* [SharePoint 和 OneDrive 与 Azure AD B2B 的集成](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)（本文）