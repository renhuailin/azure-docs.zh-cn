---
title: 使用“我的员工”委托用户管理权限 - Azure AD | Microsoft Docs
description: 使用“我的员工”和管理单元来委托用户管理权限
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: c7f05973f7d609ab3f4dc0f2f567ede4ce0d0f64
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124830920"
---
# <a name="manage-your-users-with-my-staff"></a>使用“我的员工”管理用户

使用“我的员工”可将权限委托给某个权威人物，例如商店经理或团队主管，以确保其员工能够访问他们的 Azure AD 帐户。 组织可将常见任务（例如重置密码或更改电话号码）委托给本地团队经理，而不是依赖于中心支持人员。 通过“我的员工”门户，无法访问帐户的用户只需点击几下即可重新获取访问权限，无需求助支持人员或 IT 人员。

在为组织配置“我的员工”之前，我们建议查看本文档以及[用户文档](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd)，确保了解“我的员工”的工作原理及其对用户的影响。 可以利用用户文档来培训用户，让其准备好适应新的体验，并帮助确保“我的员工”成功推出。

## <a name="how-my-staff-works"></a>“我的员工”的工作原理

“我的员工”基于管理单元。管理单元是资源的容器，可用于限制角色分配的管理控制范围。 有关详细信息，请参阅 [Azure Active Directory 中的管理单元管理](administrative-units.md)。 在“我的员工”中，管理单元可用于包含商店或部门中的一组用户。 然后，可将团队经理分配到一个或多个单元范围的管理角色。

## <a name="before-you-begin"></a>开始之前

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。

  * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个与订阅关联的 Azure Active Directory 租户。

  * 如果需要，请[创建一个 Azure Active Directory 租户](../fundamentals/sign-up-organization.md)或[将 Azure 订阅关联到你的帐户](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
* 需要在 Azure AD 租户中拥有“全局管理员”权限才能启用基于短信的身份验证。
* 短信身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用该方法也是如此。 每个启用的用户必须拥有以下 Azure AD 或 Microsoft 365 许可证之一：

  * [Azure AD Premium P1 或 P2](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)
  * [Microsoft 365 F1 或 F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [企业移动性 + 安全性 (EMS) E3 或 E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) 或 [Microsoft 365 E3 或 E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>如何启用“我的员工”

配置管理单元后，可将此范围应用到访问“我的员工”的用户。 只有拥有管理角色的用户才可以访问“我的员工”。 若要启用“我的员工”，请完成以下步骤：

1. 以用户管理员身份登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “用户设置” > “用户功能预览” > “管理用户功能预览设置”。   

1. 在“管理员可以访问‘我的员工’”下，可以选择为所有用户、选定用户启用“我的员工”，或者不允许任何用户访问“我的员工”。

> [!Note]
> 只有拥有管理员角色的用户才可以访问“我的员工”。 如果为不拥有管理员角色的用户启用“我的员工”，他们将无法访问“我的员工”。

## <a name="conditional-access"></a>条件性访问

可以使用 Azure AD 条件访问策略来保护“我的员工”门户。 请将其用于要求在访问“我的员工”之前完成多重身份验证等任务。

强烈建议使用 [Azure AD 条件访问策略](../conditional-access/index.yml)来保护“我的员工”。 若要将条件访问策略应用到“我的员工”，首先必须花费几分钟时间访问“我的员工”站点一次，以自动预配租户中供条件访问使用的服务主体。

在创建应用于“我的员工”云应用程序的条件访问策略时，你将看到该服务主体。

![为“我的员工”应用创建条件访问策略](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>使用“我的员工”

当用户转到“我的员工”时，将会看到他们对其拥有管理权限的[管理单元](administrative-units.md)的名称。 [“我的员工”用户文档](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd)中使用术语“位置”来表示管理单元。 如果管理员的权限不带有管理单元范围，则这些权限将在整个组织中应用。 启用“我的员工”后，拥有管理角色的已启用用户可以通过 [https://mystaff.microsoft.com](https://mystaff.microsoft.com) 访问“我的员工”。 他们可以选择某个管理单元来查看该单元中的用户，并选择一个用户来打开其配置文件。

## <a name="reset-a-users-password"></a>重置用户密码

必须满足以下先决条件才能为本地用户重置密码。 有关详细说明，请参阅[启用自助式密码重置](../authentication/tutorial-enable-sspr-writeback.md)教程。

* 配置密码写回权限
* 在 Azure AD Connect 中启用密码写回
* 在 Azure AD 自助式密码重置 (SSPR) 中启用密码写回

以下角色拥有重置用户密码的权限：

* [身份验证管理员](permissions-reference.md#authentication-administrator)
* [特权身份验证管理员](permissions-reference.md#privileged-authentication-administrator)
* [全局管理员](permissions-reference.md#global-administrator)
* [支持管理员](permissions-reference.md#helpdesk-administrator)
* [用户管理员](permissions-reference.md#user-administrator)
* [密码管理员](permissions-reference.md#password-administrator)

在“我的员工”中打开用户的配置文件。 选择“重置密码”。

* 如果该用户是仅限云的用户，则你可以看到一个可为该用户指定的临时密码。
* 如果该用户已从本地 Active Directory 同步，则你可以输入符合本地 AD 策略的密码。 然后，可将该密码提供给该用户。

    ![密码重置进度指示器和成功通知](./media/my-staff-configure/reset-password.png)

用户下一次登录时必须更改其密码。

## <a name="manage-a-phone-number"></a>管理电话号码

在“我的员工”中打开用户的配置文件。

* 选择“添加电话号码”部分可以添加用户的电话号码
* 选择“编辑电话号码”可以更改电话号码
* 选择“删除电话号码”可以删除用户的电话号码

然后，根据你的设置，用户可以使用设置的电话号码通过短信登录、执行多重身份验证，以及执行自助式密码重置。

必须拥有以下角色之一才能管理用户的电话号码：

* [身份验证管理员](permissions-reference.md#authentication-administrator)
* [特权身份验证管理员](permissions-reference.md#privileged-authentication-administrator)
* [全局管理员](permissions-reference.md#global-administrator)

## <a name="search"></a>搜索

可以使用“我的员工”中的搜索栏搜索组织中的管理单元和用户。 你可以跨组织中的所有管理单元和用户进行搜索，但只能更改你对其拥有管理权限的管理单元中的用户。

还可以在管理单元中搜索用户。 为此，请使用用户列表顶部的搜索栏。

## <a name="audit-logs"></a>审核日志

可以在 Azure Active Directory 门户中查看在“我的员工”中执行的操作的审核日志。 如果审核日志是“我的员工”中执行的操作生成的，则审核事件中“更多详细信息”下面会指示这一点。

## <a name="next-steps"></a>后续步骤

[“我的员工”用户文档](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd)
[管理单元文档](administrative-units.md)