---
title: 教程：使用 Azure Active Directory 为 Zoom 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Zoom 以及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: thwimmer
ms.openlocfilehash: 393b93d3d5e2a8d9088fe8ec759971fd096eb76c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760504"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>教程：为 Zoom 配置自动用户预配

本教程介绍了在 Zoom 和 Azure Active Directory (Azure AD) 中配置自动用户预配时需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Zoom](https://zoom.us/pricing/) 以及取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Zoom 中创建用户
> * 在用户不再有访问需求的情况下，在 Zoom 中删除用户
> * 使用户特性在 Azure AD 和 Zoom 之间保持同步
> * [单一登录](./zoom-tutorial.md)到 Zoom（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* [Zoom 租户](https://zoom.us/pricing)。
* Zoom 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Zoom 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Zoom 以支持通过 Azure AD 进行预配

1. 登录到 [Zoom 管理控制台](https://zoom.us/signin)。 导航到左侧导航窗格中的“高级”>“应用市场”。

    ![Zoom 集成](media/zoom-provisioning-tutorial/zoom01.png)

2. 导航到页面右上角的“管理”。 

    ![Zoom 应用市场的屏幕截图，其中突出显示了“管理”选项。](media/zoom-provisioning-tutorial/zoom02.png)

3. 导航到创建的 Azure AD 应用。 
    
    ![“已创建的应用”部分的屏幕截图，其中突出显示了 Azure AD 应用。](media/zoom-provisioning-tutorial/zoom03.png)

4. 在左侧导航窗格中，选择“应用凭据”。

    ![左侧导航窗格的屏幕截图，其中突出显示了“应用凭据”选项。](media/zoom-provisioning-tutorial/zoom04.png)

5. 复制并保存“JWT 令牌”。 将在 Azure 门户中 Zoom 应用程序的“预配”选项卡的“机密令牌”字段中输入此值。 如果需要新的不会过期的令牌，将需要重新配置过期时间，此操作会自动生成新令牌。 

    ![“应用凭据”页的屏幕截图。](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Zoom

从 Azure AD 应用程序库添加 Zoom，开始管理 Zoom 的预配。 如果之前为 Zoom 设置过 SSO，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Zoom 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>步骤 5。 配置 Zoom 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>在 Azure AD 中为 Zoom 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zoom”。

    ![应用程序列表中的 Zoom 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://api.zoom.us/scim` 。 在“机密令牌”中，输入之前检索到的 JWT 令牌值 。 单击“测试连接”以确保 Azure AD 可以连接到 Zoom。 如果连接失败，请确保 Zoom 帐户具有管理员权限，然后重试。

    ![Zoom 预配](./media/zoom-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Zoom” 。

9. 在“特性映射”部分中，查看从 Azure AD 同步到 Zoom 的用户特性。 选为“匹配”属性的特性用于匹配 Zoom 中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](../app-provisioning/customize-application-attributes.md)，则需要确保 Zoom API 支持基于该特性筛选用户。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |活动|Boolean|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |emails[type eq "work"]|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为 Zoom 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Zoom 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="connector-limitations"></a>连接器限制
* Zoom 现在最多允许 9,999 名基本用户。

## <a name="change-log"></a>更改日志
* 2020/05/14 - 为 emails[type eq "work"] 特性添加了对 UPDATE 操作的支持。
* 2020/10/20 - 添加了对“已授权”和“本地”这两个新角色的支持，用其替换了现有的角色“专业”和“企业”。 未来将删除对“专业”和“企业”角色的支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)