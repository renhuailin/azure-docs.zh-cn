---
title: 教程：使用 Azure Active Directory 为 Grammarly 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Grammarly 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: thwimmer
ms.openlocfilehash: 4ece3675a0d15821ae7691e85588d0575f13eae3
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767666"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>教程：为 Grammarly 配置自动用户预配

本教程介绍了在 Grammarly 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Grammarly](https://www.grammarly.com/) 和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Grammarly 中创建用户
> * 在用户不再有访问需求的情况下，在 Grammarly 中删除用户
> * 使用户属性在 Azure AD 和 Grammarly 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* 具有管理员访问权限的 Grammarly 业务帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定[在 Azure AD 与 Grammarly 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Grammarly 以支持通过 Azure AD 进行预配

与你的 Grammarly 代表联系，或写信到 <support@grammarly.com> 请求预配令牌。

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Grammarly

从 Azure AD 应用程序库添加 Grammarly，开始管理 Grammarly 的预配。 如果以前为 Grammarly 设置过 SSO，则可以使用同一应用程序。 我们建议你在初次测试集成时创建一个单独的应用。 若要了解有关如何从库中添加应用程序的详细信息，请参阅[本快速入门](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中

可以使用 Azure AD 预配服务根据对应用程序的分配或用户或组的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[通过范围筛选器预配应用](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中所述的范围筛选器。

* 将用户和组分配到 Grammarly 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以通过将一两个用户或组分配到应用来控制此选项。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>步骤 5。 配置 Grammarly 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配在 TestApp 中创建、更新和禁用用户或组。

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>在 Azure AD 中为 Grammarly 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序”   。

    ![显示“企业应用程序”窗格的屏幕截图。](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Grammarly”。

    ![显示应用程序列表中的 Grammarly 链接的屏幕截图。](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![显示“预配”选项卡的屏幕截图。](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![显示“预配模式”设置为“自动”的屏幕截图。](common/provisioning-automatic.png)

1. 在“管理员凭据”部分的“租户 URL”字段中，输入 `https://sso.grammarly.com/scim/v2`，然后在“机密令牌”字段中输入 Grammarly 提供的令牌（参见上面的步骤 2）。 单击“测试连接”，确保 Azure AD 可连接到 Grammarly。 如果连接失败，请确保 Grammarly 帐户具有管理员权限，然后重试。

    ![屏幕截图显示“租户 URL”和“机密令牌”框。](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![显示“通知电子邮件”框的屏幕截图。](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Grammarly” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 Grammarly 的用户属性。 选为“匹配”属性的特性用于匹配 Grammarly 中的用户帐户以执行更新操作。 如果更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 Grammarly API 支持基于该属性筛选用户。 选择“保存”，提交所有更改。

   |属性|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |externalId|字符串|
   |活动|Boolean|
   |displayName|字符串|
   |emails[type eq "work"].value|字符串|


1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的说明。

1. 若要为 Grammarly 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![显示“预配状态”切换为“启用”的屏幕截图。](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Grammarly 的用户或组 。

    ![屏幕截图显示预配范围。](common/provisioning-scope.png)

1. 准备好预配时，选择“保存”。

    ![屏幕截图显示“保存”按钮。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署

配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)以查看预配周期的状态以及完成进度。
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 若要了解有关隔离状态的详细信息，请参阅[隔离状态的应用程序预配](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)