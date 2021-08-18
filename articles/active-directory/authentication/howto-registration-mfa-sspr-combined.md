---
title: 启用合并安全信息注册 - Azure Active Directory
description: 了解如何通过组合 Azure AD 多重身份验证和自助式密码重置注册来简化最终用户体验。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/28/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82eb6494041ffab4f70eb546d24f8a24729b626
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090360"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>在 Azure Active Directory 中启用合并安全信息注册

在进行合并注册之前，用户分别注册了 Azure AD 多重身份验证和自助式密码重置 (SSPR) 的身份验证方法。 让人们感到困惑的是，Azure AD 多重身份验证和 SSPR 使用的方法相似，但他们却不得不同时注册这两个功能。 现在，通过合并注册，用户只需注册一次，便可同时获得 Azure AD 多重身份验证和 SSPR 带来的好处。

> [!NOTE]
> 自 2020 年 8 月 15 日起，将自动为所有新的 Azure AD 租户启用合并注册。 该日期之后创建的租户将无法使用旧注册工作流。

为确保用户在启用新体验之前已了解相关功能和影响，请参阅[合并安全信息注册概念](concept-registration-mfa-sspr-combined.md)。

![合并安全信息注册增强了体验](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>启用合并注册

要启用合并注册，请完成以下步骤：

1. 以用户管理员或全局管理员身份登录到 Azure 门户。
2. 转到“Azure Active Directory” > “用户设置” > “管理用户功能预览设置”。
3. 在“用户可以使用合并安全信息注册体验”下，选择为“选定”用户组或“所有”用户启用该体验。

   ![为用户启用合并安全信息体验](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> 启用合并注册后，通过新体验注册或确认其电话号码或移动应用的用户可以将它们同时用于 Azure AD 多重身份验证和 SSPR（如果已在 Azure AD 多重身份验证和 SSPR 策略中启用这些方法）。
>
> 如果之后禁用了此体验，则对于在 `https://aka.ms/ssprsetup` 处转到旧版 SSPR 注册页的用户，需要执行多重身份验证才能访问该页面。

如果已在 Internet Explorer 中配置“站点到区域分配列表”，则以下站点必须位于同一区域中：

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://Login.windows.net](https://login.windows.net)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>合并注册的条件访问策略

要确保用户注册 Azure AD 多重身份验证和自助式密码重置的安全，可以在条件访问策略中使用用户操作。 对于希望用户在 HR 入职期间从中心位置（例如受信任的网络位置）注册 Azure AD 多重身份验证和 SSPR 的组织，可以启用此功能。

> [!NOTE]
> 仅当用户访问合并注册页面时，此策略才适用。 当用户访问其他应用程序时，此策略不会强制进行 MFA 注册。
>
> 可以通过 [Azure 标识保护 - 配置 MFA 策略](../identity-protection/howto-identity-protection-configure-mfa-policy.md)来创建 MFA 注册策略。

有关在条件访问中创建受信任位置的详细信息，请参阅 [Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建要求从受信任位置进行注册的策略

请按照以下策略来创建策略，使其适用于所有尝试使用合并注册体验进行注册的选定用户，并且除非他们从标记为受信任网络的位置进行连接，否则将阻止访问：

1. 在“Azure 门户”中，浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“+ 新建策略”。
1. 输入此策略的名称，例如“受信任网络上的合并安全信息注册”。
1. 在“分配”下，选择“用户和组”。 选择要应用此策略的用户和组，然后选择“完成”。

   > [!WARNING]
   > 必须为用户启用合并注册。

1. 在“云应用或操作”下，选择“用户操作”。 选中“注册安全信息”，然后选择“完成”。

    ![创建用于控制安全信息注册的条件访问策略](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在“条件” > “位置”下，配置以下选项：
   1. 配置“是”。
   1. 包括：“任何位置”。
   1. 排除“所有受信任的位置”。
1. 在“位置”窗口中选择“完成”，然后在“条件”窗口中选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“阻止访问”、“选择”。
1. 将“启用策略”设置为“打开”。
1. 若要完成策略，请选择“创建”。

## <a name="next-steps"></a>后续步骤

如果需要帮助，请参阅[合并安全信息注册故障排除](howto-registration-mfa-sspr-combined-troubleshoot.md)或了解 [Azure AD 条件访问中的位置条件是什么？](../conditional-access/location-condition.md)

为用户启用合并注册之后，则可以[启用自助式密码重置](tutorial-enable-sspr.md)和[启用 Azure AD 多重身份验证](tutorial-enable-azure-mfa.md)。

如果需要，请了解如何[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。
