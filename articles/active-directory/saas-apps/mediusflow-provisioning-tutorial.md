---
title: 教程：使用 Azure Active Directory 为 MediusFlow 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure AD 中自动向 MediusFlow 预配和取消预配用户帐户。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: thwimmer
ms.openlocfilehash: ef4ffd0417cc1566282466e28ee82f1d27f3db3c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768687"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>教程：为 MediusFlow 配置自动用户预配

本教程介绍了在 MediusFlow 和 Azure Active Directory (Azure AD) 中配置自动用户预配所需执行的步骤。 配置后，Azure AD 就可以使用 Azure AD 预配服务自动向 [MediusFlow](https://www.mediusflow.com/) 预配和取消预配用户和组。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 MediusFlow 中创建用户
> * 在 MediusFlow 中删除不再需要访问的用户
> * 在 Azure AD 和 MediusFlow 之间同步用户特性
> * 在 MediusFlow 中预配组和组成员资格
> * 单一登录 MediusFlow（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 具有质量保证或生产租户的活动 MediusFlow 订阅。
* MediusFlow 中拥有管理员访问权限的用户帐户，可以在 MediusFlow 中执行配置。
* 在 MediusFlow 租户中添加的公司，应将用户预配到其中。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)内。
3. 确定[在 Azure AD 与 MediusFlow 之间映射](../app-provisioning/customize-application-attributes.md)哪些数据。 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>步骤 2. 将 MediusFlow 配置为支持通过 Azure AD 进行预配

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>在 MediusFlow 中激活 Microsoft 365 应用
首先，在 MediusFlow 中启用 Azure AD 登录名的访问权限和 Azure AD 配置功能，具体步骤如下：

#### <a name="user-login"></a>用户登录
若要启用对 Microsoft 365/Azure AD 的登录流，请参阅[这篇] (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) 文章。

#### <a name="user-transfer-configuration"></a>用户传输配置
若要启用用户的配置门户，以便通过 Azure AD 进行预配，请参阅[这篇](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup)文章。

#### <a name="configure-user-provisioning"></a>配置用户设置

1.  通过提供租户 ID 来登录 [MediusFlow 管理控制台](https://office365.cloudapp.mediusflow.com/)。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="MediusFlow 管理控制台的屏幕截图。在第一个集成步骤中，突出显示了“MediusFlow 租户名称”框和“验证”按钮。" border="false":::

2. 验证与 MediusFlow 的连接。

    ![Verify](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. 提供 Azure AD 租户 ID。

    ![提供租户 ID](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   若要了解如何找到它，可以详细阅读 [FAQ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id)。

4. 保存配置。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="显示第四个集成步骤的 MediusFlow 管理控制台的屏幕截图。突出显示了“保存配置”按钮。" border="false":::

5. 选中“使用用户预配”，然后单击“确定”。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="显示第五个集成步骤的 MediusFlow 管理控制台的屏幕截图。突出显示了“使用用户设置”和“确定”按钮。" border="false":::

6. 单击“生成密钥”。 复制并保存此值。将在 Azure 门户中 MediusFLow 应用的“预配”选项卡的“密钥令牌”字段中输入此值。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="MediusFlow 管理控制台中的“用户设置配置”选项卡的屏幕截图。突出显示了“生成密钥”和“复制”按钮。" border="false":::

7. 单击“确定”。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="MediusFlow 管理控制台的屏幕截图，其中显示一个通知，指示用户单击“确定”以生成新密钥。突出显示了“确定”按钮。" border="false":::

8. 若要在 MediusFlow 中导入带有一组预定义角色、公司和其他常规配置的用户，需要先对其进行配置。 首先，通过单击“添加新配置”来添加配置。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="MediusFlow 管理控制台中的“用户设置配置”选项卡的屏幕截图。突出显示了“添加新配置”按钮。" border="false":::

9. 提供用户的默认设置。 在此视图中，可以设置默认特性。 如果标准设置是正确的，那么只提供有效的公司名称就足够了。 由于这些配置设置是从 Mediusflow 中提取的，因此需要先对其进行配置。 有关详细信息，请参阅本文的“先决条件”部分。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="MediusFlow“添加新配置”窗口的屏幕截图。许多设置都可见，包括区域设置、筛选器和用户角色。" border="false":::

10. 单击“保存”，以保存用户配置。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="MediusFlow 管理控制台中的“用户设置配置”选项卡的屏幕截图。突出显示了“保存”按钮。" border="false":::

11. 若要获取用户预配链接，请单击“复制 SCIM 链接”。 复制并保存此值。 将在 Azure 门户中 MediusFLow 应用的“预配”选项卡的“租户 URL”字段中输入此值。
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="MediusFlow 管理控制台中的“用户设置配置”选项卡的屏幕截图。突出显示了“复制 SCIM 链接”按钮。" border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用库添加 MediusFlow

从 Azure AD 应用库添加 MediusFlow，以开始管理向 MediusFlow 预配用户。 如果之前已经为 MediusFlow 设置了 SSO，那么可以使用相同的应用。 不过，建议在最初测试集成时单独创建一个应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择只根据用户或组的特性来限定谁在预配范围内，可以使用范围筛选器，如[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述。 

* 将用户和组分配到 MediusFlow 时，必须选择除“默认访问”之外的角色。 具有“默认访问”角色的用户被排除在预配范围之外，并在预配日志中被标记为无有效资格。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，可以通过将一两个用户或组分配到应用来控制它。 如果预配范围设置为所有用户和组，可以指定[基于特性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>步骤 5。 配置自动向 MediusFlow 预配用户 

此部分逐步介绍了如何将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>若要在 Azure AD 中为 MediusFlow 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用列表中，选择“MediusFlow”。

    ![应用列表中的“MediusFlow”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入之前检索到的租户 URL 值。 在“密钥令牌”中，输入之前检索到的密钥令牌值。 单击“测试连接”，以确保 Azure AD 可以连接到 MediusFlow。 如果连接失败，请确保 MediusFlow 帐户拥有管理员权限，然后重试。

      ![屏幕截图显示“管理员凭据”对话框，可在该框中输入租户 URL 和机密令牌。](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选中“将 Azure Active Directory 用户同步到 MediusFlow”。

9. 在“特性映射”部分中，审阅从 Azure AD 同步到 MediusFlow 的用户特性。 选为“匹配”属性的特性是用于匹配 MediusFlow 中用于更新操作的用户帐户。 如果选择更改[匹配目标特性](../app-provisioning/customize-application-attributes.md)，则需要确保 MediusFlow API 支持根据相应特性来筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |emails[type eq "work"].value|字符串|
   |name.displayName|字符串|
   |活动|Boolean|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:configurationFilter|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:identityProvider|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:nameIdentifier|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText1|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText2|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText3|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText4|字符串|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText5|字符串|


10. 在“映射”部分下，选中“将 Azure Active Directory 组同步到 MediusFlow”。

11. 在“特性映射”部分中，审阅从 Azure AD 同步到 MediusFlow 的组特性。 选为“匹配”属性的特性是用于匹配 MediusFlow 中用于更新操作的组。 选择“保存”按钮以提交任何更改。

    | Attribute | 类型 |
    |--|--|
    | displayName | 字符串 |
    | externalID | 字符串 |
    | members | 参考 |

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 MediusFlow 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“开”。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要向 MediusFlow 预配的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="change-log"></a>更改日志

* 2021/01/21 - 已添加自定义扩展属性 configurationFilter、identityProvider、nameIdentifier、customFieldText1、customFieldText2、customFieldText3、customFieldText3 和 customFieldText5       。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)