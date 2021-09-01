---
title: 条件访问策略中的授权控制 - Azure Active Directory
description: Azure AD 条件访问策略中的授权控制是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695786a43291bdb0cb679d79cfa4e6d08cf67770
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867217"
---
# <a name="conditional-access-grant"></a>条件访问：授予

在条件访问策略中，管理员可以利用访问控制来授予或阻止对资源的访问权限。

![包含授权控制且需要多重身份验证的条件访问策略](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>阻止访问

阻止操作会考虑到任何分配，根据条件访问策略配置阻止访问。

阻止是一种强有力的控制，运用此项控制时，应具备相应的知识。 带有块语句的策略可能会产生意外的副作用。 在大规模启用之前，正确的测试和验证至关重要。 进行更改时，管理员应使用[条件访问仅限报告模式](concept-conditional-access-report-only.md)和[条件访问中的 What If 工具](what-if-tool.md)等工具。

## <a name="grant-access"></a>授予访问权限

在授予访问权限时，管理员可以选择强制实施一项或多项控制。 这些控制包括以下选项： 

- [需要多重身份验证（Azure AD 多重身份验证）](../authentication/concept-mfa-howitworks.md)
- [要求将设备标记为合规 (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [要求使用已建立混合 Azure AD 联接的设备](../devices/concept-azure-ad-join-hybrid.md)
- [“需要已批准的客户端应用”](app-based-conditional-access.md)
- [需要应用保护策略](app-protection-based-conditional-access.md)
- [要求更改密码](#require-password-change)

当管理员组合使用这些选项时，可以选择以下方法：

- 需要所有选定控制（控制 **和** 控制）
- 需要某一选定控制（控制 **或** 控制）

默认情况下，条件访问需要所有选定控制。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

选中此复选框会要求用户执行 Azure AD 多重身份验证。 在[规划基于云的 Azure AD 多重身份验证部署](../authentication/howto-mfa-getstarted.md)一文中可以找到有关部署 Azure AD 多重身份验证的详细信息。

[Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-overview)满足条件访问策略中多重身份验证的要求。 

### <a name="require-device-to-be-marked-as-compliant"></a>要求将设备标记为合规

部署了 Microsoft Intune 的组织可以根据从其设备返回的信息来识别符合具体合规要求的设备。 此策略合规性信息将从 Intune 转发到 Azure AD，其中的条件访问可以决定是要授予还是阻止对资源的访问。 有关合规性策略的详细信息，请参阅[使用 Intune 在设备上设置规则以允许访问组织中的资源](/intune/protect/device-compliance-get-started)一文。

可以通过 Intune（适用于任何设备 OS）或通过适用于 Windows 10 设备的第三方 MDM 系统将设备标记为合规。 可在[支持 Intune 中的第三方设备合规性合作伙伴](/mem/intune/protect/device-compliance-partners)一文中找到受支持的第三方 MDM 系统列表。

设备必须在 Azure AD 中进行注册，然后才能标记为合规。 如需详细了解设备注册，请参阅[什么是设备标识](../devices/overview.md)一文。

### <a name="require-hybrid-azure-ad-joined-device"></a>要求使用已建立混合 Azure AD 联接的设备

组织可以选择使用设备标识作为其条件访问策略的一部分。 组织可以使用此复选框要求设备是已加入混合 Azure AD 的设备。 如需详细了解设备标识，请参阅[什么是设备标识？](../devices/overview.md)一文。

使用[设备代码 OAuth 流](../develop/v2-oauth2-device-code.md)时，不支持要求受管理设备授权控制或设备状态条件。 这是因为执行身份验证的设备无法向提供代码的设备提供其设备状态，并且令牌中的设备状态会锁定到执行身份验证的设备。 请改用“需要多重身份验证授权控制”。

### <a name="require-approved-client-app"></a>需要批准的客户端应用

组织可以要求只能尝试从已批准的客户端应用访问选定的云应用。 这些已批准的客户端应用支持 [Intune 应用保护策略](/intune/app-protection-policy)，而不受任何移动设备管理 (MDM) 解决方案影响。

为了应用此授权控制，条件访问要求在 Azure Active Directory 中注册设备，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是 Microsoft Authenticator 或适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时设备上未安装代理应用，则会将用户重定向到相应的应用商店来安装所需的代理应用。

已确认以下客户端应用支持此设置：

- Microsoft Azure 信息保护
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft 列表
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power Apps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- 微软待办
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Microsoft 365 管理员

**备注**

- 批准的客户端应用支持 Intune 移动应用管理功能。
- “需要批准的客户端应用”要求：
   - 仅支持 iOS 和 Android 作为设备平台条件。
   - 注册设备需要代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是 Microsoft Authenticator 或适用于 Android 设备的 Microsoft 公司门户。
- 条件访问无法将 InPrivate 模式下的 Microsoft Edge 视为已批准的客户端应用。
- 条件访问策略要求将 Microsoft Power BI 应用作为获批的客户端应用，它不支持使用 Azure AD 应用程序代理将 Power BI 移动应用连接到本地 Power BI 报表服务器。

请参阅文章[如何：使用条件访问要求使用批准的设备应用访问云应用](app-based-conditional-access.md)，以获取配置示例。

### <a name="require-app-protection-policy"></a>需要应用保护策略

在条件访问策略中，你可以要求客户端应用上先存在 [Intune 应用保护策略](/intune/app-protection-policy)，然后才能访问所选云应用。 

为了应用此授权控制，条件访问要求在 Azure Active Directory 中注册设备，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时设备上未安装代理应用，则会将用户重定向到应用商店来安装代理应用。

应用程序需要具有已实现策略保障的 Intune SDK，并且需要满足其他一些要求才能支持此设置 。 通过 Intune SDK 实施应用程序的开发人员可在 SDK 文档中找到这些要求的详细信息。

已确认以下客户端应用支持此设置：

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft 列表 (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Teams
- Microsoft Word
- MultiLine for Intune
- Nine Mail - 电子邮件和日历

> [!NOTE]
> Microsoft Kaizala、Microsoft Skype for Business 和 Microsoft Visio 不支持“需要应用保护策略”授权。 如果需要这些应用正常工作，请仅使用“需要已批准的应用”授权。 对于这三款应用程序，在两个授权之间使用 or 子句将不起作用。

**备注**

- 适用于应用保护策略的应用支持带策略保护的 Intune 移动应用程序管理功能。
- “需要应用保护策略”要求：
    - 仅支持 iOS 和 Android 作为设备平台条件。
    - 注册设备需要代理应用。 在 iOS 上，代理应用是 Microsoft Authenticator；在 Android 上，代理应用是 Intune 公司门户应用。

如需配置示例，请查看[如何：需要应用保护策略和已批准的客户端应用才能通过条件访问来访问云应用](app-protection-based-conditional-access.md)一文。

### <a name="require-password-change"></a>要求更改密码 

如果检测到用户风险，则使用用户风险策略条件，管理员可以选择让用户通过 Azure AD 自助式密码重置来安全地更改密码。 如果检测到用户风险，用户可以执行自助式密码重置进行自我修正，此过程将关闭用户风险事件，以避免为管理员带来不必要的干扰。 

当系统提示用户更改其密码时，他们首先需要完成多重身份验证。 你需要确保所有用户都已注册多重身份验证，为其帐户检测到风险做准备。  

> [!WARNING]
> 用户必须预先注册了自助式密码重置，然后才能触发用户风险策略。 

使用密码变更控制配置策略时的限制。  

1. 必须将策略分配到“所有云应用”。 此要求可防止攻击者通过登录不同的应用使用不同的应用来更改用户的密码并重置帐户的风险。 
1. 要求密码更改不能与其他控件一起使用，例如，要求设备符合规范。  
1. 密码更改控件只能与用户和组分配条件、云应用分配条件（必须设置为“全部”）和用户风险条件一起使用。 

### <a name="terms-of-use"></a>使用条款

如果你的组织已创建使用条款，则授权控制下可能会显示其他选项。 管理员可以通过这些选项要求用户确认使用条款，作为访问受策略保护的资源的条件。 若要详细了解使用条款，可查看 [Azure Active Directory 使用条款](terms-of-use.md)一文。

## <a name="next-steps"></a>后续步骤

- [条件访问：会话控制](concept-conditional-access-session.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)

- [“仅报表”模式](concept-conditional-access-report-only.md)
