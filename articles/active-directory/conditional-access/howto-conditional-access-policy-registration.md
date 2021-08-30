---
title: 条件访问 - 合并的安全信息 - Azure Active Directory
description: 创建用于安全信息注册的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/28/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa38195c836c77e9f0fd621776e997d4b7fff7bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744152"
---
# <a name="conditional-access-securing-security-info-registration"></a>条件访问：保护安全信息注册

通过使用条件访问策略中的用户操作，可以保护用户何时以及如何注册 Azure AD 多重身份验证和自助式密码重置。 此功能适用于启用了[合并注册](../authentication/concept-registration-mfa-sspr-combined.md)的组织。 此功能可让组织将注册过程视为条件访问策略中的任何应用程序，并使用条件访问的全部功能来保护体验。 登录 Microsoft Authenticator 应用或启用无密码电话登录的用户受此策略的约束。

过去，某些组织可能已使用受信任的网络位置或设备合规性来保护注册体验。 随着 Azure AD 中添加了[临时访问密码](../authentication/howto-authentication-temporary-access-pass.md)，管理员可以为其用户预配限定了时间的凭据，以允许用户从任何设备或位置进行注册。 临时访问密码凭据满足多重身份验证的条件访问要求。

## <a name="create-a-policy-to-secure-registration"></a>创建策略以保护注册

以下策略适用于尝试使用组合注册体验进行注册的选定用户。 策略要求用户执行多重身份验证或使用临时访问密码凭据。

1. 在“Azure 门户”中，浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 在“名称”中，输入此策略的名称。 例如，“TAP 上的合并安全信息注册”。
1. 在“分配”下，选择“用户和组”，并选择要将此策略应用到的用户和组。
   1. 在“包括”下，选择“所有用户”。  

      > [!WARNING]
      > 必须为[合并注册](../authentication/howto-registration-mfa-sspr-combined.md)启用用户。

   1. 在“排除”下。
      1. 选择“所有来宾和外部用户”。
      
         > [!NOTE]
         > 临时访问密码不适用于来宾用户。

      1. 选择“用户和组”，并选择组织的紧急访问或不受限帐户。 
1. 在“云应用或操作”下，选择“用户操作”，选中“注册安全信息”。
1. 在“访问控制” > “授予”下。
   1. 选择“授予访问权限”。
   1. 选择“需要多重身份验证”  。
   1. 单击“选择”。
1. 将“启用策略”设置为“打开”。
1. 然后选择“创建”。

管理员现在必须向新用户颁发临时访问密码凭据，让新用户能够满足多重身份验证注册的要求，从而进行注册。 可[在 Azure AD 门户创建临时访问密码](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)部分中找到完成此任务的步骤。

除了步骤 6b 中的“要求多重身份验证”之外，组织还可能选择要求其他授权控制，或者将其取代。 选择多个控件时，请确保选择适当的单选按钮切换，以在进行此更改时要求“所有”或“一个”所选控件。 

### <a name="guest-user-registration"></a>来宾用户注册

对于需要在目录中注册多重身份验证的[来宾用户](../external-identities/what-is-b2b.md)，你可以根据以下指南，选择阻止从[受信任的网络位置](concept-conditional-access-conditions.md#locations)外部进行注册。

1. 在“Azure 门户”中，浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 在“名称”中，输入此策略的名称。 例如，“受信任网络上的合并安全信息注册”。
1. 在“分配”下，选择“用户和组”，并选择要将此策略应用到的用户和组。
   1. 在“包括”下，选择“所有来宾和外部用户”。 
1. 在“云应用或操作”下，选择“用户操作”，选中“注册安全信息”。
1. 在“条件” > “位置”下。
   1. 配置“是”。
   1. 包括：“任何位置”。
   1. 排除所有受信任的位置。
   1. 在“位置”边栏选项卡上选择“完成”。
   1. 在“条件”边栏选项卡中，单击“完成”。
1. 在“访问控制” > “授予”下。
   1. 选择“阻止访问”。
   1. 然后单击“选择”。
1. 将“启用策略”设置为“打开”。
1. 再选择“保存”。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[要求用户重新确认身份验证信息](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
