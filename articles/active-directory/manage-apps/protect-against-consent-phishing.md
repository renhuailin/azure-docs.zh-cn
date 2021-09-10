---
title: 防范同意钓鱼 | Azure AD 应用管理
description: 了解如何使用 Azure AD 缓解基于应用的同意钓鱼攻击。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: tilarso
ms.openlocfilehash: 7f9f5a49a46c4f73dce65f23e8ace278f02fe941
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113194"
---
# <a name="protecting-against-consent-phishing"></a>防范同意钓鱼

工作效率不再局限于专用网络，并且工作已急剧转向云服务。 虽然使用云应用程序可以使员工通过远程方式保证工作效率，但攻击者还可以使用基于应用程序的攻击来获取重要的组织数据。 你可能熟悉以用户为中心的攻击，如电子邮件钓鱼或凭据泄露。 同意钓鱼是另一个需要注意的威胁媒介。
本文介绍什么是同意钓鱼，Microsoft 采取哪些措施来保护你的安全，以及组织可以执行哪些步骤来保持安全。

## <a name="what-is-consent-phishing"></a>什么是同意钓鱼？

同意钓鱼攻击会欺骗用户向恶意的云应用授予权限。 随后，这些恶意应用可以访问用户的合法云服务和数据。 与凭据泄露不同，执行同意钓鱼的威胁参与者将以能够直接授权访问其个人或组织数据的用户为目标。 同意屏幕会显示应用接收的所有权限。 所需权限由于应用程序是由合法提供程序（如 Microsoft 的标识平台）托管的，因此，不知情的用户会接受条款或点击“接受”，这会向恶意应用程序授予对用户或组织数据的所需权限。

:::image type="content" source="./media/protect-consent-phishing/permissions-requested.png" alt-text="显示“请求的权限”窗口要求用户同意的屏幕截图。":::

*请求访问各种权限的 OAuth 应用的示例。*

## <a name="mitigating-consent-phishing-attacks-using-azure-ad"></a>使用 Azure AD 缓解同意钓鱼攻击

管理员、用户或 Microsoft 安全研究人员可能会标记看起来行为可疑的 OAuth 应用程序。 已标记的应用程序将由 Microsoft 审核，确定应用是否违反服务条款。 如果确认违反，Azure AD 将禁用应用程序，并阻止在所有 Microsoft 服务中进一步使用。

当 Azure AD 禁用 OAuth 应用程序时，会发生以下情况：
- 恶意应用程序和相关的服务主体被置于完全禁用状态。 任何新令牌请求或刷新令牌的请求将被拒绝，但现有访问令牌在过期之前仍有效。
- 我们通过 Microsoft Graph 中相关[应用程序](/graph/api/resources/application?view=graph-rest-1.0&preserve-view=true)和[服务主体](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true)资源类型上名为“disabledByMicrosoftStatus”的公开属性，显示了禁用状态。
- 如果全局管理员所在的组织中可能有一个用户在被 Microsoft 禁用之前同意了一个应用程序，他们应收到一封电子邮件，其中包含可用来调查并改善其安全状况的所需操作和建议步骤。

## <a name="recommended-response-and-remediation"></a>建议的应对和补救措施

如果你的组织已受到 Microsoft 禁用的应用程序的影响，我们建议立即执行以下步骤来确保你的环境安全：

1. 调查已禁用应用程序的应用程序活动，包括：
    - 应用程序请求的委托的权限或应用程序权限。
    - 应用程序的活动以及有权使用应用程序的用户的登录活动的 Azure AD 审核日志。
1. 查看并实施 Microsoft 云产品中[有关抵御非法同意授权的指导](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants?view=o365-worldwide&preserve-view=true)，其中包括对已禁用的应用程序或审核期间发现的任何其他可疑应用审核权限和同意。
1. 实施下面所述的有关强化防范同意钓鱼的最佳做法。


## <a name="best-practices-for-hardening-against-consent-phishing-attacks"></a>强化防范同意钓鱼攻击的最佳做法

Microsoft 希望通过提供适当的见解和功能使管理员处于控制地位，控制如何在组织中许可和使用应用程序。 尽管攻击者一直存在，但组织可以执行一些步骤来改善安全状况。 要遵循的一些最佳做法包括：

* 培训组织成员，使他们了解权限和同意框架的工作原理
    - 了解应用程序请求的数据和权限，以及 [权限和同意](../develop/v2-permissions-and-consent.md)在平台内部如何工作。
    - 确保管理员知道如何 [管理和评估同意请求](./manage-consent-requests.md)。
    - 定期在组织内[审核应用和已同意的权限](/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) ，确保所使用的应用程序仅可访问所需数据，并遵循最小特权原则。
* 了解如何发现和阻止常见的同意钓鱼手段
    - 检查拼写和语法是否有误。 如果电子邮件消息或应用程序的同意屏幕出现拼写和语法错误，这可能是可疑应用程序。 在这种情况下，可以使用“在此处报告”链接直接在[同意提示](../develop/application-consent-experience.md#building-blocks-of-the-consent-prompt)上报告它，Microsoft 将进行调查，如果确认是恶意应用程序，会将其禁用。
    - 不要依赖应用名称和域 URL 作为真实性的来源。 攻击者喜欢使用欺骗性的应用名称和域，使它们看起来像是来自合法的服务或公司，促使你同意恶意应用。 你应该验证域 URL 的来源，并尽可能使用来自[经过验证的发布者](../develop/publisher-verification-overview.md)的应用程序。
    - 通过[使用 Microsoft Defender for Office 365 阻止同意钓鱼电子邮件](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies?view=o365-worldwide&preserve-view=true#impersonation-settings-in-anti-phishing-policies-in-microsoft-defender-for-office-365)，防范攻击者冒充组织中的已知用户的钓鱼活动。
    - 配置 Microsoft Cloud App Security 策略（如 [活动策略](/cloud-app-security/user-activity-policies)、 [异常情况检测](/cloud-app-security/anomaly-detection-policy)和  [OAuth 应用策略](/cloud-app-security/app-permission-policy) ），帮助管理组织中的异常应用程序活动并对其执行操作。
    - 通过遵循有关 [Microsoft 365 Defender 高级搜寻](/microsoft-365/security/defender/advanced-hunting-overview?view=o365-worldwide&preserve-view=true)的指导来调查并搜寻同意钓鱼攻击。
* 允许访问你信任的应用，并防范你不信任的应用
    - 使用已由发布者验证的应用程序。 [发布者验证](../develop/publisher-verification-overview.md)可以帮助管理员和最终用户通过 Microsoft 支持的审核流程了解应用程序开发人员的真实身份。
    - [配置用户同意设置](./configure-user-consent.md?tabs=azure-portal)，让用户仅同意你信任的特定应用程序，例如你的组织开发的应用程序，或来自经验证发布者的应用程序。
    - 创建主动[应用管理](/microsoft-365/compliance/app-governance-manage-app-governance?view=o365-worldwide&preserve-view=true)策略，监视 Microsoft 365 平台上的第三方应用行为，以防范常见的可疑应用行为。

## <a name="next-steps"></a>后续步骤

* [应用同意授权调查](/security/compass/incident-response-playbook-app-consent)
* [管理对应用的访问](./what-is-access-management.md)
* [限制 Azure AD 中的用户同意操作](/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations)
