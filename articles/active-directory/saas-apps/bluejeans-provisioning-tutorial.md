---
title: 教程：使用 Azure Active Directory 为 BlueJeans 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 BlueJeans 和取消其预配。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fc1518b169f3da8b34e97c999cd23e201aea59ce
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015670"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>教程：为 BlueJeans 配置自动用户预配

本教程介绍了在 BlueJeans 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户预配到 [BlueJeans](https://www.bluejeans.com) 并自动取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 BlueJeans 中创建用户
> * 在用户不再有访问需求的情况下，在 BlueJeans 中删除用户
> * 使用户属性在 Azure AD 和 BlueJeans 之间保持同步
> * [单一登录](./bluejeans-tutorial.md)到 BlueJeans（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 启用了[我的公司](https://www.bluejeans.com)计划或更佳计划的 BlueJeans 租户。
* BlueJeans 中具有管理员权限的用户帐户。
* BlueJeans Enterprise 中已启用 SCIM 预配。

> [!NOTE]
> Azure AD 预配集成依赖于可供 BlueJeans 团队在标准计划或更佳计划中使用的 [BlueJeans API](https://BlueJeans.github.io/developer)。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)内。
3. 确定要[在 Azure AD 与 BlueJeans 之间映射](../app-provisioning/customize-application-attributes.md)的数据。

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 BlueJeans 以支持通过 Azure AD 进行预配

1. 登录到 BlueJeans 管理控制台。 导航到“组设置”>“安全”。
2. 选择“单一登录”和“立即配置” 。

    ![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. 在“预配和集成”窗口中，选择“通过 IDP 创建和管理用户帐户”，然后单击“生成令牌”  。

    ![generate](./media/bluejeans-provisioning-tutorial/token.png)
    
4. 复制并保存该令牌。 
5. BlueJeans 租户 URL 是 `https://api.bluejeans.com/v2/scim`。 前一步骤中的“租户 URL”和“机密令牌”将输入到 Azure 门户中 BlueJeans 应用程序的“预配”选项卡中 。

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 BlueJeans

从 Azure AD 应用程序库添加 BlueJeans，开始管理 BlueJeans 的预配。 如果以前为 BlueJeans 设置过 SSO，则可以使用同一应用程序。 不过，建议在最初测试集成时单独创建一个应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户分配到应用程序。 如果选择仅根据用户的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户分配到 BlueJeans 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户被排除在预配范围之外，并在预配日志中被标记为无有效资格。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，请先在少量用户中进行测试。 如果预配范围设置为分配的用户，则可以先尝试将一两个用户分配到应用。 当预配范围设置为所有用户时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>步骤 5。 对 BlueJeans 配置自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户分配在 TestApp 中创建、更新和禁用用户。

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>若要在 Azure AD 中为 BlueJeans 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“BlueJeans”。

    ![应用程序列表中的 BlueJeans 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，输入在步骤 2 中检索到的 BlueJeans 租户 URL 和机密令牌。 单击“测试连接”，确保 Azure AD 可连接到 BlueJeans。 如果连接失败，请确保 BlueJeans 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)


6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知” 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 BlueJeans”。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 BlueJeans 的用户属性。 选为“匹配”属性的特性用于匹配 BlueJeans 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 BlueJeans API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

|Attribute|类型|支持筛选|
|---|---|---|
|userName|字符串|&check;|
|活动|Boolean|
|title|字符串|
|emails[type eq "work"].value|字符串|
|name.givenName|字符串|
|name.familyName|字符串|
|phoneNumbers[type eq "work"].value|字符串|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 BlueJeans 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 BlueJeans 的用户 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户启动初始同步 。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="connector-limitations"></a>连接器限制

* Bluejeans 禁止使用超过 30 个字符的 userName。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
