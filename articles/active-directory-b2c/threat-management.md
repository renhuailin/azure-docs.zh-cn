---
title: 缓解凭据攻击 - Azure AD B2C
titleSuffix: Azure AD B2C
description: 了解 Azure Active Directory B2C 中用于凭据攻击（密码攻击）的检测和缓解技术，包括智能帐户锁定功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99363e4f9f67e6ceca0d163131a2f2ff64b09857
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546966"
---
# <a name="mitigate-credential-attacks-in-azure-ad-b2c"></a>缓解 Azure AD B2C 中的凭据攻击

凭据攻击会导致未经授权的资源访问。 要求用户所设密码的复杂性合理。 Azure AD B2C 针对凭据攻击实施了缓解技术。 缓解技术包括检测暴力凭据攻击和字典凭据攻击。 Azure Active Directory B2C (Azure AD B2C) 使用各种信号分析请求的完整性。 Azure AD B2C 旨在智能地将目标用户与黑客和僵尸网络区分开来。

Azure AD B2C 使用复杂策略来锁定帐户。 将根据请求的 IP 和输入的密码锁定帐户。 锁定的持续时间也会根据存在攻击的可能性而延长。 密码尝试 10 次失败后（默认尝试阈值），会进行一分钟锁定。 在帐户解锁后（即在锁定期限到期后由服务自动解锁帐户后）下一次登录失败时，将再次进行一分钟锁定，每次登录失败都将继续锁定。 重复输入相同或类似的密码不会计为多次不成功登录。

> [!NOTE]
> [用户流、自定义策略](user-flow-overview.md) 和 [ROPC](add-ropc-policy.md) 流支持此功能。 它默认已激活，因此无需在用户流或自定义策略中对其进行配置。

## <a name="unlock-accounts"></a>解锁帐户

前 10 个锁定期限的长度为一分钟。 接下来的 10 个锁定期限时间稍长，并且每 10 个锁定期限后都会增加持续时间。 当帐户未锁定时，锁定计数器在成功登录后重置为零。 锁定期限可以持续长达五个小时。 用户必须等到锁定持续时间到期。 但是，用户可以使用自助 [密码用户流](add-password-reset-policy.md) 解锁。

## <a name="manage-password-protection-settings"></a>管理密码保护设置

若要管理密码保护设置（包括锁定阈值），请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 在“安全性”下，选择“身份验证方法(预览版)”，然后选择“密码保护”。
1. 在“自定义智能锁定”下，输入所需的密码保护设置：

   - “锁定阈值”：帐户首次锁定之前允许的失败登录尝试次数。如果锁定后的首次登录也失败了，则帐户再次锁定。
   - “锁定持续时间（以秒计）”：每次锁定的最短持续时间（以秒计）。 如果帐户重复锁定，则此持续时间增加。

       ![Azure AD 设置中的 Azure 门户密码保护页](./media/threat-management/portal-02-password-protection.png)
    <br />在“密码保护”设置中，将锁定阈值设置为 5。**

1. 选择“保存”。

## <a name="testing-the-password-protection-settings"></a>测试密码保护设置

智能锁定功能使用许多因素来确定何时应锁定帐户，但主要因素是密码模式。 智能锁定功能将密码的细微变化视为一个集，并计为单次尝试。 例如：

- 密码，比如 12456！ 和 1234567! （或 newAccount1234 和 newaccount1234）非常相似，因此算法将其解释为人为错误，并计为单次尝试。
- 模式中较大的变化，例如 12456！ 和 ABCD2！ 被视为单独的尝试。

测试智能锁定功能时，对输入的每个密码使用一种独特的模式。 考虑使用密码生成 web 应用，例如 [https://passwordsgenerator.net/](https://passwordsgenerator.net/)。

当帐户处于锁定状态时达到智能锁定阈值，将收到以下消息：“帐户暂时锁定以防止未经授权的使用。请稍后重试。” 可以将错误消息[本地化](localization-string-ids.md#sign-up-or-sign-in-error-messages)。

> [!NOTE]
> 测试智能锁定时，由于 Azure AD 身份验证服务的地理分布和负载均衡特性，登录请求可能由不同的数据中心处理。 在这种情况下，由于每个 Azure AD 数据中心独立地跟踪锁定，因此可能需要比所定义的锁定阈值更多的尝试次数才会导致锁定。 在完全锁定之前，用户最多有 (threshold_limit * datacenter_count) 次错误尝试机会。

## <a name="viewing-locked-out-accounts"></a>查看锁定的帐户

若要获取有关已锁定帐户的信息，可以检查 Active Directory [登录活动报表](../active-directory/reports-monitoring/concept-sign-ins.md)。 在“状态”下，选择“失败”。 登录尝试失败，“登录错误代码”为 `50053`，表示帐户已锁定：

![Azure AD 登录报表部分，显示锁定的帐户](./media/threat-management/portal-01-locked-account.png)

若要了解如何在 Azure Active Directory 中查看登录活动报表，请参阅[登录活动报表错误代码](../active-directory/reports-monitoring/concept-sign-ins.md)。

