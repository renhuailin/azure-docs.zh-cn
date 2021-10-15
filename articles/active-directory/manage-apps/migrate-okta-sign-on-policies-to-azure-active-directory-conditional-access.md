---
title: 有关将 Okta 登录策略迁移到 Azure Active Directory 条件访问策略的教程
titleSuffix: Active Directory
description: 本教程介绍如何将 Okta 登录策略迁移到 Azure Active Directory 条件访问策略。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 840d2cfd64f6384c8c503e472f7557ababd0d08e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389184"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>教程：将 Okta 登录策略迁移到 Azure Active Directory 条件访问策略

本教程介绍组织如何从 Okta 中的全局登录策略或应用程序级登录策略迁移到 Azure Active Directory (Azure AD) 条件访问策略，以保护 Azure AD 和已连接的应用程序中的用户访问。

本教程假设已将一个 Office 365 租户与 Okta 联合以用于登录和多重身份验证 (MFA)。 此外，应配置 Azure AD Connect 服务器或 Azure AD Connect 云预配代理，以将用户预配到 Azure AD。

## <a name="prerequisites"></a>先决条件

从 Okta 登录切换到 Azure AD 条件访问时，必须了解许可要求。 Azure AD 条件访问要求用户在注册 Azure AD 多重身份验证之前已分配有 Azure AD Premium P1 许可证。

在执行任何混合 Azure AD 加入步骤之前，需要在本地林中具有一个用于配置服务连接点 (SCP) 记录的企业管理员凭据。

## <a name="catalog-current-okta-sign-on-policies"></a>编录当前 Okta 登录策略

若要成功过渡到条件访问，请评估现有的 Okta 登录策略，以确定用于过渡到 Azure AD 的用例和要求。

