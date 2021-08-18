---
title: 教程：使用 Azure Active Directory 为自动用户预配配置 BrowserStack 单一登录 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 BrowserStack 单一登录及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 39999abc-e4a2-4058-81e0-bf88182f8864
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2021
ms.author: thwimmer
ms.openlocfilehash: a990ab3a38c066ac13ff68ddde94a17fa9940bbd
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764448"
---
# <a name="tutorial-configure-browserstack-single-sign-on-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 BrowserStack 单一登录

本教程介绍在 BrowserStack 单一登录和 Azure Active Directory (Azure AD) 中配置自动用户预配要执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户预配到 [BrowserStack 单一登录](https://www.browserstack.com)并自动取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 BrowserStack 单一登录中创建用户
> * 在用户不再有访问需求的情况下，在 BrowserStack 单一登录中删除用户
> * 使用户特性在 Azure AD 与 BrowserStack 单一登录之间保持同步
> * [单一登录](./browserstack-single-sign-on-tutorial.md)到 BrowserStack 单一登录（建议）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* BrowserStack 中具有“所有者”权限的用户帐户。
* 具有 BrowserStack 的 [企业计划](https://www.browserstack.com/pricing)。 
* [单一登录](https://www.browserstack.com/docs/enterprise/single-sign-on/azure-ad) 与 BrowserStack（必需）的集成。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要[在 Azure AD 与 BrowserStack 单一登录之间映射](../app-provisioning/customize-application-attributes.md)哪些数据。 

## <a name="step-2-configure-browserstack-single-sign-on-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 BrowserStack 单一登录以支持通过 Azure AD 进行预配

1. 以拥有“所有者”权限的用户身份登录到 [BrowserStack](https://www.browserstack.com/users/sign_in)。

2. 导航到“帐户” -> “设置和权限” 。 选择“安全”选项卡。

3. 在“自动用户预配”下，单击“配置” 。

    ![设置](media/browserstack-single-sign-on-provisioning-tutorial/configure.png)

4. 选择要通过 Azure AD 控制的用户特性，然后单击“确认”。

    ![用户](media/browserstack-single-sign-on-provisioning-tutorial/attributes.png)

5. 复制“租户 URL”和“机密令牌” 。 在 Azure 门户的 BrowserStack 单一登录应用程序的“预配”选项卡中，将这些值输入“租户 URL”和“机密令牌”字段。 单击“完成”  。

    ![授权](media/browserstack-single-sign-on-provisioning-tutorial/credential.png)

6. 预配配置已保存在 BrowserStack 上。 Azure AD 上的预配设置完成后，在 BrowserStack 中启用用户设置，以防止阻止邀请 BrowserStack [帐户](https://www.browserstack.com/accounts/manage-users)中的新用户 。 

    ![帐户](media/browserstack-single-sign-on-provisioning-tutorial/enable.png)
    
## <a name="step-3-add-browserstack-single-sign-on-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 BrowserStack 单一登录

从 Azure AD 应用程序库添加 BrowserStack 单一登录，开始管理 BrowserStack 单一登录的预配。 如果之前为 SSO 设置过 BrowserStack 单一登录，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户分配到应用程序。 如果选择仅根据用户的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户分配到 BrowserStack 单一登录时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，请先在少量用户中进行测试。 如果预配范围设置为分配的用户，则可以先尝试将一两个用户分配到应用。 当预配范围设置为所有用户时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-browserstack-single-sign-on"></a>步骤 5。 配置 BrowserStack 单一登录的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户分配在应用中创建、更新和禁用用户。

### <a name="to-configure-automatic-user-provisioning-for-browserstack-single-sign-on-in-azure-ad"></a>在 Azure AD 中配置 BrowserStack 单一登录的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中选择“BrowserStack Single Sign-on”。

    ![“应用程序”列表中的“BrowserStack 单一登录”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，输入 BrowserStack 单一登录租户 URL 和机密令牌。 单击“测试连接”，确保 Azure AD 可连接到 BrowserStack 单一登录。 如果连接失败，请确保 BrowserStack 单一登录帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的人员的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 BrowserStack 单一登录” 。

9. 在“特性映射”部分中，查看从 Azure AD 同步到 BrowserStack 单一登录的用户特性。 选为“匹配”属性的特性用于匹配 BrowserStack 单一登录中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](../app-provisioning/customize-application-attributes.md)，需要确保 BrowserStack 单一登录 API 支持基于该特性筛选用户。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|支持筛选|
   |---|---|--|
   |userName|字符串|&check;|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_role|字符串|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_team|字符串|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_product|字符串|


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为 BrowserStack 单一登录启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 BrowserStack 单一登录的用户 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

- 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
- 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
- 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="connector-limitations"></a>连接器限制

* BrowserStack 单一登录不支持组预配。
* BrowserStack 单一登录要求“emails[type eq "work"].value”和“userName”具有相同的源值 。

## <a name="troubleshooting-tips"></a>故障排除提示

* 请参阅[此处](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#troubleshooting)的故障排除提示。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [在 BrowserStack 单一登录中配置特性映射](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad)
* [在 BrowserStack 中设置和启用自动用户预配](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#setup-and-enable-auto-user-provisioning)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)