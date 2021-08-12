---
title: 教程：使用 Azure Active Directory 为 Smartsheet 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Smartsheet 和解除其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 334859fea8035d3afe87de635058fd347ae62e2d
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759537"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教程：为 Smartsheet 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 [Smartsheet](https://www.smartsheet.com/pricing) 以及解除其预配，需在 Smartsheet 和 Azure Active Directory (Azure AD) 中执行的步骤。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Smartsheet 中创建用户
> * 在用户不再有访问需求的情况下，在 Smartsheet 中删除用户
> * 使用户属性在 Azure AD 和 Smartsheet 之间保持同步
> * 单一登录到 Smartsheet（推荐）

> [!NOTE]
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。
* [Smartsheet 租户](https://www.smartsheet.com/pricing)。
* Smartsheet Enterprise 或 Enterprise Premier 计划中具有系统管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要[在 Azure AD 与 Smartsheet 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Smartsheet 以支持通过 Azure AD 进行预配

使用 Azure AD 为 Smartsheet 配置自动用户预配之前，需要在 Smartsheet 上启用 SCIM 预配。

1. 在 [Smartsheet 门户](https://app.smartsheet.com/b/home)中以 SysAdmin 身份登录，然后导航到“帐户管理员”。

    ![Smartsheet 帐户管理员](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 转到“安全控制”>“用户自动预配”>“编辑”。

    ![Smartsheet 安全控制](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 为计划从 Azure AD 预配到 Smartsheet 的用户添加并验证电子邮件域。 选择“未启用”可确保所有预配操作仅源自 Azure AD，还可确保 Smartsheet 用户列表与 Azure AD 分配同步。

    ![Smartsheet 用户预配](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 验证完成后，必须激活该域。 

    ![Smartsheet 激活域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 通过导航到“应用和集成”，生成使用 Azure AD 配置自动用户预配所需的机密令牌。

    ![Smartsheet 管理员页的屏幕截图，其中标注了用户 avatar 和“应用和集成”选项。](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 选择“API 访问”。 单击“生成新的访问令牌”。

    ![“个人设置”对话框的屏幕截图，其中标注了“API 访问”和“生成新的访问令牌”选项。](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定义 API 访问令牌的名称。 单击“确定”  。

    ![“第 1 步(共 2 步):生成 API 访问令牌”的屏幕截图，其中标注了“确定”选项。](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 复制并保存 API 访问令牌，因为你只能看到它这一次。 Azure AD 中的“机密令牌”字段必须使用它。

    ![Smartsheet 令牌](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Smartsheet

从 Azure AD 应用程序库添加 Smartsheet，开始管理到 Smartsheet 的预配。 如果以前为 Smartsheet 设置过 SSO，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Smartsheet 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 为了确保 Smartsheet 和 Azure AD 之间用户角色分配的均等性，建议使用完整 Smartsheet 用户列表中填充的相同角色分配。 若要从 Smartsheet 中检索此用户列表，请导航到“帐户管理员”>“用户管理”>“更多操作”>“下载用户列表(csv)”。

* 若要访问应用中的某些功能，Smartsheet 要求用户拥有多个角色。 若要详细了解 Smartsheet 中的用户类型和权限，请转到[用户类型和权限](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)。

*  如果用户在 Smartsheet 中分配有多个角色，你必须确保在 Azure AD 中复制这些角色分配，以避免用户可能永久失去对 Smartsheet 对象的访问权限。 必须将 Smartsheet 中的每个唯一角色分配给 Azure AD 中的不同组。 然后，必须将用户添加到所需角色对应的每个组。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>步骤 5。 配置 Smartsheet 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务，以便根据 Azure AD 中的用户和/或组分配在 Smartsheet 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>在 Azure AD 中为 Smartsheet 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Smartsheet”。

    ![应用程序列表中的 Smartsheet 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，分别在“租户 URL”和“机密令牌”中输入之前从 Smartsheet 检索到的 SCIM 2.0 基 URL 和访问令牌值   。 单击“测试连接”，以确保 Azure AD 可以连接到 Smartsheet。 如果连接失败，请确保 Smartsheet 帐户具有 SysAdmin 权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Smartsheet” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Smartsheet 的用户属性。 选为“匹配”属性的特性用于匹配 Smartsheet 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |title|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "fax"].value|字符串|
   |emails[type eq "work"].value|字符串|
   |externalId|String|
   |角色|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|字符串|


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Smartsheet 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用” 。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Smartsheet 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="connector-limitations"></a>连接器限制

* Smartsheet 不支持软删除。 当用户的 active 属性设置为 False 时，Smartsheet 将永久删除该用户。

## <a name="change-log"></a>更改日志

* 2020/06/16 - 为用户添加了对企业扩展属性“成本中心”、“分区”、“经理”和“部门”的支持。
* 2021/02/10 - 为用户添加了对核心特性“emails[type eq "work"]”的支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)