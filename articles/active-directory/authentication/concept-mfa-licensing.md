---
title: Azure AD 多重身份验证版本和消耗计划
description: 了解 Azure AD 多重身份验证客户端以及可用的不同方法和版本。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b2a3528e30546acf0e8f652de7f8e46226fd188
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451478"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Azure AD 多重身份验证的功能和许可证

若要保护组织中的用户帐户，应使用多重身份验证。 对资源拥有访问特权的帐户尤其需要此功能。 基本多重身份验证功能适用于 Microsoft 365 和 Azure Active Directory (Azure AD) 全局管理员，不收取额外的费用。 如果想要升级管理员的功能，或者将多重身份验证扩展到其他用户，可通过多种方式购买 Azure AD 多重身份验证。

> [!IMPORTANT]
> 本文详细说明 Azure AD 多重身份验证的不同许可和使用方式。 有关定价和计费的具体详细信息，请参阅 [Azure AD 定价页](https://www.microsoft.com/en-us/security/business/identity-access-management/azure-ad-pricing)。

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>可用的 Azure AD 多重身份验证版本

可以根据组织的需求，以几种不同的方式使用和许可 Azure AD 多重身份验证。 根据你当前拥有的 Azure AD、EMS 或 Microsoft 365 许可证，你可能已有权使用 Azure AD 多重身份验证。 例如，Azure AD 外部标识中的前 50,000 名月度活跃用户可以免费使用 MFA 和其他高级版 P1 或 P2 功能。 有关详细信息，请参阅 [Azure Active Directory 外部标识定价](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)。

下表详细说明了获取 Azure AD 多重身份验证和某些功能的不同方式，以及每种方式的用例。

| 如果你是以下产品的用户 | 功能和用例 |
| --- | --- |
| [Microsoft 365 商业高级版](https://www.microsoft.com/microsoft-365/business)和 [EMS](https://www.microsoft.com/security/business/enterprise-mobility-security) 或 [Microsoft 365 E3 和 E5](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans) | EMS E3、Microsoft 365 E3 和 Microsoft 365 商业高级版包括 Azure AD 高级版 P1。 EMS E5 或 Microsoft 365 E5 包括 Azure AD Premium P2。 可使用以下部分所述的相同条件访问功能向用户提供多重身份验证。 |
| [Azure AD Premium P1](../fundamentals/active-directory-get-started-premium.md) | 在特定的情况下或者发生适合业务要求的事件时，使用 [Azure AD 条件访问](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)提示用户执行多重身份验证。 |
| [Azure AD Premium P2](../fundamentals/active-directory-get-started-premium.md) | 提供了最强的安全保障和改进的用户体验。 在 Azure AD Premium P1 功能的基础上增加了[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)，以适应用户模式并尽量减少出现多重身份验证提示。 |
| [所有 Microsoft 365 计划](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans) | 可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)为所有用户启用 Azure AD 多重身份验证。 Azure AD 多重身份验证的管理通过 Microsoft 365 门户进行。 若要改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件访问。 有关详细信息，请参阅[使用多重身份验证保护 Microsoft 365 资源](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication)。 也可以[按用户启用](howto-mfa-userstates.md) MFA。 |
| [Office 365 免费版](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans)<br>[Azure AD 免费版](../verifiable-credentials/how-to-create-a-free-developer-account.md) | 可以根据需要使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)提示用户进行多重身份验证，但无法精细控制已启用的用户或方案，但它确实提供了额外的安全步骤。<br /> 即使不使用安全默认值来为每个人启用多重身份验证，也可以将分配有“Azure AD 全局管理员”角色的用户配置为使用多重身份验证。 此免费层功能确保关键的管理员帐户受到多重身份验证的保护。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较

下表提供了 Azure AD 多重身份验证的各个版本中可用的功能列表。 规划保护用户身份验证的需求，然后确定哪种方法符合这些要求。 例如，尽管 Azure AD Free 提供安全默认值来实现 Azure AD 多重身份验证，但只能通过手机验证器应用显示身份验证提示，而不能通过电话呼叫或短信来显示。 如果无法确保将手机身份验证应用安装到用户的个人设备上，此方法可能存在限制。 有关更多详细信息，请参阅本主题后面的 [Azure AD 免费层](#azure-ad-free-tier)。 

| 特征 | Azure AD Free - 安全默认值（已为所有用户启用） | Azure AD Free - 仅限全局管理员 | Office 365 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保护 Azure AD 租户管理员帐户 | ● | ●（仅限“Azure AD 全局管理员”帐户） | ● | ● |
| 将移动应用用作第二个因素 | ● | ● | ● | ● |
| 将电话呼叫用作第二个因素 | | ● | ● | ● |
| 将短信用作第二个因素 | | ● | ● | ● |
| 管理员控制验证方法 | | ● | ● | ● |
| 欺诈警报 | | | | ● |
| MFA 报告 | | | | ● |
| 通话的自定义问候语 | | | | ● |
| 通话的自定义呼叫方 ID | | | | ● |
| 受信任的 IP | | | | ● |
| 记住受信任的设备的 MFA | | ● | ● | ● |
| 适用于本地应用程序的 MFA | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>购买和启用 Azure AD 多重身份验证

若要使用 Azure AD 多重身份验证，请注册或购买符合条件的 Azure AD 层。 Azure AD 提供四个版本：Free、Office 365、Premium P1 和 Premium P2。

Free 版本随附在 Azure 订阅中。 有关如何使用安全默认值或者使用“Azure AD 全局管理员”角色保护帐户的信息，请参阅[下面的部分](#azure-ad-free-tier)。

Azure AD Premium 版本通过 Microsoft 代表、[开放批量许可计划](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)和[云解决方案提供商计划](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)提供。 Azure 和 Microsoft 365 订阅者还可以在线购买 Azure Active Directory 高级版 P1 和 P2。 [登录](https://portal.office.com/Commerce/Catalog.aspx)购买。

购买所需的 Azure AD 层后，请[规划并部署 Azure AD 多重身份验证](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free 层

Azure AD Free 租户中的所有用户都可以通过安全默认值使用 Azure AD 多重身份验证。 使用 Azure AD Free 安全默认值时，只能使用手机身份验证应用来完成 Azure AD 多重身份验证。

* [详细了解 Azure AD 安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md)
* [为 Azure AD 免费版中的用户启用安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果不希望为所有用户启用 Azure AD 多重身份验证，可以改为选择仅使用“Azure AD 全局管理员”角色来保护用户帐户。 此方法针对关键的管理员帐户提供更多的身份验证提示。 可以通过以下方式之一启用 Azure AD 多重身份验证，具体取决于所使用的帐户类型：

* 如果使用 Microsoft 帐户，请[注册多重身份验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果使用的不是 Microsoft 帐户，请[在 Azure AD 中为用户或组启用多重身份验证](howto-mfa-userstates.md)。

## <a name="next-steps"></a>后续步骤

* 有关费用的详细信息，请参阅 [Azure AD 定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。
* [什么是条件访问？](../conditional-access/overview.md)

