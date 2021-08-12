---
title: 教程：使用 Azure Active Directory 为 SolarWinds Service Desk（以前称为 Samanage）配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 SolarWinds Service Desk（以前称为 Samanage）及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: thwimmer
ms.openlocfilehash: 3999575e13a1bb12478f6a9270dafa7c18c22f35
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759650"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>教程：为 SolarWinds Service Desk（以前称为 Samanage）配置自动用户预配

本教程介绍了在 SolarWinds Service Desk（以前称为 Samanage）和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [SolarWinds Service Desk](https://www.samanage.com/pricing/) 和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>迁移到新的 SolarWinds Service Desk 应用程序

如果具有与 SolarWinds Service Desk 的现有集成，请参阅以下有关即将推出的更改的部分。 如果是第一次设置 SolarWinds Service Desk，则可以跳过此部分并转到“支持的功能”。

#### <a name="whats-changing"></a>有什么变化？

* Azure AD 端的更改：在 Samange 中用于预配用户的授权方法一直以来都是基本身份验证。不久，你便会看到授权方法已更改为长期机密令牌。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>将现有自定义集成迁移到新应用程序需要执行哪些操作？

如果具有包含有效管理员凭据的现有 SolarWinds Service Desk 集成，则无需执行任何操作。 我们会自动将客户迁移到新应用程序。 此过程完全在幕后完成。 如果现有凭据过期，或者如果需要再次授权对应用程序的访问权限，则需要生成长期机密令牌。 若要生成新令牌，请参阅本文的步骤 2。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>如何判断应用程序是否已迁移？ 

你的应用程序迁移后，在“管理员凭据”部分中，“管理员用户名”和“管理员密码”字段会替换为单个“机密令牌”字段   。

## <a name="capabilities-supported"></a>支持的功能

> [!div class="checklist"]
> * 在 SolarWinds Service Desk 中创建用户
> * 在用户不再有访问需求的情况下，在 SolarWinds Service Desk 中删除用户
> * 使用户属性在 Azure AD 和 SolarWinds Service Desk 之间保持同步
> * 在 SolarWinds Service Desk 中预配组和组成员身份
> * [单一登录](./samanage-tutorial.md)到 SolarWinds Service Desk（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 具有专业版包的 [SolarWinds Service Desk 租户](https://www.samanage.com/pricing/)。
* SolarWinds Service Desk 中具有管理员权限的用户帐户。

> [!Note]
> 导入角色时，不应在 Azure Active Directory 中手动编辑角色。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 SolarWinds Service Desk 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 SolarWinds Service Desk 以支持通过 Azure AD 进行预配

若要生成用于身份验证的机密令牌，请参阅[用于 API 集成的令牌身份验证教程](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)。

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 SolarWinds Service Desk

从 Azure AD 应用程序库添加 SolarWinds Service Desk，开始管理到 SolarWinds Service Desk 的预配。 如果以前为 SSO 设置过 SolarWinds Service Desk，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 SolarWinds Service Desk 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>步骤 5。 对 SolarWinds Service Desk 配置自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>若要在 Azure AD 中为 SolarWinds Service Desk 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SolarWinds Service Desk”。

3. 选择“预配”  选项卡。

    ![显示选择了“预配”选项卡的屏幕截图。](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![显示“预配模式”设置为“自动”的屏幕截图。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://api.samanage.com` 。  在“机密令牌”中，输入之前检索到的机密令牌值。 选择“测试连接”以确保 Azure AD 可以连接到 SolarWinds Service Desk。 如果连接失败，请确保 SolarWinds Service Desk 帐户具有管理员权限，然后重试。

    ![显示选择了“测试连接”按钮的屏幕截图。](./media/samanage-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 SolarWinds Service Desk” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 SolarWinds Service Desk 的用户属性。 选为“匹配”属性的特性用于匹配 SolarWinds Service Desk 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 SolarWinds Service Desk API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改  。

      ![Samange 用户映射](./media/samanage-provisioning-tutorial/user-attributes.png)

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 SolarWinds Service Desk” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 SolarWinds Service Desk 的组属性。 选为“匹配”属性的特性用于匹配 SolarWinds Service Desk 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

      ![Samange 组映射](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 SolarWinds Service Desk 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 SolarWinds Service Desk 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 准备好预配时，选择“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问[此处](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="connector-limitations"></a>连接器限制

如果选择“同步所有用户和组”选项，并为 SolarWinds Service Desk“角色”属性配置值，则“默认值”框下的值必须采用以下格式进行表示  ：

- {"displayName":"role"}，其中 role 是所需的默认值。

## <a name="change-log"></a>更改日志

* 2020/09/14 - 对于 `https://github.com/ravitmorales` ，将两个 SaaS 教程中的公司名称从 Samanage 更改为 SolarWinds Service Desk（以前称为 Samanage）。
* 2020/04/22 - 将授权方法从基本身份验证更新为长期机密令牌。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
