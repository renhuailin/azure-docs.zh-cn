---
title: 使用智能锁定防止攻击 - Azure Active Directory
description: 了解 Azure Active Directory 智能锁定如何帮助保护组织免受试图猜出用户密码的暴力攻击。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3f8d5fb55d547a1c0602843fb36f19ad45dbc2a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536578"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>利用 Azure Active Directory 智能锁定保护用户帐户免受攻击

智能锁定功能用于锁定那些试图猜测用户密码或使用暴力破解方法进入系统的恶意行动者。 智能锁定功能可识别有效用户的登录，将其与攻击者和其他未知来源的登录区别对待。 攻击者会被锁定，而你的用户可继续访问其帐户，工作效率很高。

## <a name="how-smart-lockout-works"></a>智能锁定的工作原理

默认情况下，在 Azure 公共租户和 Azure 中国世纪互联租户尝试失败 10 次或者 Azure 美国政府租户尝试失败 3 次后，智能锁定会将帐户锁定一分钟，使其无法进行登录尝试。 在每次后续登录尝试失败后，帐户会再次锁定，第一次锁定一分钟，后续尝试失败会锁定更长时间。 若要最大程度地减少攻击者可绕过此行为的方法，我们不公开锁定时段随着额外的不成功登录尝试而增长的速率。

智能锁定跟踪最后三个错误的密码哈希，以避免对相同密码增大锁定计数器。 如果有人多次输入同一个错误密码，此行为不会导致帐户被锁定。

> [!NOTE]
> 对于启用了直通身份验证的客户，哈希跟踪功能不可用，因为身份验证是在本地而不是在云中进行的。

使用 AD FS 2016 和 AF FS 2019 的联合身份验证部署可以使用 [AD FS Extranet 锁定和 Extranet 智能锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)来实现类似的好处。

智能锁定对于所有 Azure AD 客户始终可用，这些默认设置提供了合适的组合安全性和可用性。 要使用组织特定的值自定义智能锁定设置，需要向用户提供 Azure AD Premium P1 或更高版本的许可证。

使用智能锁定并不能保证真正的用户永远不会被锁定。当智能锁定锁定用户帐户时，我们将尽最大努力不锁定真正的用户。 锁定服务会尽力确保不良参与者无法访问真正的用户帐户。 请注意以下事项：

* 每个 Azure AD 数据中心独立地跟踪锁定。 如果用户访问了每个数据中心，则该用户具有 (threshold_limit * datacenter_count) 次尝试。
* 智能锁定使用熟悉的位置与不熟悉的位置来区分不良参与者与真正的用户。 不熟悉的位置和熟悉的位置都具有独立的锁定计数器。

智能锁定可与混合部署集成，使用密码哈希同步或传递身份验证来避免攻击者锁定本地 Active Directory 域服务 (AD DS) 帐户。 通过在 Azure AD 中适当地设置智能锁定策略，可在攻击到达本地 AD DS 之前将其筛选掉。

使用[直通身份验证](../hybrid/how-to-connect-pta.md)时，请注意以下事项：

* Azure AD 锁定阈值小于 AD DS 帐户锁定阈值。 设置这些值，使 AD DS 帐户锁定阈值至少是 Azure AD 锁定阈值的二倍或三倍。
* Azure AD 锁定持续时间必须设置为长于 AD DS 重置帐户锁定计数器的持续时间。 Azure AD 持续时间以秒为单位进行设置，而 AD 持续时间以分钟为单位进行设置。

例如，如果希望 Azure AD 智能锁定持续时间长于 AD DS，则 Azure AD 将为 120 秒（2 分钟），而本地 AD 将设置为 1 分钟（60 秒）。 如果希望 Azure AD 锁定阈值为 5，则需要本地 AD 锁定阈值为 10。  此配置将确保智能锁定可防止本地 AD 帐户被针对 Azure AD 帐户的暴力攻击锁定。

> [!IMPORTANT]
> 目前，如果用户的云帐户已被智能锁定功能锁定，管理员将无法为其解锁。 管理员必须等到锁定持续时间到期。 但是，用户可使用自助密码重置 (SSPR) 从受信任的设备或位置解锁。

## <a name="verify-on-premises-account-lockout-policy"></a>验证本地帐户锁定策略

若要验证本地 AD DS 帐户锁定策略，请使用管理员特权在已加入域的系统中完成以下步骤：

1. 打开“组策略管理”工具。
2. 编辑包含组织帐户锁定策略的组策略，例如默认域策略。
3. 浏览到“计算机配置” > “策略” > “Windows 设置” > “安全设置” > “帐户策略” > “帐户锁定策略”。
4. 验证“帐户锁定阈值”和“在此后重置帐户锁定计数器”值。

![修改本地 Active Directory 帐户锁定策略](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>管理 Azure AD 智能锁定值

根据组织要求，可自定义 Azure AD 智能锁定值。 要使用组织特定的值自定义智能锁定设置，需要向用户提供 Azure AD Premium P1 或更高版本的许可证。 智能锁定设置自定义不适用于Azure 中国世纪互联租户。

若要检查或修改组织的智能锁定值，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后选择“安全” > “身份验证方法” > “密码保护”  。
1. 根据帐户在第一次锁定之前允许的登录失败次数，设置“锁定阈值”。

    Azure 公共租户的默认值为 10，Azure 美国政府租户的默认值为 3。

1. 将“锁定持续时间(以秒计)”设置为每次锁定的时长（以秒计）。

    默认值为 60 秒（一分钟）。

> [!NOTE]
> 如果锁定后的首次登录也失败了，则帐户再次锁定。 如果帐户重复锁定，则锁定持续时间增加。

![在 Azure 门户中自定义 Azure AD 智能锁定策略](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="testing-smart-lockout"></a>测试智能锁定

触发智能锁定阈值后，将在帐户锁定时收到以下消息：

*帐户暂时锁定以防止未经授权的使用。请稍后再试！如果仍有问题，请与管理员联系。*

测试智能锁定时，由于 Azure AD 身份验证服务的地理分布和负载均衡特性，登录请求可能由不同的数据中心处理。 在这种情况下，由于每个 Azure AD 数据中心独立地跟踪锁定，因此可能需要比所定义的锁定阈值更多的尝试次数才会导致锁定。 在完全锁定之前，用户最多有 (threshold_limit * datacenter_count) 次错误尝试机会。

智能锁定跟踪最后三个错误的密码哈希，以避免对相同密码增大锁定计数器。 如果有人多次输入相同的错误密码，则此行为不会导致帐户被锁定。


## <a name="default-protections"></a>默认保护
除了智能锁定之外，Azure AD 还会通过分析包含 IP 流量的信号和识别异常行为来防范攻击。 Azure AD 默认会阻止这些恶意登录，不管密码有效性如何，都会返回 [AADSTS50053 - IdsLocked 错误代码](../develop/reference-aadsts-error-codes.md)。

## <a name="next-steps"></a>后续步骤

若要进一步自定义体验，可[为 Azure AD 密码保护配置自定义受禁密码](tutorial-configure-custom-password-protection.md)。

若要帮助用户在 Web 浏览器中重置或更改其密码，可[配置 Azure AD 自助式密码重置](tutorial-enable-sspr.md)。
