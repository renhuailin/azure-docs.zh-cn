---
title: 教程：将 Adobe Identity Management 配置为自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何从 Azure AD 自动预配和取消预配用户帐户。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: Zhchia
ms.openlocfilehash: a87a08db672c459138fc1efd865332dc0f19944e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586584"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Adobe Identity Management

本教程介绍了需要在 Adobe 标识管理和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置时，Azure AD 会自动将用户和组预配和取消预配到使用 Azure AD 预配服务的 Adobe 标识管理。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Adobe Identity Management 中创建用户
> * 如果用户不需要访问权限，请删除 Adobe Identity Management 中的用户
> * 使用户属性在 Azure AD 和 Adobe Identity Management 之间保持同步
> * 在 Adobe Identity Management 中预配组和组成员身份
> * 单一登录到 Adobe Identity Management (推荐) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* [Adobe 管理控制台](https://adminconsole.adobe.com/)中包含已验证域的联合目录。

> [!NOTE]
> 如果你的组织使用用户同步工具或 UMAPI 集成，则必须先暂停集成。 然后，将 Azure AD 自动预配添加到 Azure 门户中的自动用户管理。 配置并运行 Azure AD 自动预配后，可以完全删除用户同步工具或 UMAPI 集成。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 规划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要 [在 Azure AD 和 Adobe Identity Management 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>步骤 2。 将 Adobe Identity Management 配置为支持预配 Azure AD

1. 登录到 [Adobe 管理控制台](https://adminconsole.adobe.com/)。 导航到 " **设置" > > 同步的目录详细信息**"。 

2. 单击 " **添加同步**"。

      ![添加](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. 选择 " **同步用户 Microsoft Azure** "，然后单击 " **下一步**"。

      ![显示选定的 "从 Microsoft Azure Active Directory 同步用户" 的屏幕截图。](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. 复制并保存 **租户 URL** 和 **机密令牌**。 这些值将输入到 " **租户 URL** " 和 " **机密令牌** " 字段中的 "设置" 选项卡上的 "Azure 门户中。

      ![同步](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Adobe Identity Management

从 "Azure AD" 应用程序库中添加 Adobe Identity Management，开始管理预配到 Adobe 标识管理。 如果你以前为 SSO 设置了 Adobe 标识管理，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 可在[此处](../manage-apps/add-application-portal.md)详细了解如何从库中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户/组的属性来限定谁在预配范围内。 如果选择根据分配来查看要将谁预配到应用，则可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Adobe Identity Management 时，必须选择 " **默认" 访问权限** 以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>步骤 5。 配置 Adobe Identity Management 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>若要为 Azure AD 中的 Adobe Identity Management 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Adobe Identity Management”。

    ![应用程序列表中的 "Adobe 标识管理" 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分下，输入你在步骤2之前检索到的 Adobe Identity MANAGEMENT 租户 URL 和机密令牌。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 Adobe Identity Management。 如果连接失败，请确保你的 Adobe 标识管理帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 Adobe 标识管理**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Adobe Identity Management 的用户属性。 选为 " **匹配** " 属性的属性用于匹配 Adobe Identity Management 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](../app-provisioning/customize-application-attributes.md)，将需要确保 Adobe 标识管理 API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |emails[type eq "work"].value|字符串|
   |活动|Boolean|
   |addresses[type eq "work"].country|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |urn： ietf： params： scim：架构：扩展： Adobe：2.0： User： emailAliases|字符串|

10. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 组同步到 Adobe 标识管理**"。

11. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Adobe Identity Management 的组属性。 选为 " **匹配** " 属性的属性用于匹配 Adobe Identity Management 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Adobe 标识管理启用 Azure AD 预配服务，请在 "设置" 部分中将 **设置****状态** 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Adobe 标识管理的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)