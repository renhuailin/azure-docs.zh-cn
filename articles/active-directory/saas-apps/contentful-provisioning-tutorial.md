---
title: 教程：使用 Azure Active Directory 为 Contentful 配置自动用户预配
description: 了解如何将用户帐户从 Azure Active Directory (Azure AD) 自动预配到 Contentful 以及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: thwimmer
ms.openlocfilehash: 81801acb9fe7498c124dedb9bb2d5263d4f53711
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764009"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>教程：为 Contentful 配置自动用户预配

本文介绍了在 Contentful 和 Azure Active Directory (Azure AD) 中配置自动用户预配需完成的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Contentful](https://www.contentful.com/) 以及将其取消预配。 有关此服务的功能、工作原理的重要详细信息以及常见问题解答，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动化用户预配和取消预配](../app-provisioning/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支持的功能

> [!div class="checklist"]
> * 在 Contentful 中创建用户
> * 在用户不再有访问需求的情况下，在 Contentful 中删除用户
> * 使用户特性在 Azure AD 和 Contentful 之间保持同步
> * 在 Contentful 中预配组和组成员身份
> * [单一登录](contentful-tutorial.md)到 Contentful（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* 具有支持跨域身份管理系统 (SCIM) 预配的订阅的Contentful 组织帐户。 如果你对组织的订阅有任何疑问，请联系 [Contentful 支持](mailto:support@contentful.com)。
 
## <a name="plan-your-provisioning-deployment"></a>计划预配部署

1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Contentful 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>配置 Contentful 以支持通过 Azure AD 进行预配

1. 在 Contentful 中，创建 **服务用户** 帐户。 Azure 的所有预配权限都通过此帐户提供。 建议选择“所有者”作为此帐户的组织角色。

2. 以“服务用户”的身份登录到 Contentful。

3. 在左侧菜单中，选择“组织设置” > “访问工具” > “用户预配”。  

   ![Contentful 中的组织设置菜单的屏幕截图，其中突出显示了访问工具下的用户预配。](media/contentful-provisioning-tutorial/access.png)

4. 复制并保存“SCIM URL”。 将在 Azure 门户中 Contentful 应用程序的“预配”选项卡上输入此值。

5. 选择“生成个人访问令牌”。

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. 在模式窗口中，输入个人访问令牌的名称，然后选择“生成”。

7. 生成 SCIM URL 和机密令牌。 复制并保存这些值。 将在 Azure 门户中 Contentful 应用程序的“预配”选项卡上输入这些值。

    ![个人访问令牌窗格的屏幕截图，其中突出显示了 C F P A T 和令牌占位符名称。](media/contentful-provisioning-tutorial/token.png)


如果在 Contentful 管理控制台中配置预配时遇到问题，请联系 [Contentful 支持](mailto:support@contentful.com)。

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>从 Azure AD 应用程序库添加 Contentful

若要管理 Contentful 的预配，请从 Azure AD 应用程序库添加 Contentful。 如果以前为 Contentful 设置过单一登录，则可以使用同一应用程序。 但是，我们建议在最初测试集成时创建一个单独的应用。 了解如何[在库中添加应用程序](../manage-apps/add-application-portal.md)。 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>定义谁在预配范围中 

可以使用 Azure AD 预配服务根据对应用程序的分配或用户或组的属性来限定谁在预配范围内。 

如果选择根据分配来限定要将谁预配到应用，完成以下步骤[将用户和组分配到应用程序](../manage-apps/assign-user-or-group-access-portal.md)。

如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用范围筛选器来[定义预配用户帐户的条件规则](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

* 将用户和组分配到 Contentful 时，必须选择“默认访问”以外的角色。 具有默认访问角色的用户将从预配中排除，并在预配日志中被指示为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。 
* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以通过将一两个用户或组分配到应用来控制范围。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>配置 Contentful 的自动用户预配 

本部分介绍了如何设置 Azure AD 预配服务以基于 Azure AD 中的用户和组分配在测试应用中创建、更新和禁用用户或组。

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>在 Azure AD 中为 Contentful 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序”。 

   ![显示 Azure 门户中的企业应用程序菜单的屏幕截图，其中突出显示了所有应用程序。](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Contentful”。

   ![显示应用程序列表中返回的前 20 个结果的屏幕截图。](common/all-applications.png)

3. 选择“预配”  选项卡。

   ![左侧菜单的管理部分中突出显示的预配选项卡的屏幕截图。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

   ![显示预配模式选项的屏幕截图，并突出显示了自动。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，输入 Contentful 租户 URL 和机密令牌。 选择“测试连接”以确保 Azure AD 可以连接到 Contentful。 如果连接失败，请确保 Contentful 帐户具有管理员权限，然后重试。

   ![显示租户 U R L 和机密令牌文本框的屏幕截图，其中突出显示了测试连接按钮。](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中“发生故障时发送电子邮件通知”复选框 。

   ![显示通知电子邮件文本框的屏幕截图。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Contentful” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Contentful 的用户属性。 选为“匹配”属性的特性用于匹配 Contentful 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，必须确保 Contentful API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |name.givenName|字符串|
   |name.familyName|字符串|

10. 在“映射”部分中，选择“将 Azure Active Directory 组同步到 Contentful” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Contentful 的组属性。 选为“匹配”属性的特性用于匹配 Contentful 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    |Attribute|类型|支持筛选|
    |---|---|---|
    |displayName|字符串|&check;|
    |members|参考|

12. 若要设置范围筛选器，请完成[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中所述的步骤。

13. 要为 Contentful 启用 Azure AD 预配服务，请在“设置”部分的“预配状态”中，选择“开”  。

    ![显示预配状态开关切换的屏幕截图。](common/provisioning-toggle-on.png)

14. 若要定义要预配到 Contentful 的用户或组，请在“设置”部分的“范围”中，选择相关选项。 

    ![显示可在范围窗格中选择的选项的屏幕截图。](common/provisioning-scope.png)

15. 准备好预配时，选择“保存”。

    ![显示保存按钮和取消按钮的屏幕截图。](common/provisioning-configuration-save.png)

此操作会对“设置”下的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="monitor-your-deployment"></a>监视部署

配置预配后，请使用以下资源来监视部署：

* 若要确定哪些用户已预配成功或失败，请查看[预配日志](../reports-monitoring/concept-provisioning-logs.md)。
* 若要查看预配周期的状态以及完成进度，请检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 了解有关[隔离状态](../app-provisioning/application-provisioning-quarantine-status.md)的详细信息。  

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
