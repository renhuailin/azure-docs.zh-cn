---
title: 教程：使用 Azure Active Directory 为 G Suite 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 G Suite 及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: thwimmer
ms.openlocfilehash: eb283ab73180bed43c90f57fd7ad09c045f82bc4
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113763819"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>教程：为 G Suite 配置自动用户预配

本教程介绍了在 G Suite 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [G Suite](https://gsuite.google.com/) 以及将其取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 G Suite 中创建用户
> * 在用户不再有访问需求的情况下，在 G Suite 中删除用户
> * 使用户属性在 Azure AD 和 G Suite 之间保持同步
> * 在 G Suite 中预配组和组成员身份
> * [单一登录](./google-apps-tutorial.md)到 G Suite（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* [G Suite 租户](https://gsuite.google.com/pricing.html)
* G Suite 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 G Suite 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 G Suite 以支持通过 Azure AD 进行预配

使用 Azure AD 为 G Suite 配置自动用户预配之前，需要在 G Suite 上启用 SCIM 预配。

1. 使用管理员帐户登录到 [G Suite 管理控制台](https://admin.google.com/)，然后选择“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。

    ![G Suite 安全](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. 在“安全”页上，选择“API 参考”。

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. 选择 **“启用 API 访问”**。

    ![已启用 G Suite API](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > 对于要预配到 G Suite 的每个用户，他们在 Azure AD 中的用户名必须绑定到自定义域。 例如，G Suite 不会接受 bob@contoso.onmicrosoft.com 之类的用户名， 但会接受 bob@contoso.com。 可以按照[此处](../fundamentals/add-custom-domain.md)的说明来更改现有用户的域。

4. 使用 Azure AD 添加并验证所需的自定义域后，必须使用 G Suite 再次验证它们。 若要在 G Suite 中验证域，请参阅以下步骤：

    a. 在 [G Suite 管理控制台](https://admin.google.com/)中，选择“域”。

    ![G Suite 域](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. 选择“添加域或域别名”。

    ![G Suite 添加域](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. 选择“添加另一个域”，然后键入要添加的域名。

    ![G Suite 添加另一个域](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. 选择“继续验证域所有权”。 然后按步骤验证所拥有的域名。 有关如何通过 Google 来验证域的完整说明，请参阅[验证站点所有权](https://support.google.com/webmasters/answer/35179)。

    e. 对所有要添加到 G Suite 的其他域重复上述步骤。

5. 接下来，确定要用于在 G Suite 中管理用户预配的管理员帐户。 导航到“管理员角色”。

    ![G Suite 管理员](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. 对于该帐户的“管理员角色”，编辑该角色的“特权”。 请确保启用该帐户的所有“管理员 API 权限”，使其可用于预配。

    ![G Suite 管理员权限](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库中添加 G Suite

从 Azure AD 应用程序库中添加 G Suite，开始管理到 G Suite 的预配。 如果以前为 G Suite 设置过 SSO，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 G Suite 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>步骤 5。 配置到 G Suite 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

> [!NOTE]
> 若要了解有关 G Suite 目录 API 终结点的详细信息，请参阅[目录 API](https://developers.google.com/admin-sdk/directory)。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>若要在 Azure AD 中为 G Suite 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。 用户需要登录到 portal.azure.com，并且将无法使用 aad.portal.azure.com

    ![“企业应用程序”边栏选项卡](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![“所有应用程序”边栏选项卡](./media/g-suite-provisioning-tutorial/all-applications.png)

2. 在应用程序列表中，选择“G Suite”。

    ![应用程序列表中的 G Suite 链接](common/all-applications.png)

3. 选择“预配”选项卡。单击“入门”。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

      ![“开始”边栏选项卡](./media/g-suite-provisioning-tutorial/get-started.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，单击“授权”。 你会在新的浏览器窗口中重定向到 Google 授权对话框。

      ![G Suite 授权](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. 确认想要授权 Azure AD 对你的 G Suite 租户进行更改。 选择“接受”。

     ![G Suite 租户身份验证](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. 在 Azure 门户中单击“测试连接”，确保 Azure AD 可以连接到 G Suite。 如果连接失败，请确保 G Suite 帐户具有管理员权限，然后重试。 然后再次重试“授权”步骤。

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“预配 Azure Active Directory 用户” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 G Suite 的用户属性。 选为“匹配”属性的特性用于匹配 G Suite 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 G Suite API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |primaryEmail|字符串|
   |relations.[type eq "manager"].value|字符串|
   |name.familyName|字符串|
   |name.givenName|字符串|
   |已挂起|字符串|
   |externalIds.[type eq "custom"].value|字符串|
   |externalIds.[type eq "organization"].value|字符串|
   |addresses.[type eq "work"].country|字符串|
   |addresses.[type eq "work"].streetAddress|字符串|
   |addresses.[type eq "work"].region|字符串|
   |addresses.[type eq "work"].locality|字符串|
   |addresses.[type eq "work"].postalCode|字符串|
   |emails.[type eq "work"].address|字符串|
   |organizations.[type eq "work"].department|字符串|
   |organizations.[type eq "work"].title|字符串|
   |phoneNumbers.[type eq "work"].value|字符串|
   |phoneNumbers.[type eq "mobile"].value|字符串|
   |phoneNumbers.[type eq "work_fax"].value|字符串|
   |emails.[type eq "work"].address|字符串|
   |organizations.[type eq "work"].department|字符串|
   |organizations.[type eq "work"].title|字符串|
   |phoneNumbers.[type eq "work"].value|字符串|
   |phoneNumbers.[type eq "mobile"].value|字符串|
   |phoneNumbers.[type eq "work_fax"].value|字符串|
   |addresses.[type eq "home"].country|字符串|
   |addresses.[type eq "home"].formatted|字符串|
   |addresses.[type eq "home"].locality|字符串|
   |addresses.[type eq "home"].postalCode|字符串|
   |addresses.[type eq "home"].region|字符串|
   |addresses.[type eq "home"].streetAddress|字符串|
   |addresses.[type eq "other"].country|字符串|
   |addresses.[type eq "other"].formatted|字符串|
   |addresses.[type eq "other"].locality|字符串|
   |addresses.[type eq "other"].postalCode|字符串|
   |addresses.[type eq "other"].region|字符串|
   |addresses.[type eq "other"].streetAddress|字符串|
   |addresses.[type eq "work"].formatted|字符串|
   |changePasswordAtNextLogin|字符串|
   |emails.[type eq "home"].address|字符串|
   |emails.[type eq "other"].address|字符串|
   |externalIds.[type eq "account"].value|字符串|
   |externalIds.[type eq "custom"].customType|字符串|
   |externalIds.[type eq "customer"].value|字符串|
   |externalIds.[type eq "login_id"].value|字符串|
   |externalIds.[type eq "network"].value|字符串|
   |gender.type|字符串|
   |GeneratedImmutableId|字符串|
   |标识符|字符串|
   |ims.[type eq "home"].protocol|字符串|
   |ims.[type eq "other"].protocol|字符串|
   |ims.[type eq "work"].protocol|字符串|
   |includeInGlobalAddressList|字符串|
   |ipWhitelisted|字符串|
   |organizations.[type eq "school"].costCenter|字符串|
   |organizations.[type eq "school"].department|字符串|
   |organizations.[type eq "school"].domain|字符串|
   |organizations.[type eq "school"].fullTimeEquivalent|字符串|
   |organizations.[type eq "school"].location|字符串|
   |organizations.[type eq "school"].name|字符串|
   |organizations.[type eq "school"].symbol|字符串|
   |organizations.[type eq "school"].title|字符串|
   |organizations.[type eq "work"].costCenter|字符串|
   |organizations.[type eq "work"].domain|字符串|
   |organizations.[type eq "work"].fullTimeEquivalent|字符串|
   |organizations.[type eq "work"].location|字符串|
   |organizations.[type eq "work"].name|字符串|
   |organizations.[type eq "work"].symbol|字符串|
   |OrgUnitPath|字符串|
   |phoneNumbers.[type eq "home"].value|字符串|
   |phoneNumbers.[type eq "other"].value|字符串|
   |websites.[type eq "home"].value|字符串|
   |websites.[type eq "other"].value|字符串|
   |websites.[type eq "work"].value|字符串|
   

10. 在“映射”部分下，选择“预配 Azure Active Directory 组” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 G Suite 的组属性。 选为“匹配”属性的特性用于匹配 G Suite 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |电子邮件|字符串|
      |成员|String|
      |name|字符串|
      |description|字符串|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 G Suite 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用” 。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 G Suite 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。

> [!NOTE]
> 如果用户已有使用 Azure AD 用户电子邮件地址的现有个人/使用者帐户，则可能会导致每个问题，可以在执行目录同步之前使用 Google 传输工具解决该问题。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="change-log"></a>更改日志

* 2020/10/17 - 添加了对其他 G Suite 用户和组属性的支持。
* 2020/10/17 - 更新了 G Suite 目标属性名称，使其匹配[此处](https://developers.google.com/admin-sdk/directory)定义的内容。
* 2020/10/17 - 更新了默认属性映射。
* 2021/03/18 - 对于所有新用户，管理员电子邮件（而不是 ID）现已同步。 对于已使用 manager 作为 ID 进行预配的任何现有用户，可以通过范围为“full”的 [Microsoft Graph](/graph/api/synchronization-synchronizationjob-restart?preserve-view=true&tabs=http&view=graph-rest-beta) 执行重启，以确保预配电子邮件。 此更改只会影响 GSuite 预配作业，而不会影响以 Goov2OutDelta 开头的较早的预配作业。 请注意，在首次创建用户时，或者管理员发生更改时，将预配管理员电子邮件。 如果管理员更改了其电子邮件地址，则不会预配管理员电子邮件。 

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)