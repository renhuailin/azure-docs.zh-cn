---
title: 教程：使用 Azure Active Directory 为 Snowflake 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Snowflake 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: thwimmer
ms.openlocfilehash: c7eced7fb6c073eece1edbee93da0d9f33e3ed27
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690287"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>教程：为 Snowflake 配置自动用户预配

本教程演示要将 Azure AD 配置为自动将用户和组预配和到 [Snowflake](https://www.Snowflake.com/pricing/) 以及解除其预配，需在 Snowflake 和 Azure Active Directory (Azure AD) 中执行的步骤。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[什么是 Azure AD 中的自动 SaaS 应用用户预配？](../app-provisioning/user-provisioning.md)。 

> [!NOTE]
> 此连接器目前提供公共预览版。 有关使用条款的信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Snowflake 中创建用户
> * 在用户不再有访问需求的情况下，在 Snowflake 中删除用户
> * 使用户属性在 Azure AD 和 Snowflake 之间保持同步
> * 在 Snowflake 中预配组和组成员身份
> * 允许[单一登录](./snowflake-tutorial.md)到 Snowflake（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）
* [Snowflake 租户](https://www.Snowflake.com/pricing/)
* 在 Snowflake 中具有管理员权限的用户帐户

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1：规划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定[在 Azure AD 与 Snowflake 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>步骤 2：配置 Snowflake 以支持通过 Azure AD 进行预配

通过 Azure AD 为 Snowflake 配置自动用户预配之前，需要在 Snowflake 上启用跨域身份管理系统 (SCIM) 预配。

1. 登录到 Snowflake 管理控制台。 在突出显示的工作表中输入以下查询，然后选择“运行”。

   ![带有“查询”和“运行”按钮的 Snowflake 管理控制台的屏幕截图。](media/Snowflake-provisioning-tutorial/image00.png)
    
   ```
   use role accountadmin;
   
   create or replace role aad_provisioner;
   grant create user on account to aad_provisioner;
   grant create role on account to aad_provisioner;
   grant role aad_provisioner to role accountadmin;
   create or replace security integration aad_provisioning type=scim scim_client=azure run_as_role='AAD_PROVISIONER';
   
   select SYSTEM$GENERATE_SCIM_ACCESS_TOKEN('AAD_PROVISIONING');
   ```

1.  随即将为你的 Snowflake 租户生成 SCIM 访问令牌。 若要检索它，请选择下面的屏幕截图中突出显示的链接。

   ![Snowflake UI 中工作表的屏幕截图，其中突出显示了 SCIM 访问令牌。](media/Snowflake-provisioning-tutorial/image01.png)

1. 复制生成的令牌值并选择“完成”。 将在 Azure 门户中 Snowflake 应用程序“预配”选项卡的“机密令牌”框输入此值 。

   ![“详细信息”部分的屏幕截图，显示了复制到文本字段中的令牌并突出显示了“完成”选项。](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>步骤 3：从 Azure AD 应用程序库中添加 Snowflake

从 Azure AD 应用程序库中添加 Snowflake，开始管理 Snowflake 的预配。 如果以前为 Snowflake 设置过单一登录 (SSO)，则可以使用同一应用程序。 但是，我们建议你在最初测试集成时创建一个单独的应用。 [详细了解如何从库中添加应用程序](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4：定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配，或用户或组的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤将用户和组分配到应用程序](../manage-apps/assign-user-or-group-access-portal.md)。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以[使用范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

请记住以下提示：

* 将用户和组分配到 Snowflake 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户被排除在预配范围之外，并在预配日志中被标记为无有效资格。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>步骤 5：配置到 Snowflake 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以在 Snowflake 中创建、更新以及禁用用户和组。 可以根据 Azure AD 中的用户和组分配进行配置。

若要在 Azure AD 中为 Snowflake 配置自动用户预配，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序”   。

    ![显示“企业应用程序”窗格的屏幕截图。](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Snowflake”。

    ![显示应用程序列表的屏幕截图。](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，分别在“租户 URL”和“机密令牌”框中输入之前检索到的 SCIM 2.0 基 URL 和身份验证令牌  。 

   选择“测试连接”以确保 Azure AD 可以连接到 Snowflake。 如果连接失败，请确保 Snowflake 帐户具有管理员权限，然后重试。

    ![显示与“测试连接”按钮一起显示的“租户 URL”和“机密令牌”框的屏幕截图。](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址。 然后，选中“发生故障时发送电子邮件通知”复选框。

    ![显示通知电子邮件的框的屏幕截图。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Snowflake” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Snowflake 的用户属性。 选为“匹配”属性的特性用于匹配 Snowflake 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

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

10. 在“映射”部分中，选择“将 Azure Active Directory 组同步到 Snowflake” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Snowflake 的组属性。 选为“匹配”属性的特性用于匹配 Snowflake 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

    |Attribute|类型|
    |---|---|
    |displayName|字符串|
    |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

13. 若要为 Snowflake 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

     ![显示“预配状态”切换为“启用”的屏幕截图。](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Snowflake 的用户和组 。 

    如果此选项不可用，请在管理员凭据下配置必填字段，选择“保存”并刷新页面 。 

     ![显示预配范围选项的屏幕截图。](common/provisioning-scope.png)

15. 准备好预配时，选择“保存”。

     ![保存预配配置按钮的屏幕截图。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步 。 初始同步所需的时间比后续同步要长。 只要 Azure AD 预配服务正在运行，后续同步大约每 40 分钟就会进行一次。

## <a name="step-6-monitor-your-deployment"></a>步骤 6：监视部署
配置预配后，请使用以下资源来监视部署：

- 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
- 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)以查看预配周期的状态以及完成进度。
- 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 [了解有关隔离状态的详细信息](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="connector-limitations"></a>连接器限制

Snowflake 生成的 SCIM 令牌将在 6 个月后过期。 请注意，需要在这些令牌过期前对它们进行刷新，以便“预配同步”能够继续生效。 

## <a name="troubleshooting-tips"></a>故障排除提示

Azure AD 预配服务当前在特定 [IP 范围](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)下运行。 如有必要，你可以限制其他 IP 范围，并将这些特定的 IP 范围添加到你的应用程序的允许列表中。 该方法允许流量从 Azure AD 预配服务传递到应用程序。

## <a name="change-log"></a>更改日志

* 2020/07/21：为所有用户启用软删除（通过活动属性）。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
