---
title: 教程：使用 Azure Active Directory 为 Snowflake 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Snowflake 和取消其预配。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ac6981d998f25b8df7cea553fe152d68cd43fee5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181413"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>教程：为 Snowflake 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 [Snowflake](https://www.Snowflake.com/pricing/) 以及取消其预配需在 Snowflake 和 Azure Active Directory (Azure AD) 中执行的步骤。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


> [!NOTE]
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Snowflake 中创建用户
> * 在用户不再有访问需求的情况下，在 Snowflake 中删除用户
> * 使用户属性在 Azure AD 和 Snowflake 之间保持同步
> * 在 Snowflake 中预配组和组成员身份
> * [单一登录](./snowflake-tutorial.md)到 Snowflake（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* [Snowflake 租户](https://www.Snowflake.com/pricing/)。
* 在 Snowflake 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Snowflake 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Snowflake 以支持通过 Azure AD 进行预配

使用 Azure AD 为 Snowflake 配置自动用户预配之前，需要在 Snowflake 上启用 SCIM 预配。

1. 登录到 Snowflake 管理控制台。 在突出显示的工作表中输入如下所示的查询，然后单击“运行”。

    ![Snowflake 管理控制台](media/Snowflake-provisioning-tutorial/image00.png)

2.  将为你的 Snowflake 租户生成 SCIM 访问令牌。 若要检索它，请单击下面突出显示的链接。

    ![Snowflake U I 中工作表的屏幕截图，其中调出了 S C I M 访问令牌。](media/Snowflake-provisioning-tutorial/image01.png)

3. 请复制生成的令牌值并单击“完成”。 将在 Azure 门户中 Snowflake 应用程序“预配”选项卡的“机密令牌”字段输入此值。

    ![“详细信息”部分的屏幕截图，显示了复制到文本字段中的令牌和调出的“完成”选项。](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库中添加 Snowflake

从 Azure AD 应用程序库中添加 Snowflake，开始管理 Snowflake 的预配。 如果之前为 Snowflake 设置过 SSO，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](../manage-apps/add-application-portal.md)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Snowflake 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>步骤 5。 配置到 Snowflake 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Snowflake 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>若要在 Azure AD 中为 Snowflake 配置自动用户预配，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Snowflake”。

    ![应用程序列表中的 Snowflake 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，分别在“租户 URL”和“机密令牌”字段中输入之前检索到的 SCIM 2.0 基 URL 和身份验证令牌值  。 单击“测试连接”以确保 Azure AD 可以连接到 Snowflake。 如果连接失败，请确保 Snowflake 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Snowflake” 。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 Snowflake 的用户属性。 选为“匹配”属性的特性用于匹配 Snowflake 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|
   |---|---|
   |活动|Boolean|
   |displayName|字符串|
   |emails[type eq "work"].value|字符串|
   |userName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|字符串|

11. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Snowflake” 。

12. 在“属性映射”部分中，查看从 Azure AD 同步到 Snowflake 的组属性。 选为“匹配”属性的特性用于匹配 Snowflake 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |members|参考|

13. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

14. 若要为 Snowflake 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

15. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Snowflake 的用户和/或组 。 如果此选项不可用，请在管理员凭据下配置必填字段，单击“保存”并刷新页面。 

    ![预配范围](common/provisioning-scope.png)

16. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

    此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="connector-limitations"></a>连接器限制

* Snowflake 生成的 SCIM 令牌将在 6 个月后过期。 请注意，需要在这些令牌过期前对它们进行刷新，以便“预配同步”能够继续生效。 

## <a name="change-log"></a>更改日志

* 2020/07/21 - 为所有用户启用软删除（通过活动属性）。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤
* [了解如何查看日志并获取有关预配活动的报表](../app-provisioning/check-status-user-account-provisioning.md)。