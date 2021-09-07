---
title: 教程：使用 Azure Active Directory 为 BLDNG APP 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 BLDNG APP 及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 5ccc1176-c244-4003-8486-67586bcdf317
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: thwimmer
ms.openlocfilehash: f9b8c1501ecb3aba941e412ca1f0583a6df23f38
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970485"
---
# <a name="tutorial-configure-bldng-app-for-automatic-user-provisioning-in-bldngai"></a>教程：在 BLDNG.AI 中为 BLDNG APP 配置自动用户预配

本教程介绍在 BLDNG APP 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [BLDNG APP](https://dashboard.bldng.ai/) 以及取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 BLDNG.AI 中创建用户 
> * 在用户不再有访问需求的情况下，在 BLDNG.AI 中删除用户。
> * 使用户属性在 Azure AD 和 BLDNG.AI 之间保持同步
> * 在 BLDNG.AI 中预配组和组成员身份
> * [单一登录](../manage-apps/add-application-portal-setup-oidc-sso.md)到 BLDNG.AI（推荐）。


## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* [BLDNG.AI](https://dashboard.bldng.ai/) 协议。
* BLDNG.AI 发出的启用用户预配和使用 BLDNG APP 的邀请

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定要[在 Azure AD 与 BLDNG APP 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-bldng-app-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 BLDNG APP 以支持通过 Azure AD 进行预配

* 若要从 Azure 配置用户、用户组和组成员身份的预配，你需要有 BLDNG.AI 协议和租户。
* 若要获取协议，请联系[销售部门](mailto:salg@bldng.ai)，与销售代表取得联系。 如果不存在协议，你将无法继续操作，也无法使用 BLDNG APP。
* 如果已有有效的协议，只需启用用户预配，请直接联系[支持部门](mailto:support@bldng.ai)。

签订协议后，你将收到一封电子邮件，其中包含有关如何设置用户预配的详细说明。 如果需要，该电子邮件还将包含有关管理员同意（代表组织）使用 BLDNG APP 的详细信息。 

该电子邮件还将包含租户 URL 和机密令牌，以便在配置自动用户预配时使用。 

## <a name="step-3-add-bldng-app-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库中添加 BLDNG APP

从 Azure AD 应用程序库中添加 BLDNG APP，开始管理 BLDNG APP 的预配。 如果以前为 BLDNG APP 设置过 SSO，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 BLDNG APP 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-bldng-app"></a>步骤 5。 配置 BLDNG APP 的自动用户预配 

本部分介绍如何将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 BLDNG APP 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-bldng-app-in-azure-ad"></a>若要在 Azure AD 中为 BLDNG APP 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“BLDNG APP”。

    ![应用程序列表中的 BLDNG APP 链接](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

1. 在“管理员凭据”部分中，输入 BLDNG APP“租户 URL”和“机密令牌”  。 单击“测试连接”，确保 Azure AD 可连接到 BLDNG APP。 如果连接失败，请确保 BLDNG APP 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 BLDNG APP” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 BLDNG APP 的用户属性。 选为“匹配”属性的特性用于匹配 BLDNG APP 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 BLDNG APP API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

> [!NOTE]
> 需要注意的是，如果更改 externalId 的映射，租户中的用户将无法使用 BLDNG APP 登录。

   |属性|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;
   |活动|Boolean|   
   |displayName|字符串|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   

1. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 BLDNG APP” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 BLDNG APP 的组属性。 选为“匹配”属性的特性用于匹配 BLDNG APP 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|支持筛选|
      |---|---|---|
      |displayName|字符串|&check;
      |members|参考|
      |externalId|字符串|      

1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

1. 若要为 BLDNG APP 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“开”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 BLDNG APP 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

1. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="more-resources"></a>更多资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)