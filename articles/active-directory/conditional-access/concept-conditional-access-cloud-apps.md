---
title: 条件访问策略中的云应用、操作或身份验证上下文 - Azure Active Directory
description: 什么是 Azure AD 条件访问策略中的云应用、操作和身份验证上下文
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99da9afc9afb3c6eb19caf696c6b9802aed6a2dd
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953672"
---
# <a name="conditional-access-cloud-apps-actions-and-authentication--context"></a>条件访问：云应用、操作和身份验证上下文

云应用、操作和身份验证上下文是条件访问策略中的关键信号。 管理员可以使用条件访问策略将控制措施分配给特定应用程序、操作和身份验证上下文。

- 管理员可以从包括内置 Microsoft 应用程序以及任何 [Azure AD 集成应用程序](../manage-apps/what-is-application-management.md)（包括库、非库和通过[应用程序代理](../app-proxy/what-is-application-proxy.md)发布的应用程序）的应用程序列表进行选择。
- 管理员可以选择定义一个并非基于云应用程序，而是基于[用户操作](#user-actions)的策略（如“注册安全信息”或“注册或加入设备(预览)”），使条件访问能够围绕这些操作强制实施控制 。
- 管理员可以使用[身份验证上下文](#authentication-context-preview)在应用程序内提供额外的安全层。 

![定义条件访问策略并指定云应用](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 云应用程序

许多现有 Microsoft 云应用程序都包含在可供选择的应用程序的列表中。 

管理员可以向 Microsoft 提供的以下云应用分配条件访问策略。 某些应用（例如 Office 365 和 Microsoft Azure 管理）包含多个相关子应用或服务。 我们会不断地添加更多应用，因此以下列表并不完整，并且可能会发生更改。

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- Azure 事件中心
- Azure 服务总线
- [Azure SQL 数据库和 Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure 信息保护](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 管理](#microsoft-azure-management)
- Microsoft Azure 订阅管理
- Microsoft Cloud App Security
- Microsoft Commerce Tools 访问控制门户
- Microsoft Commerce Tools 身份验证服务
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 注册](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft 必应搜索
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 服务
- Project Online
- Skype for Business Online
- 虚拟专用网络 (VPN)
- Windows Defender ATP

适用于条件访问的应用程序已完成加入和验证过程。 此列表并不包括所有 Microsoft 应用，因为许多应用是后端服务，不会直接向其应用策略。 如果你正在寻找某个缺少的应用程序，可以联系特定的应用程序团队，或者在 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167259) 上发出请求。

### <a name="office-365"></a>Office 365

Microsoft 365 提供基于云的高效生产和协作服务，如 Exchange、SharePoint 和 Microsoft Teams。 Microsoft 365 云服务已深度集成，以确保用户拥有顺畅的协作体验。 在创建策略时，这种集成可能会造成混淆，因为某些应用（如 Microsoft Teams）依赖于 SharePoint 或 Exchange 等其他一些应用。

使用 Office 365 应用可以同时将这些服务作为目标。 建议使用新的 Office 365 应用，而不是以单个云应用作为目标，以避免[服务依赖项](service-dependencies.md)出现问题。 将这一组应用程序作为目标有助于避免因策略和依赖关系不一致而导致的问题。

如果需要，管理员可以选择从策略中排除特定应用，方法是在策略中包括 Office 365 应用并排除所选的特定应用。

Office 365 客户端应用中包含的关键应用程序：

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - 微软待办
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 搜索服务
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 管理

Microsoft Azure 管理应用程序包括多个基础服务。 

   - Azure 门户
   - Azure 资源管理器提供程序
   - 经典部署模型 API
   - Azure PowerShell
   - Azure CLI
   - Visual Studio 订阅管理员门户
   - Azure DevOps
   - Azure 数据工厂门户

> [!NOTE]
> Microsoft Azure 管理应用程序适用于 Azure PowerShell，后者调用 Azure 资源管理器 API。 它不适用于 Azure AD PowerShell，后者调用 Microsoft Graph。

### <a name="other-applications"></a>其他应用程序

除 Microsoft 应用以外，管理员还可以将任何已在 Azure AD 中注册的应用程序添加到条件访问策略。 这些应用程序包括： 

- 通过 [Azure AD 应用程序代理](../app-proxy/what-is-application-proxy.md)发布的应用程序
- [从库中添加的应用程序](../manage-apps/add-application-portal.md)
- [不在库中的自定义应用程序](../manage-apps/view-applications-portal.md)
- [通过应用交付控制器和网络发布的传统应用程序](../manage-apps/secure-hybrid-access.md)
- 使用[基于密码的单一登录](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)的应用程序

> [!NOTE]
> 由于条件访问策略设置了服务访问方面的要求，因此你无法将其应用于客户端（公共/本机）应用程序。 换句话说，该策略不是直接在客户端（公共/本机）应用程序上设置的，而是在客户端调用服务时应用的。 例如，在 SharePoint 服务上设置的策略将应用于调用 SharePoint 的客户端。 在 Exchange 上设置的策略将应用于使用 Outlook 客户端访问电子邮件的尝试。 正因如此，云应用选取器没有客户端（公共/本机）应用程序可供选择，并且在租户中注册的客户端（公共/本机）应用程序的应用程序设置中未提供条件访问选项。 

## <a name="user-actions"></a>用户操作

用户操作是可由用户执行的任务。 目前，条件访问支持两种用户操作： 

- **注册安全信息**：使用此用户操作，可以在启用了组合注册的用户尝试注册其安全信息时强制实施条件访问策略。 在[组合安全信息注册](../authentication/concept-registration-mfa-sspr-combined.md)一文中可以找到详细信息。

- **注册或加入设备（预览）** ：使用此用户操作，管理员可以在用户向 Azure AD [注册](../devices/concept-azure-ad-register.md)或 [加入](../devices/concept-azure-ad-join.md)设备时强制实施条件访问策略。 使用此操作可以精细针对注册或加入设备的操作配置多重身份验证方面，而无需配置当前存在的租户范围的策略。 对于此用户操作，需要注意三个重要事项： 
   - `Require multi-factor authentication` 是此用户操作唯一可用的访问控制，所有其他访问控制均处于禁用状态。 此限制可防止与依赖于 Azure AD 设备注册或不适用于 Azure AD 设备注册的访问控制发生冲突。 
   - `Client apps` 和 `Device state` 条件在此用户操作中不可用，因为它们依赖于使用 Azure AD 设备注册来强制实施条件访问策略。
   - 对此用户操作启用条件访问策略时，必须将“Azure Active Directory” > “设备” > “设备设置” - `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` 设置为“否”   。 否则，将无法正确地强制实施此用户操作的条件访问策略。 有关此设备设置的详细信息，请参阅[配置设备设置](../devices/device-management-azure-portal.md#configure-device-settings)。 

## <a name="authentication-context-preview"></a>身份验证上下文（预览版）

身份验证上下文可用于进一步保护应用程序中的数据和操作。 这些应用程序可以是你自己的自定义应用程序、自定义业务线 (LOB) 应用程序、SharePoint 等应用程序或受 Microsoft Cloud App Security (MCAS) 保护的应用程序。 

例如，组织可能会保留 SharePoint 站点中的文件，如午餐菜单或其机密 BBQ 酱料食谱。 所有人都可以访问午餐菜单站点，但是有权访问机密 BBQ 酱料食谱站点的用户可能需要从托管设备访问并同意特定使用条款。

### <a name="configure-authentication-contexts"></a>配置身份验证上下文

身份验证上下文在 Azure 门户中的“Azure Active Directory” > “安全性” > “条件访问” > “身份验证上下文”下进行管理   。

![在 Azure 门户中管理身份验证上下文](./media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-get-started.png)

> [!WARNING]
> * 在预览版期间，无法删除身份验证上下文定义。 
> * 预览版的限制是 Azure 门户中最多 25 个身份验证上下文定义。

在 Azure 门户中选择“新建身份验证上下文”来创建新的身份验证上下文定义。 配置以下属性：

- “显示名称”是用于标识 Azure AD 以及使用身份验证上下文的应用程序中的身份验证上下文的名称。 我们建议使用可以跨资源使用的名称（例如“受信任的设备”），以减少所需的身份验证上下文数。 减少设置可限制重定向数量，并提供更好的端到端用户体验。
- “说明”提供有关 Azure AD 管理员使用的策略以及将身份验证上下文应用于资源的策略的详细信息。
- 选中“发布到应用”复选框时，会将身份验证上下文播发到应用并使其可供分配。 如果未选中，身份验证上下文将不可用于下游资源。 
- “ID”是只读的，用于特定于请求的身份验证上下文定义的令牌和应用。 此处列出了故障排除和开发用例。 

然后，管理员可以在“分配” > “云应用或操作” > “身份验证上下文”下的条件访问策略中选择已发布的身份验证上下文  。

### <a name="tag-resources-with-authentication-contexts"></a>使用身份验证上下文来标记资源 

有关在应用程序中使用身份验证上下文的详细信息，请参阅以下文章。

- [SharePoint Online](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Microsoft Cloud App Security](/cloud-app-security/session-policy-aad?branch=pr-en-us-2082#require-step-up-authentication-authentication-context)
- 自定义应用程序

## <a name="next-steps"></a>后续步骤

- [条件访问：条件](concept-conditional-access-conditions.md)
- [条件访问常见策略](concept-conditional-access-policy-common.md)
- [客户端应用程序依赖关系](service-dependencies.md)