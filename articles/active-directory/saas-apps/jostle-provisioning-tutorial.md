---
title: 教程：使用 Azure Active Directory 为 Jostle 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Jostle 及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: thwimmer
ms.openlocfilehash: 5fe843fb735aecda1eff51d3e52392a79bf8c1dd
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195528"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>教程：为 Jostle 配置自动用户预配

本教程介绍了在 Jostle 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Jostle](https://www.jostle.me/) 并自动取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Jostle 中创建用户
> * 在用户不再有访问需求的情况下，在 Jostle 中删除用户
> * 使用户特性在 Azure AD 与 Jostle 之间保持同步
> * [单一登录](jostle-tutorial.md)到 Jostle（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* [Jostle 租户](https://www.jostle.me/)。
* Jostle 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署

1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定要[在 Azure AD 与 Jostle 之间映射](../app-provisioning/customize-application-attributes.md)哪些数据。

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Jostle 以支持通过 Azure AD 进行预配

### <a name="automation-account"></a>自动化帐户

在开始之前，需要在 Jostle Intranet 中创建自动化用户。 这会是将用于配置 Azure 的帐户。 可以在“管理设置”>“用户帐户和数据”>“管理自动化用户”创建自动化用户。

有关自动化用户以及如何创建一个用户的更多详细信息，请参阅[本文](https://forum.jostle.us/hc/en-us/articles/360057364073)。

创建后，必须先激活自动化用户帐户（即至少登录 Intranet 一次），然后才能将它用于配置 Azure。

### <a name="manage-user-provisioning"></a>管理用户预配

在开始之前，请确保帐户订阅包含 SSO/用户预配功能。 如果不包含，可以与客户成功经理 <success@jostle.me> 联系，他们可以帮助你将它添加到你的帐户。

下一步是从 Jostle 获取 API URL 和 API 密钥 ：

1. 转到主导航，并单击“管理设置”。
1. 在“其他系统的用户数据”下，单击“管理用户预配” 。如果在此处未看到“管理用户预配”，并且已验证你的帐户包含 SSO/用户预配，请联系客户支持 <support@jostle.me>，以便在管理设置中启用此页面。
1. 在“用户预配 API 详细信息”部分中，转到“基 URL”字段，单击“复制”按钮并将 URL 保存在以后可以轻松访问的位置 。                                                           

   ![设置](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. 接下来，单击“添加新密钥...”按钮
1. 在以下屏幕上，转到“自动化用户”字段，然后使用下拉菜单选择自动化用户帐户。 

   ![集成帐户](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. 在“预配 API 密钥说明”字段中，为密钥提供名称（即“Azure”），然后单击“添加”按钮 。

1. 生成密钥后，请确保立即复制该密钥，并将它保存到用于保存 URL 的位置（因为只有此时密钥才会出现）。                                                               
1. 接下来，你会使用 API URL 和 API 密钥在 Azure 中配置集成 。
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>步骤 3。 从 Azure AD 应用程序库添加 Jostle

从 Azure AD 应用程序库添加 Jostle，开始管理 Jostle 的预配。 如果之前为 Jostle 设置过 SSO，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Jostle 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>步骤 5。 配置 Jostle 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和组分配在 Jostle 应用中创建、更新以及禁用用户和组。

> [!NOTE]
> 若要详细了解如何自动将用户预配到 Jostle，请参阅 [User-Provisioning-Azure-Integration](https://forum.jostle.us/hc/en-us/articles/360056368534-User-Provisioning-Azure-Integration)。

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>在 Azure AD 中为 Jostle 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Jostle”  。

    ![应用程序列表中的 Jostle 链接](common/all-applications.png)

1. 选择“预配”选项卡并单击“开始”。

    ![“预配”选项卡](common/provisioning.png)

1.  将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

1. 在“管理员凭据”部分中，输入你的 Jostle“租户 URL”和“机密令牌”信息  。 选择“测试连接”以确保 Azure AD 可以连接到 Jostle。 如果连接失败，请确保 Jostle 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。 但需要注意的是，Jostle 也会发送预配失败通知，因此这是可选的。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分，选择“将 Azure Active Directory 用户预配到 Jostle” 。

1. 在“特性映射”部分中，查看从 Azure AD 同步到 Jostle 的用户特性。 选为“匹配”属性的特性用于匹配 Jostle 中的用户帐户以执行更新操作。 如果更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 Jostle API 支持基于该属性筛选用户。 选择“保存”，提交所有更改。

   |属性|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |emails[type eq "work"].value|字符串|
   |emails[type eq "personal"].value|String|
   |emails[type eq "alternate1"].value|String|
   |emails[type eq "alternate2"].value|String|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|String|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |String|  

1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的说明。

1. 若要为 Jostle 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Jostle 的用户或组。对于 Jostle，应将“范围”设置为“仅同步分配的用户和组”。

    ![预配范围](common/provisioning-scope.png)

1. 准备好预配时，选择“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署

配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度。
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 若要了解有关隔离状态的详细信息，请参阅[隔离状态的应用程序预配](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="more-resources"></a>更多资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)