---
title: Azure AD 多重身份验证提示和会话生存期
description: 了解 Azure AD 多重身份验证的重新身份验证提示的建议配置和会话生存期的应用方式。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/12/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93f5af54992189d553c9e5c5c141a6ca08b7018e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860526"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>优化重新身份验证提示并了解 Azure AD 多重身份验证的会话生存期

Azure Active Directory (Azure AD) 包含多个设置，用于确定用户需要重新执行身份验证的频率。 这种重新身份验证可以是只使用第一重验证（如密码、FIDO 或无密码 Microsoft Authenticator），也可以是执行多重身份验证 (MFA)。 你可以根据自己的环境和需要的用户体验配置这些重新身份验证设置。

Azure AD 的默认用户登录频率配置为 90 天滚动时限。 经常要求用户提供凭据看似很明智，但有时会适得其反。 如果用户习惯于不加思索地输入凭据，可能会无意中将凭据提供给恶意的凭据提示。

即使任何违反 IT 策略的情况都会撤销会话，但不要求用户再次登录听起来也很令人吃惊。 部分示例包括密码更改、不合规的设备或帐户禁用操作。 也可以[通过 PowerShell 显式撤销用户会话](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)。

本文详细介绍了建议的配置以及不同设置的工作原理和相互之间的交互。

## <a name="recommended-settings"></a>建议的设置

若要通过要求用户按适当的频率登录来为用户适当平衡安全性和易用性，我们建议使用以下配置：

* 如果有 Azure AD Premium：
    * 请使用[受管理设备](../devices/overview.md)或[无缝 SSO](../hybrid/how-to-connect-sso.md) 跨应用程序启用单一登录 (SSO)。
    * 如需重新进行身份验证，请使用条件访问[登录频率策略](../conditional-access/howto-conditional-access-session-lifetime.md)。
    * 对于从非托管设备或移动设备场景登录的用户，持久性浏览器会话可能不是首选方案，或者你可使用条件访问通过登录频率策略启用持久性浏览器会话。 根据登录风险将持续时间限制为适当的时间，用户风险越小，会话持续时间越长。
* 如果拥有 Microsoft 365 应用许可证或 Azure AD 免费层：
    * 请使用[受管理设备](../devices/overview.md)或[无缝 SSO](../hybrid/how-to-connect-sso.md) 跨应用程序启用单一登录 (SSO)。
    * 保持启用“保持登录状态”选项，并引导用户接受该选项。
* 对于移动设备方案，请确保用户使用 Microsoft Authenticator 应用。 此应用被用作其他 Azure AD 联合应用的代理，可减少设备上的身份验证提示。

我们的研究表明，这些设置适用于大多数租户。 这些设置的某些组合（如“记住 MFA”和“保持登录状态”）可能会导致过于频繁地提示用户进行身份验证 。 定期的重新身份验证提示会影响用户的工作效率，并且可能使用户更易受到攻击。

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD 会话生存期配置设置

若要为用户优化身份验证提示频率，你可以配置 Azure AD 会话生存期选项。 了解业务和用户的需求，并配置可为你的环境提供最佳平衡的设置。

### <a name="evaluate-session-lifetime-policies"></a>评估会话生存期策略

如果没有任何会话生存期设置，浏览器会话中就不会有持久性 Cookie。 用户每次关闭再打开浏览器时，都会收到重新进行身份验证的提示。 在 Office 客户端中，默认时间周期为 90 天的滚动周期。 使用此默认 Office 配置，如果用户已重置密码或处于非活动状态超过 90 天，系统会要求用户对所有必需的因素（第一个和第二个因素）重新进行身份验证。

用户在没有 Azure AD 标识的设备上可能看到多个 MFA 提示。 每个应用程序都有其未与其他客户端应用共享的 OAuth 刷新令牌时，会产生多个提示。 在这种情况下，MFA 会由于每个应用程序都请求使用 MFA 验证 OAuth 刷新令牌而进行多次提示。

在 Azure AD 中，由对会话生存期限制最严格的策略决定用户什么时候需要重新进行身份验证。 假设出现了下面这种情景：

* 你启用了“保持登录状态”，该选项会使用持久性浏览器 Cookie；并且
* 你还启用了“记住 MFA 14 天”

在这个示例场景中，用户每 14 天便需要重新进行身份验证。 即使“使我保持登录状态”本身不会要求用户在浏览器中重新进行身份验证，但此行为仍遵循限制最严格的策略。

### <a name="managed-devices"></a>托管设备

使用 Azure AD 联接或混合 Azure AD 联接与 Azure AD 联接的设备会收到[主刷新令牌 (PRT)](../devices/concept-primary-refresh-token.md)，以便跨应用程序使用单一登录 (SSO)。 这个 PRT 使用户能够在设备上登录一次，并使 IT 工作人员确保能符合安全性和合规性标准。 如果针对某些应用或场景，需要要求用户在已联接的设备上更频繁地登录，可以使用[条件访问登录频率](../conditional-access/howto-conditional-access-session-lifetime.md)来实现此目的。

### <a name="show-option-to-remain-signed-in"></a>显示保持登录状态的选项

如果用户在登录期间对“保持登录状态?”选项选择“是”，这会在浏览器上设置持久性 Cookie。 这个持久性 Cookie 会记住第一个和第二个因素，并且它仅适用于浏览器中的身份验证请求。

