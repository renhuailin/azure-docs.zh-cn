---
title: 教程：使用 Azure Active Directory 为 15Five 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 来使其自动将用户帐户预配到 15Five 和取消其预配。
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
ms.openlocfilehash: e98254654bd3286cc86831941c707949a687494e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765238"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>教程：为 15Five 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 15Five 以及解除其预配需在 [15Five](https://www.15five.com/pricing/) 和 Azure Active Directory (Azure AD) 中执行的步骤。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配。

> [!NOTE]
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 15Five 中创建用户
> * 在用户不再有访问需求的情况下，在 15Five 中删除用户
> * 使用户属性在 Azure AD 和 15Five 之间保持同步
> * 在 15Five 中预配组和组成员身份
> * [单一登录](./15five-tutorial.md)到 15Five（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* [15Five 租户](https://www.15five.com/pricing/)。
* 15Five 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 15Five 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 15Five 以支持通过 Azure AD 进行预配

使用 Azure AD 为 15Five 配置自动用户预配之前，需要在 15Five 上启用 SCIM 预配。

1. 登录到 [15Five 管理控制台](https://my.15five.com/)。 导航到“功能”>“集成”。

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="15Five 管理控制台的屏幕截图。“集成”显示在菜单中的“功能”下，并且“功能”和“集成”均突出显示。" border="false":::

2.  单击“SCIM 2.0”。

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="15Five 管理控制台中“集成”页的屏幕截图。在“工具”下，突出显示 S C I M 2.0。" border="false":::

3.  导航到“SCIM 集成”>“生成 OAuth 标记”。

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="15Five 管理控制台中 S C I M 集成页的屏幕截图。突出显示了“生成 OAuth 标记”。" border="false":::

4.  复制“SCIM 2.0 基 URL”和“访问令牌”的值 。 将在 Azure 门户中 15Five 应用程序“预配”选项卡的“机密令牌”字段输入此值 。
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="S C I M 集成页的屏幕截图。在令牌表中，突出显示了“S C I M 2.0 基 URL”和“访问令牌”旁的值。" border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 15Five

从 Azure AD 应用程序库添加 15Five，开始管理 15Five 的预配。 如果之前为 15Five 设置过 SSO，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 15Five 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>步骤 5。 配置到 15Five 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 15Five 中创建、更新以及禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>要在 Azure AD 中为 15Five 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“15Five”。

    ![应用程序列表中的 15Five 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5.  在“管理员凭据”部分中，分别在“租户 URL”和“机密令牌”字段中输入之前检索到的 SCIM 2.0 基 URL 和访问令牌值。   单击“测试连接”，确保 Azure AD 可连接到 15Five。 如果连接失败，请确保 15Five 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 15Five” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 15Five 的用户属性。 选为“匹配”属性的特性用于匹配 15Five 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。


   |Attribute|类型|
   |---|---|
   |活动|Boolean|
   |title|字符串|
   |emails[type eq "work"].value|字符串|
   |userName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:location|字符串|
   |urn:ietf:params:scim:schemas:extension:15Five:2.0:User:startDate|字符串|

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 15Five” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 15Five 的组属性。 选为“匹配”属性的特性用于匹配 15Five 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

      |Attribute|类型|
      |---|---|
      |externalId|字符串|
      |displayName|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 15Five 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 15Five 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

    此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。  
    
## <a name="connector-limitations"></a>连接器限制

* 15Five 不支持用户的软删除。

## <a name="change-log"></a>更改日志

* 2020/06/16 - 为用户添加了针对企业扩展属性“经理”以及自定义属性“位置”和“开始日期”的支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报表](../app-provisioning/check-status-user-account-provisioning.md)。