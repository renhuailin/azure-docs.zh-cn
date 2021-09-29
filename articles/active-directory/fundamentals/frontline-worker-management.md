---
title: 一线员工管理 - Azure Active Directory
description: 了解通过“我的员工”门户提供的一线员工管理功能。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 26d7e967ceb0208d6e852db9946ac0c260751dd3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732567"
---
# <a name="frontline-worker-management"></a>一线员工管理

一线员工占全球劳动力总人数的 80% 以上。 然而，由于规模大、人员流动快且流程分散，一线员工往往缺乏工具，无法让他们繁重的工作变得轻松一点。 一线员工管理为整个一线劳动力带来了数字化转型。 劳动力可以是经理、一线员工、运营和 IT 人员。

一线员工管理使一线劳动力更容易完成以下活动：
- 通过“我的员工”优化常见 IT 任务
- 通过简化的身份验证使一线员工轻松加入
- 无缝预配共享设备并使一线员工安全注销

## <a name="delegated-user-management-through-my-staff"></a>通过“我的员工”进行的委托用户管理

Azure Active Directory (Azure AD) 支持通过[“我的员工”门户](../roles/my-staff-configure.md)将用户管理委托给一线经理，帮助节省宝贵的时间并降低风险。 通过直接从商店或工厂车间启用简化的密码重置和电话管理，管理人员可以向员工授予访问权限，而无需通过技术支持、IT 或运营来路由请求。

![“我的员工”门户中的委托用户管理](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>通过简化的身份验证加速加入

“我的员工”还允许一线管理员注册其团队成员的电话号码以[通过短信登录](../authentication/howto-authentication-sms-signin.md)。 在许多垂直领域中，一线员工维护一个本地用户名和密码组合，这种解决方案通常比较繁琐、成本高昂且容易出错。 使用短信登录启用身份验证时，一线员工可以使用[单一登录 (SSO) ](../manage-apps/what-is-single-sign-on.md) 来登录 Microsoft Teams 和其他应用，只需使用其电话号码和通过短信发送的一次性密码 (OTP) 即可。 这使得一线员工的登录既简单又安全，并使他们能够快速访问其最需要的应用。

![短信登录](media/concept-fundamentals-frontline-worker/sms-signin.png)

一线管理员还可以使用托管主屏幕 (MHS) 应用程序，以允许员工访问其 Intune 注册的 Android 专用设备上的一组特定应用程序。 专用设备注册为 [Azure AD 共享设备模式](../develop/msal-shared-devices.md)。 在 Microsoft Endpoint Manager (MEM) 控制台中的多应用展台模式下进行配置时，MHS 将自动作为设备上的默认主屏幕启动，并作为唯一的主屏幕向最终用户显示。 若要了解详细信息，请参阅如何[配置适用于 Android Enterprise 的 Microsoft 托管主屏幕应用](/mem/intune/apps/app-configuration-managed-home-screen-app)。

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>从共享设备安全注销一线员工

许多公司都使用共享设备，这样一线员工就可以进行库存管理和销售点交易，而无需承担预配和跟踪个人设备的 IT 负担。 使用共享设备注销功能，一线员工在将共享设备交回中心或在转交给下一班队友之前，可轻松地从任何共享设备上的所有应用中安全注销。 Microsoft Teams 就是共享设备当前支持的其中一个应用，它允许一线员工查看分配给他们的任务。 一旦员工注销共享设备，Intune 和 Azure AD 就会清除所有公司数据，以便将设备安全地移交给下一个员工。 你可以选择使用 [Microsoft 身份验证库](../develop/msal-overview.md)将此功能集成到你的所有业务线 [iOS](../develop/msal-ios-shared-devices.md) 和 [Android](../develop/msal-android-shared-devices.md) 应用中。

![共享设备注销](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>后续步骤

- 有关委托的用户管理的详细信息，请参阅[“我的员工”用户文档](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd)。
- 有关 SAP SuccessFactors 的入站用户预配，请参阅有关[配置 Sap SuccessFactors Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)的教程。
- 对于 Workday 的入站用户预配，请参阅有关[配置 Workday 以实现自动用户预配](../saas-apps/workday-inbound-tutorial.md)的教程。