![保持登录状态示例提示的屏幕截图](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

如果有 Azure AD Premium 1 许可证，建议你使用条件访问策略来实现持久性浏览器会话。 此策略覆盖“保持登录状态?”设置，提供改进的用户体验。 如果没有 Azure AD Premium 1 许可证，建议你为用户启用“保持登录状态”设置。

有关配置使用户保持登录状态的选项的详细信息，请参阅[自定义 Azure AD 登录页](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)。

### <a name="remember-multi-factor-authentication"></a>记住多重身份验证  

此设置使你可以配置一个 1-365 天之间的值，并于用户在登录时选择“X 天内不再询问”选项时，在浏览器上设置持久性 Cookie。

![同意登录请求的示例提示的屏幕截图](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

此设置减少了 Web 应用的身份验证次数，但增加了新式身份验证客户端（如 Office 客户端）的身份验证次数。 这些客户端通常仅在密码重置或处于非活动状态 90 天后，才会显示提示。 但是，将此值设置为小于 90 天会缩短 Office 客户端的默认 MFA 提示周期，增加重新身份验证的频率。 如果与“保持登录状态”或条件访问策略结合使用，它可能会增加身份验证请求次数。

如果使用“记住 MFA”选项并且拥有 Azure AD Premium 1 许可证，请考虑将这些设置迁移到“条件访问登录频率”。 否则，请考虑改用“使我保持登录状态?”。

有关详细信息，请参阅[记住多重身份验证](howto-mfa-mfasettings.md#remember-multi-factor-authentication)。

### <a name="authentication-session-management-with-conditional-access"></a>使用条件访问的身份验证会话管理

通过“登录频率”，管理员可以在客户端和浏览器中选择同时适用于第一个和第二个因素的登录频率。 如果出现需要为关键业务应用程序等限制身份验证会话的情况，建议将这些设置与受管理设备结合使用。

持久性浏览器会话可让用户在关闭再重新打开浏览器窗口后保持登录状态。 与“保持登录状态”设置类似，它会在浏览器上设置一个持久性 Cookie。 但它由管理员配置，所以不要求用户为“保持登录状态?”选项选择“是”，从而提供更好的用户体验。 如果使用“保持登录状态?”选项，则建议改为启用“持久性浏览器会话”策略。

有关详细信息， 请参阅[配置使用条件访问的身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

### <a name="configurable-token-lifetimes"></a>可配置令牌生存期

通过此设置可配置 Azure Active Directory 颁发的令牌的生存期。 此策略已被替换为“使用条件访问的身份验证会话管理”。 如果你现在正在使用“可配置的令牌生存期”，建议开始迁移到条件访问策略。

## <a name="review-your-tenant-configuration"></a>查看租户配置  

现在，你已了解不同设置的工作原理和建议的配置，是时候检查租户配置并进行相应更改了：

若要配置或查看“保持登录状态”选项，请完成以下步骤：

1. 在 Azure AD 门户中，搜索并选择“Azure Active Directory”。
1. 选择“公司品牌”，然后为每个区域设置选择“显示保持登录状态的选项” 。
1. 选择“是”，然后选择“保存”。

若要在受信任的设备上记住多重身份验证设置，请完成以下步骤：

1. 在 Azure AD 门户中，搜索并选择“Azure Active Directory”。
1. 选择“安全性”，然后选择“MFA” 。
1. 在“配置”下，选择“其他基于云的 MFA 设置” 。
1. 在“多重身份验证服务设置”页中，滚动到“记住多重身份验证设置”。 取消选中相应复选框即可禁用此设置。

若要配置用于登录频率和持久性浏览器会话的条件访问策略，请完成以下步骤：

1. 在 Azure AD 门户中，搜索并选择“Azure Active Directory”。
1. 选择“安全性”，然后选择“条件访问” 。
1. 使用本文详述的建议会话管理选项配置策略。

若要查看令牌生存期，请访问[使用 Azure AD PowerShell 查询任何 Azure AD 策略](../develop/configure-token-lifetimes.md#get-started)。 禁用已有的任何策略。

如果租户中启用了多个设置，建议你根据可用的许可更新设置。 例如，如果你有 Azure AD Premium 许可证，应只使用“登录频率”和“持久性浏览器会话”条件访问策略 。 如果你有 Microsoft 365 应用或 Azure AD 免费许可证，应使用“保持登录状态?”配置。

如果启用了可配置的令牌生存期，此功能很快会被删除。 计划向条件访问策略迁移。

下表汇总了基于许可证的建议：

|              | Azure AD Free 和 Microsoft 365 应用 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | 适用于非管理的设备的 [Azure AD 联接](../devices/concept-azure-ad-join.md)、[混合 Azure AD 联接](../devices/concept-azure-ad-join-hybrid.md)或[无缝 SSO](../hybrid/how-to-connect-sso.md)。 | Azure AD 加入<br />混合 Azure AD 加入 |
| **重新身份验证设置** | 保持登录状态                  | 对登录频率和持久性浏览器会话使用条件访问策略 |

## <a name="next-steps"></a>后续步骤

在开始操作前，请完成教程[使用 Azure AD 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md)或[对用户登录使用风险检测以触发 Azure AD 多重身份验证](tutorial-risk-based-sspr-mfa.md)。
