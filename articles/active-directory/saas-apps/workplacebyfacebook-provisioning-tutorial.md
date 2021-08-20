---
title: 教程：使用 Azure Active Directory 为 Workplace by Facebook 配置自动用户预配 | Microsoft Docs
description: 了解在 Workplace by Facebook 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: jeedes
ms.openlocfilehash: 5e4091138f51fdd5af4052895cdb75c2390f7ce5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459724"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教程：为 Workplace by Facebook 配置自动用户预配

本教程介绍在 Workplace by Facebook 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户预配到 [Workplace by Facebook](https://work.workplace.com/) 并自动解除预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Workplace by Facebook 中创建用户
> * 在用户不再有访问需求的情况下，在 Workplace by Facebook 中删除用户
> * 使用户属性在 Azure AD 和 Workplace by Facebook 之间保持同步
> * [单一登录](./workplacebyfacebook-tutorial.md)到 Workplace by Facebook（推荐）

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）
* 已启用 Workplace by Facebook 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Workplace by Facebook 之间映射](../app-provisioning/customize-application-attributes.md)的数据。

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Workplace by Facebook 以支持通过 Azure AD 进行预配

在配置和启用预配服务前，需确定 Azure AD 中哪些用户表示需要访问 Workplace by Facebook 应用的用户。 确定后，可按照此处的说明将这些用户分配到你的 Workplace by Facebook 应用：

*   建议将单个 Azure AD 用户分配到 Workplace by Facebook 以测试预配配置。 其他用户可以稍后分配。

*   将用户分配到 Workplace by Facebook 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Workplace by Facebook

从 Azure AD 应用程序库添加 Workplace by Facebook，开始管理 Workplace by Facebook 的预配。 如果以前为 SSO 设置过 Workplace by Facebook，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户分配到应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户分配到 Workplace by Facebook 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，请先在少量用户中进行测试。 如果预配范围设置为分配的用户，则可以先尝试将一两个用户分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="step-5-configure-automatic-user-provisioning-to-workplace-by-facebook"></a>步骤 5。 为 Workplace by Facebook 配置自动用户预配
本部分介绍如何配置 Azure AD 预配服务，以基于 Azure AD 中的用户分配在 Workplace by Facebook 应用中创建、更新和禁用用户。

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Workplace by Facebook”。

    ![应用程序列表中的 Workplace by Facebook 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，单击“授权”。 会重定向到 Workplace by Facebook 的授权页。 输入 Workplace by Facebook 用户名，并单击“继续”按钮。 单击“测试连接”以确保 Azure AD 可以连接到 Workplace by Facebook。 如果连接失败，请确保 Workplace by Facebook 帐户具有管理员权限并重试。

    ![屏幕截图显示具有“授权”选项的“管理员凭据”对话框。](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![授权](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Workplace by Facebook” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Workplace by Facebook 的用户属性。 选为“匹配”属性的属性将用于匹配 Workplace by Facebook 中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](../app-provisioning/customize-application-attributes.md)，则需要确保 Workplace by Facebook API 支持基于该特性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |displayName|字符串|
   |活动|Boolean|
   |title|布尔|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |addresses[type eq "work"].formatted|字符串|
   |addresses[type eq "work"].streetAddress|字符串|
   |addresses[type eq "work"].locality|字符串|
   |addresses[type eq "work"].region|字符串|
   |addresses[type eq "work"].country|字符串|
   |addresses[type eq "work"].postalCode|字符串|
   |addresses[type eq "other"].formatted|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "fax"].value|字符串|
   |externalId|字符串|
   |preferredLanguage|字符串|
   |urn:scim:schemas:extension:enterprise:1.0.manager|字符串|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extension:facebook:auth_method:1.0:auth_method|字符串|
   |urn:scim:schemas:extension:facebook:frontline:1.0.is_frontline|布尔|
   |urn:scim:schemas:extension:facebook:starttermdates:1.0.startDate|Integer|


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Workplace by Facebook 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。  

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Workplace by Facebook 的用户。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="troubleshooting-tips"></a>故障排除提示
*  如果看到未成功创建用户，并且存在代码为“1789003”的审核日志事件，则表示用户来自未经验证的域。
*  在某些情况下，用户会收到错误“错误: 缺少电子邮件字段: 必须提供 Facebook 返回的电子邮件错误: 处理 HTTP 请求时发生异常。 有关详细信息，请参阅此异常情况的“Response”属性返回的 HTTP 响应。 此操作已重试 0 次。 操作将在此日期后重试”。 此错误是因为客户将 mail（而不是 userPrincipalName）映射到 Facebook 电子邮件，但某些用户不具有 mail 属性。 要避免这些错误，并成功将失败的用户预配到 Facebook 中的工作区，请将映射到该工作区的属性从 Facebook 电子邮件属性修改到 Coalesce ([mail],[userPrincipalName]) 或取消从 Facebook 的工作区中分配用户，或预配用户的电子邮件地址。  


## <a name="change-log"></a>更改日志

* 2020/09/10 - 添加了对企业属性“division”、“organization”、“costCenter”和“employeeNumber”的支持。 添加了对自定义属性“startDate”、“auth_method”和“frontline”的支持
* 2021/07/22 - 更新了向客户提供的故障排除提示，即将 mail 映射到 Facebook 邮件，但某些用户不具有 mail 属性

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
