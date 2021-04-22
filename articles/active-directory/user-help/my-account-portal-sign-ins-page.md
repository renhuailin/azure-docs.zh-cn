---
title: 从“我的登录”页面查看和搜索最近的登录活动 - Azure Active Directory | Microsoft Docs
description: 有关如何在“我的帐户”门户的“我的登录”页中查看和搜索最近登录活动的详细信息。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096066"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>从“我的登录”页面查看和搜索最近的登录活动

可以从“我的帐户”门户的“我的登录”页中查看所有最近的工作或学校帐户登录活动。  通过复查登录历史记录来查看以下内容，有助于检查是否有异常活动：

- 是否有人尝试破解你的密码。
- 是否有攻击者成功登录你的帐户以及登录位置。
- 攻击者尝试访问的应用。

>[!Note]
> 如果你在使用个人 Microsoft 帐户登录时遇到错误，你仍可以使用组织的域名（例如 contoso.com）登录，也可以以管理员身份使用组织的“租户 ID”通过以下 URL 之一登录：
>
>   - https://myaccount.microsoft.com?tenantId= your_domain_name
>   - https://myaccount.microsoft.com?tenant= your_tenant_ID

## <a name="view-your-recent-sign-in-activity"></a>查看近期的登录活动

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

2. 从左侧导航窗格中选择“我的登录”，或从“我的登录”块中选择“查看最近的活动”链接  。

    ![突出显示了最近活动链接“我的帐户”页](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展开并查看每个登录项，确保辨认得出每一项。 如果发现不熟悉的登录项，请更改密码，以帮助保护你的帐户以防被盗。

    ![包含展开的登录详细信息的近期活动页面](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果发现登录成功

有时，在查看自己的正常登录活动时，可能会发现从不熟悉的位置、浏览器或操作系统的成功登录。 不熟悉的登录可能意味着攻击者已获得帐户的访问权限。 如果你发现未授权活动，建议你立即更改密码，然后转到[安全信息](https://mysignins.microsoft.com/security-info)以更新安全设置。

在确定某些内容不正确之前，请确保看到的不是误报（即该项看起来可疑，但没问题）。 例如，我们根据你的 IP 地址确定你的大致位置和地图。 由于移动网络有时会将流量传送到较远的位置，因此很难精确定位。 即使你使用移动设备在华盛顿州登录，该位置也可能会显示登录来自加利福尼亚。 我们强烈建议你查看详细信息，而不是仅根据位置来判断。 请确保操作系统、浏览器和应用程序都正常运行。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果发现登录失败

如果你发现登录失败，可能意味着你键入了错误的凭据。 还可能意味着攻击者正在尝试猜测你的密码。 为了应对此风险，你无需更改密码，但建议你注册 Azure AD 多重身份验证 (MFA)。 如果使用多重身份验证，即使黑客最终猜到你的密码，也不能访问你的帐户。

![失败登录磁贴](media/my-account-portal-sign-ins-page/unsuccessful.png)

如果发现登录失败，且“会话活动”下有一条注释，内容为 `Additional verification failed, invalid code`，这意味着主身份验证凭据成功，但多重身份验证失败。 这种情况可能表示攻击者正确地猜到了你的密码，但无法通过多重身份验证质询。 建议你仍要更改密码，因为攻击者已获得了密码，然后转到[安全信息](https://mysignins.microsoft.com/security-info)页更新安全设置。

## <a name="search-for-specific-sign-in-activity"></a>搜索特定的登录活动

你可以通过任何可用的信息搜索最近的登录活动。 例如，可以按操作系统、位置、应用等搜索最近的登录活动。

1. 在“查看最近的活动”页上，将要搜索的信息输入到“搜索”栏中。 例如，键入 `Unsuccessful` 来搜索“我的帐户”应用收集的所有失败登录活动。

2. 选择“搜索”按钮即可开始搜索。

    ![“最近的活动”页，显示突出显示的搜索栏、搜索按钮和结果](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>确认异常活动

标记为异常活动的登录可以在“我的登录”页中该活动的相应磁贴上进行确认。

![异常登录磁贴用于确认你是否尝试过登录](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>后续步骤

查看最近的登录活动后，可以：

- 查看或管理[安全信息](./security-info-setup-signin.md)。

- 查看或管理已连接的[设备](my-account-portal-devices-page.md)。

- 查看或管理[组织](my-account-portal-organizations-page.md)。

- 查看组织如何[使用隐私相关数据](my-account-portal-privacy-page.md)。

- 更改[我的帐户门户设置](my-account-portal-settings.md)