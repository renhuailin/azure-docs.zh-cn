---
title: 教程：使用 Azure Active Directory 为 Apple School Manager 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Apple School Manager 以及如何解除预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: thwimmer
ms.openlocfilehash: 5031da3751d596228a40a354b527b718920b86e5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764947"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>教程：为 Apple School Manager 配置自动用户预配



本教程介绍在 Apple School Manager 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户预配到 [Apple School Manager](https://school.apple.com/) 以及将其解除预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Apple School Manager 中创建用户
> * 在用户不再有访问需求的情况下，在 Apple School Manager 中删除用户
> * 使用户属性在 Azure AD 和 Apple School Manager 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* 具有管理员、站点管理员或人员经理角色的 Apple School Manager 帐户。

> [!NOTE]
> 令牌传输到 Azure AD 以及建立成功的连接必须在 4 个日历日内完成，否则必须重新启动此过程。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要[在 Azure AD 与 Apple School Manager 之间映射](../app-provisioning/customize-application-attributes.md)的数据。

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Apple School Manager 以支持通过 Azure AD 进行预配

1. 在 Apple School Manager 中，使用具有管理员、站点管理员或人员经理角色的帐户登录。
2. 单击侧栏底部的“设置”，单击“组织设置”下方的“数据源”，然后单击“连接到数据源”。
3. 单击 SCIM 旁的“连接”，仔细阅读警告，单击“复制”，然后单击“关闭”。
[“连接到 SCIM”窗口，该窗口提供令牌以及令牌下方的“复制”按钮。] 让该窗口保持打开状态，从 Apple Business Manager 将租户 URL“https://federation.apple.com/feeds/school/scim”复制到 Azure AD

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> 不应与 Azure AD 管理员之外的任何人共享机密令牌。

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Apple School Manager

从 Azure AD 应用程序库添加 Apple School Manager，开始管理到 Apple School Manager 的预配。 如果以前为 Apple School Manager 设置过 SSO，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户分配到 Apple School Manager 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>步骤 5。 配置 Apple School Manager 的自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Apple School Manager”。

    ![应用程序列表中的 Apple School Manager](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，分别在“租户 URL”和“机密令牌”中输入从 Apple School Manager 检索到的 SCIM 2.0 基 URL 和访问令牌值   。 单击“测试连接”，以确保 Azure AD 可以连接到 Apple School Manager。 如果连接失败，请确保 Apple School Manager 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>如果连接成功，Apple School Manager 会将 SCIM 连接显示为活动状态。 对于 Apple School Manager，此过程最多可能需要 60 秒才能反映最新的连接状态。

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Apple School Manager” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Apple School Manager 的用户属性。 选为“匹配”属性的特性用于匹配 Apple School Manager 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |活动|Boolean|
   |userName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.givenName|字符串|
   |externalId|String|
   |区域设置|字符串|
   |timezone|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Apple School Manager 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Apple School Manager 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署

配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [查看 Apple School Manager 的 SCIM 要求](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Apple School Manager 如何使用人员 ID](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [使用 SCIM 将用户导入 Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [在 Apple School Manager 中解决 SCIM 用户帐户冲突](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [在 Apple School Manager 中删除显示的 Azure AD 帐户](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [在 Apple School Manager 中查看 SCIM 活动](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [在 Apple School Manager 中管理现有的 SCIM 令牌和连接](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [在 Apple School Manager 中断开 SCIM 连接](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [在 Apple School Manager 中排查 SCIM 连接问题](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)