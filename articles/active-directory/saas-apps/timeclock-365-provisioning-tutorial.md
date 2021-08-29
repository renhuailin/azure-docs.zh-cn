---
title: 教程：使用 Azure Active Directory 为 TimeClock 365 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 TimeClock 365 以及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: dc5e95c8-d878-43dd-918e-69e1686b4db6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: thwimmer
ms.openlocfilehash: dc34defaee7f7a8c2b007fe7946694c58aebd1b5
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323819"
---
# <a name="tutorial-configure-timeclock-365-for-automatic-user-provisioning"></a>教程：为 TimeClock 365 配置自动用户预配

本教程介绍在 TimeClock 365 和 Azure Active Directory (Azure AD) 中配置自动用户预配所需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [TimeClock 365](https://timeclock365.com/) 以及将其取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 TimeClock 365 中创建用户
> * 在用户不再有访问需求的情况下，在 TimeClock 365 中删除用户
> * 使用户特性在 Azure AD 与 TimeClock 365 之间保持同步
> * [单一登录](../manage-apps/add-application-portal-setup-oidc-sso.md)到 TimeClock 365（推荐）。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* [TimeClock 365](https://timeclock365.com/) 租户。
* TimeClock 365 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定要[在 Azure AD 与 TimeClock 365 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-timeclock-365-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 TimeClock 365 以支持通过 Azure AD 进行预配

1. 登录到 [Timeclock365 管理控制台](https://live.timeclock365.com)。

1. 导航到“设置”>“公司配置文件”>“常规”。

    ![“生成令牌”页](media/timeclock-365-provisioning-tutorial/generate-token-page.png)

1. 向下滚动到“Azure 用户同步”。复制并保存“Azure AD 令牌”。 在 Azure 门户的 TimeClock 365 应用程序的“预配”选项卡中，将此值输入到“机密令牌”字段中。 

    ![生成令牌](media/timeclock-365-provisioning-tutorial/generate-token.png)

1. 在 Azure 门户的 TimeClock 365 应用程序的“预配”选项卡中，将 `https://live.timeclock365.com/scim` 输入“租户 URL”字段。

## <a name="step-3-add-timeclock-365-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 TimeClock 365

从 Azure AD 应用程序库添加 TimeClock 365，开始管理 TimeClock 365 的预配。 如果之前为 SSO 设置过 TimeClock 365，可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 TimeClock 365 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-timeclock-365"></a>步骤 5。 配置 TimeClock 365 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TimeClock 365 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-timeclock-365-in-azure-ad"></a>在 Azure AD 中为 TimeClock 365 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“TimeClock 365”。

    ![应用程序列表中的 TimeClock 365 链接](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

1. 在“管理员凭据”部分，输入 TimeClock 365 的“租户 URL”和“机密令牌”  。 单击“测试连接”以确保 Azure AD 可以连接到 TimeClock 365。 如果连接失败，请确保 TimeClock 365 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 TimeClock 365” 。

1. 在“特性映射”部分中，查看从 Azure AD 同步到 TimeClock 365 的用户特性。 选为“匹配”属性的特性用于匹配 TimeClock 365 中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](../app-provisioning/customize-application-attributes.md)，则需要确保 TimeClock 365 API 支持根据该特性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;
   |活动|Boolean|
   |displayName|字符串|   
   |emails[type eq "work"].value|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|字符串|
   
1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

1. 若要为 TimeClock 365 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 TimeClock 365 的用户和/或组 。

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