1. 导航到“安全性” > “身份验证” > “登录”来检查全局登录策略  。

   ![显示全局登录策略的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   在此示例中，全局登录策略对配置的网络区域外部的所有会话强制执行 MFA。

   ![显示强制执行 MFA 的全局登录策略的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. 转到“应用程序”，检查应用程序级登录策略。 从子菜单中选择“应用程序”，然后从“活动应用列表”中选择你的 Office 365 已连接实例 。

3. 选择“登录”并滚动到页面底部。

在以下示例中，Office 365 应用程序登录策略有四个单独的规则：

- 对移动会话强制执行 MFA：要求 iOS 或 Android 上的每个新式身份验证会话或浏览器会话执行 MFA。
- 允许受信任的 Windows 设备：阻止系统提示受信任的 Okta 设备完成其他验证或满足其他因素。
- 要求不受信任的 Windows 设备执行 MFA：要求不受信任的 Windows 设备上的每个新式身份验证会话或浏览器会话执行 MFA。
- 阻止旧式身份验证：阻止任何旧式身份验证客户端连接到服务。

  ![显示 Office 365 登录规则的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="configure-condition-prerequisites"></a>配置“条件访问”先决条件

对于大多数方案而言，可将 Azure AD 条件访问策略配置为与 Okta 的条件匹配，而无需进行其他配置。

在某些方案中，可能需要在配置条件访问策略之前进行其他设置。 在撰写本文时，有两个已知的方案：

- Okta 网络位置到 Azure AD 中的命名位置：按照[在条件访问策略中使用位置条件](../conditional-access/location-condition.md#named-locations)中的说明在 Azure AD 中配置命名位置。
- Okta 设备对基于设备的 CA 的信任：在评估用户的设备时，条件访问提供两个可能的选项：

  - [使用混合 Azure AD 加入](#hybrid-azure-ad-join-configuration)，这是在 Azure AD Connect 服务器中启用的一项功能，可将 Windows 当前设备（例如 Windows 10、Windows Server 2016 和 Windows Server 2019）同步到 Azure AD。

  - [在 Endpoint Manager 中注册设备](#configure-device-compliance)，并分配合规性策略。

### <a name="hybrid-azure-ad-join-configuration"></a>混合 Azure AD 加入配置

若要在 Azure AD Connect 服务器上启用混合 Azure AD 加入，请运行配置向导。 配置后，需要执行相应的步骤来自动注册设备。

>[!NOTE]
>Azure AD Connect 云预配代理不支持混合 Azure AD 加入。

1. 若要启用混合 Azure AD 加入，请按照这些[说明](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)进行操作。

1. 在“SCP 配置”页上，选择“身份验证服务”下拉菜单 。 选择你的 Okta 联合提供程序 URL，然后选择“添加”。 输入你的本地企业管理员凭据，然后选择“下一步”。

   ![显示 SCP 配置的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

1. 如果在全局或应用级登录策略中阻止了 Windows 客户端的旧式身份验证，请制定一个规则以允许混合 Azure AD 加入过程完成。

1. 允许对所有 Windows 客户端实现整个旧式身份验证堆栈。 也可以联系 Okta 支持人员，以便在现有应用策略上启用该堆栈的自定义客户端字符串。

### <a name="configure-device-compliance"></a>配置设备合规性

混合 Azure AD 加入是对 Windows 上的 Okta 设备信任的直接替代。 条件访问策略还可以检查已完全注册到 Endpoint Manager 的设备的设备合规性：

- 合规性概述：参阅 [Intune 中的设备合规性策略](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows)。
- 设备合规性：[在 Intune 中创建策略](/mem/intune/protect/create-compliance-policy)。
- Windows 注册：如果已选择部署混合 Azure AD 加入，可以部署另一个组策略来完成[将这些设备自动注册到 Intune 的过程](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy)。
- iOS/iPadOS 注册：在注册 iOS 设备之前，必须在终结点管理控制台中完成[其他配置](/mem/intune/enrollment/ios-enroll)。
- Android 注册：在注册 Android 设备之前，必须在终结点管理控制台中完成[其他配置](/mem/intune/enrollment/android-enroll)。

## <a name="configure-azure-ad-multi-factor-authentication-tenant-settings"></a>配置 Azure AD 多重身份验证租户设置

在转换到条件访问之前，请确认组织的基本 Azure AD 多重身份验证租户设置。

1. 转到 [Azure 门户](https://portal.azure.com)并使用全局管理员帐户登录。

1. 选择“Azure Active Directory” > “用户” > “多重身份验证”，转到旧式 Azure AD 多重身份验证门户  。

   ![显示旧式 Azure AD 多重身份验证门户的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   也可以使用 [Azure AD 多重身份验证门户](https://aka.ms/mfaportal)的旧链接。

1. 在旧式“多重身份验证”菜单中，通过更改状态菜单中的“已启用”和“已强制执行”来确认没有为用户启用旧式 MFA  。 如果租户在下面的视图中具有用户，则必须在旧式 MFA 菜单中禁用这些用户。 只有这样，条件访问策略才对这些用户的帐户生效。

   ![显示在旧式 Azure AD 多重身份验证门户中禁用用户的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   “已强制执行”字段也应该是空的。

   ![显示旧式 Azure AD 多重身份验证门户中“已强制执行”字段为空的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enforced-empty-legacy-azure-ad-portal.png)

1. 选择“服务设置”选项。 将“应用密码”选项更改为“不允许用户创建应用密码登录非浏览器的应用” 。

1. 确保已清除“对来自我的 Intranet 上的联合用户的请求跳过多重身份验证”和“允许用户在其信任的设备上记住多重身份验证(1 到 365 天)”复选框，然后选择“保存”  。

  >[!NOTE]
   >请参阅[有关配置 MFA 提示设置的最佳做法](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。
   ![显示已在旧式 Azure AD 多重身份验证门户中清除相关复选框的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="configure-conditional-access-policies"></a>配置条件访问策略

配置先决条件并建立基本设置后，接下来请生成第一个条件访问策略。

1. 若要在 Azure AD 中配置条件访问策略，请转到 [Azure 门户](https://portal.azure.com)。 在“管理 Azure Active Directory”中选择“视图” 。

   遵循[有关部署和设计条件访问的最佳做法](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components)配置条件访问策略。

1. 若要模拟 Okta 中的全局登录 MFA 策略，请[创建一个策略](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)。

1. 创建[基于设备信任的条件访问规则](../conditional-access/require-managed-devices.md)。

   与本教程中的任何其他策略一样，此策略可以设置为以特定的应用程序和/或测试用户组为目标。

   ![显示正在测试用户的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![显示成功测试用户的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

1. 配置基于位置的策略和设备信任策略后，接下来请配置对应的[阻止旧式身份验证](../conditional-access/howto-conditional-access-policy-block-legacy.md)策略。

通过这三个条件访问策略，原始的 Okta 登录策略体验已复制到 Azure AD 中。 后续步骤涉及到通过 Azure 多重身份验证注册用户并测试策略。

## <a name="enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>在 Azure AD 多重身份验证中注册试点成员

配置条件访问策略后，用户必须注册 Azure 多重身份验证方法。 可以要求用户通过多种不同的方法进行注册。

1. 对于个人注册，请将用户定向到 [Microsoft 登录窗格](https://aka.ms/mfasetup)以手动输入注册信息。

1. 用户可以转到 [Microsoft 安全信息页](https://aka.ms/mysecurityinfo)以输入信息或管理 MFA 注册表单。

请参阅[此指南](../authentication/howto-registration-mfa-sspr-combined.md)来全面了解 MFA 注册过程。  

转到 [Microsoft 登录窗格](https://aka.ms/mfasetup)。 使用 Okta MFA 登录后，系统会指示你在 Azure AD 中注册 MFA。

>[!NOTE]
>如果用户过去已注册，则在按照 MFA 提示输入所需信息后，该用户将转到“我的安全性”信息页。
请参阅[有关 MFA 注册的用户文档](../user-help/security-info-setup-signin.md)。

## <a name="enable-conditional-access-policies"></a>启用条件访问策略

1. 若要展开测试，请将前面示例中创建的策略更改为“已启用测试用户登录”。 

   ![显示启用测试用户的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

1. 在接下来显示的 Office 365“登录”窗格中，系统会提示测试用户 John Smith 使用 Okta MFA 和 Azure AD 多重身份验证登录。

   ![显示 Azure 登录窗格的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

1. 通过 Okta 完成 MFA 验证。

   ![显示通过 Okta 进行 MFA 验证的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

1. 在用户按照 Okta MFA 提示填写完所需信息后，系统将提示用户进行条件访问。 确保策略已正确配置，并且处于要为 MFA 触发的条件范围内。

   ![显示通过 Okta 进行 MFA 验证的屏幕截图，其中提示进行条件访问。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="cut-over-from-sign-on-to-conditional-access-policies"></a>从登录策略切换到条件访问策略

在对试点成员进行全面测试以确保条件访问按预期生效后，可以在完成注册后将其余组织成员添加到条件访问策略。

为了避免在 Azure 多重身份验证与 Okta MFA 之间切换时反复出现提示，请通过修改登录策略来选择退出 Okta MFA。

可以通过分阶段或直接切换的方式来完成迁移到条件访问的最终步骤。

1. 转到 Okta 管理控制台，选择“安全性” > “身份验证”，然后转到“登录策略”  。

   >[!NOTE]
   > 仅当 Okta 的所有应用程序都受其自己的应用程序登录策略保护时，才将全局策略设置为“非活动”。
1. 将“强制执行 MFA”策略设置为“非活动” 。 还可以将策略分配到不包括 Azure AD 用户的新组。

   ![显示将全局 MFA 登录策略设置为“非活动”的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

1. 在应用程序级登录策略窗格中，通过选择“禁用规则”选项将策略更新为“非活动” 。 还可以将策略分配到不包括 Azure AD 用户的新组。

1. 确保至少为应用程序启用了一个允许在不执行 MFA 的情况下进行访问的应用程序级登录策略。

   ![显示在不执行 MFA 的情况下进行应用程序访问的屏幕截图。](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

1. 禁用 Okta 登录策略或者从强制执行组中排除已迁移的 Azure AD 用户后，系统只会在这些用户下次登录时才提示他们进行条件访问。

## <a name="next-steps"></a>后续步骤

有关从 Okta 迁移到 Azure AD 的详细信息，请参阅：

- [将应用程序从 Okta 迁移到 Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [将 Okta 联合迁移到 Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [将 Okta 同步预配迁移到基于 Azure AD Connect 的同步](migrate-okta-sync-provisioning-to-azure-active-directory.md)
