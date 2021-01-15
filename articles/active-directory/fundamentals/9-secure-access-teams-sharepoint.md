---
title: 使用 Azure Active Directory 安全地访问 Microsoft 团队、SharePoint 和 OneDrive
description: 安全访问 Microsoft 365 服务，作为整体外部访问安全性的一部分。
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
ms.openlocfilehash: 18036644dc4df51bfacc5019f70ae7694757f753
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222235"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>保护对 Microsoft 团队、SharePoint 和 OneDrive for business 的外部访问 

Microsoft 团队、SharePoint 和 OneDrive for Business 是与外部用户协作和共享内容的三种最常用方式。 如果 "已批准" 方法过于严格，用户将通过电子邮件发送内容或设置不安全的外部进程和应用程序（例如个人 DropBox 或 OneDrive），以批准的方法之外。 你的目标是通过易于协作来平衡安全需求。

本文指导你使用 Microsoft 团队和 SharePoint 确定和配置外部协作，以满足你的业务目标。

## <a name="governance-begins-in-azure-active-directory"></a>调控开始于 Azure Active Directory

Microsoft 365 中的共享部分由 [外部标识控制 |](https://aad.portal.azure.com/) Azure Active Directory (Azure AD) 中的外部协作设置。 如果在 Azure AD 中禁用或限制外部共享，则会覆盖 Microsoft 365 中配置的任何共享设置。 但这种情况的一个例外是，如果未启用 Azure AD B2B 集成，则可以将 SharePoint 和 OneDrive 配置为支持通过一次性密码 (OTP) 进行即席共享。

![外部协作设置的屏幕截图](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>来宾用户访问权限

有三种来宾用户访问选项，可控制来宾用户在受邀后可以看到的内容。 

若要阻止来宾用户查看其他来宾用户的详细信息，并能够枚举组成员身份，请选择 "来宾用户对目录对象的属性和成员身份具有有限的访问权限"。

### <a name="guest-invite-settings"></a>来宾邀请设置

这些设置确定谁可以邀请来宾以及如何邀请这些来宾。 仅当启用了与 B2B 的集成时，才会启用这些设置。

建议启用来宾邀请者角色中的管理员和用户可以邀请。 此设置允许设置受控协作过程，如以下示例中所示。

* 团队所有者提交要为其分配 Guest 邀请者角色的票证，并

   * 将负责所有来宾邀请。

   * 同意不直接将用户添加到基础 SharePoint

   * 负责执行定期访问评审，并根据需要撤销访问权限。

* 中心 IT 执行以下

   * 完成培训后，通过授予请求的角色来启用外部共享。

   * 将 Azure AD P2 许可证分配给 Microsoft 365 组所有者，以启用访问评审。
   * 创建 Microsoft 365 组访问评审。

   * 确认访问评审正在进行。

   * 删除直接添加到底层 SharePoint 的用户。

 设置 " **为来宾启用电子邮件一次性密码" (预览) 并通过用户流启用 "来宾自助服务签名** **"**。 此设置利用与 Azure AD 外部协作设置的集成。

### <a name="collaboration-restrictions"></a>协作限制

协作限制下有三个选择。 你的业务要求要求你选择。

* **允许将邀请发送到任何域** 意味着任何用户都可以进行协作。

* **拒绝对指定域的邀请** 意味着这些域之外的任何用户都可以进行协作。

* **仅允许向指定的域发送邀请** ，这意味着不能邀请这些指定域之外的任何用户。 

## <a name="govern-access-in-teams"></a>管理团队中的访问权限

[团队将外部用户 (组织外的任何人区分开来) 和来宾用户 (具有来宾帐户) ](https://docs.microsoft.com/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH)) 的用户。 你可以在组织范围内的设置下管理 [团队管理员门户](https://admin.teams.microsoft.com/company-wide-settings/external-communications) 中的协作设置。 

> [!NOTE]
> Azure Active Directory 中的外部标识协作设置控制有效权限。 您可以增加团队的限制，但不会减少在 Azure AD 中设置的内容。

* **外部访问设置**。 默认情况下，团队允许外部访问，这意味着组织可以与所有外部域通信。 如果要限制或只允许团队特定域，可以在此处执行此操作。

* **来宾访问权限**。 来宾访问控制来宾用户可以在团队中执行的操作。

若要详细了解如何管理团队中的外部访问权限，请参阅以下资源。

* [在 Microsoft 团队中管理外部访问](https://docs.microsoft.com/microsoftteams/manage-external-access)

* [Microsoft 365 标识模型和 Azure Active Directory](https://docs.microsoft.com/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide)

* [Microsoft 团队的标识模型和身份验证](https://docs.microsoft.com/MicrosoftTeams/identify-models-authentication)

* [Microsoft 团队的敏感度标签](https://docs.microsoft.com/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>在 SharePoint 和 OneDrive 中控制访问权限

SharePoint 管理员有许多可用于协作的设置。 从 SharePoint 管理中心管理组织范围内的设置。 可以为每个 SharePoint 站点调整设置。 建议将组织范围内的设置设置为所需的最低安全级别，并根据需要提高特定站点的安全性。 例如，对于高风险项目，你可能希望将用户限制到某些域，并禁止成员邀请来宾。

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>将 SharePoint 和一个驱动器与 Azure AD B2B 集成

作为管理外部协作的总体策略的一部分，我们建议 [使用 AZURE AD B2B 启用 SharePoint 和 OneDrive 集成的预览](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview) 。

Azure AD B2B 提供来宾用户的身份验证和管理。 使用 SharePoint 和 OneDrive 集成， [AZURE AD B2B 一次性密码](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode) 用于文件、文件夹、列表项、文档库和站点的外部共享。 此功能提供了现有 [安全外部共享收件人体验](https://docs.microsoft.com/sharepoint/what-s-new-in-sharing-in-targeted-release)的升级体验。

> [!NOTE]
> 如果为 Azure AD B2B 集成启用预览，SharePoint 和 OneDrive 共享将受到 Azure AD 组织关系设置的限制，例如， **成员可以邀请** 和 **来宾可以邀请**。

### <a name="sharing-policies"></a>共享策略

可以为 SharePoint 和 OneDrive 设置 *外部共享*。 OneDrive 限制不能比 SharePoint 设置更好。

 ![SharePoint 和 OneDrive 中的外部共享设置的屏幕截图](media/secure-external-access/9-sharepoint-settings.png)

与 Azure AD B2B 的 SharePoint 集成更改了控件如何与帐户交互。

* **任何人**。 不推荐

   * 无论采用何种集成状态，启用任何人的链接意味着使用此类型的链接时，不会应用任何 Azure 策略。 

   * 在受管辖协作的方案中，不要启用此功能。 
   > [!NOTE]
   > 你可能会发现需要对特定站点启用此设置的情况，在这种情况下，你可以在此处启用此设置，并对单个站点设置更大的限制。

* **新的和现有的来宾**。 如果已启用集成，则建议使用。

   * 启用 **AZURE AD B2B 集成** 后，新的和现有的来宾将具有可使用 Azure AD 策略进行管理的 Azure AD B2B 来宾帐户。

   * 如果 **未启用 AZURE AD b2b 集成**，则不会创建新来宾 Azure AD b2b 帐户，也不能通过 Azure AD 对其进行管理。 现有来宾是否有 Azure AD B2B 帐户取决于创建来宾的方式。

* **现有来宾**。 如果未启用集成，则建议使用。

   * 启用此功能后，用户只能与目录中已有的其他用户共享。

* **仅适用于组织中的人员**。 当你需要与外部用户协作时，不建议使用。

   * 不管集成状态如何，用户将只能与组织中的用户共享。 

* **按域限制外部共享**。 默认情况下，SharePoint 允许外部访问，这意味着允许共享所有外部域。 如果要限制或只允许特定的域，可以在此处执行此操作。

* **仅允许特定安全组中的用户在外部共享**。  此设置限制可以在 SharePoint 和 OneDrive 中共享内容的用户，而 Azure AD 中的设置适用于所有应用程序。 如果希望要求用户对安全共享进行培训，然后在完成时将其添加到已批准的共享安全组，则限制可以共享的人员可能会很有用。 如果选择此设置，并且用户无法获取获得 "已批准的共享者" 的访问权限，则他们可能会找到未批准的共享方法进行共享。 

* **允许来宾共享他们不拥有的项目**。 建议保留此功能。

* **使用验证码的人员必须在此天数 (默认值为 30) 之后进行身份验证**。 建议启用此设置。

### <a name="access-controls"></a>访问控制

"访问控制" 设置会影响组织中的所有用户。 假设您可能无法控制外部用户是否具有相容设备，我们不会在此处处理这些控制。 

* **空闲会话注销**。建议启用此控制，这允许在一段不活动时间后在非托管设备上发出警告和注销用户。 你可以配置非活动时间和警告。 

* **网络位置**。 设置此控制意味着，你可以只允许访问你的组织拥有的 IP 地址。 在 "外部协作" 方案中，仅在所有外部合作伙伴仅访问网络中的 "资源" 表单或通过 VPN 来设置此项。

### <a name="file-and-folder-links"></a>文件和文件夹链接

在 SharePoint 管理中心，还可以设置文件和文件夹链接的共享方式。 你还可以为每个站点配置这些设置。 

 ![文件和文件夹链接设置的屏幕截图](media/secure-external-access/9-file-folder-links.png)

如果已启用与 Azure AD B2B 的集成，则与组织外的用户共享文件和文件夹将导致在共享文件和文件夹时创建 B2B 用户。

建议仅将默认链接类型设置为 **组织中的人员**，并将默认权限设置为 " **编辑**"。 这样做可确保周全共享项。 然后，你可以针对满足特定协作需求的每个站点默认值自定义此设置。

### <a name="anyone-links"></a>任何人链接

不建议启用任何人的链接。 如果执行此操作，我们建议设置过期时间，并考虑将其限制为查看权限。 如果选择 "仅查看文件或文件夹的权限"，则用户将无法更改任何人的链接以包含编辑权限。

若要了解有关管理对 SharePoint 的外部访问的详细信息，请参阅以下内容：

* [SharePoint 外部共享概述](https://docs.microsoft.com/sharepoint/external-sharing-overview)

* [SharePoint 和 OneDrive 与 Azure AD B2B 集成](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. 安全访问你在此处 (的[Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md) 。 ) 