---
title: 教程：使用 Azure Active Directory 为 getAbstract 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure Active Directory 自动预配到 getAbstract 以及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: thwimmer
ms.openlocfilehash: 1c7278b0d8a0e9ed3d94e69d1ad5da34ab0bc854
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322106"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>教程：为 getAbstract 配置自动用户预配

本教程介绍了在 getAbstract 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [getAbstract](https://www.getabstract.com) 以及将其取消预配。 有关此服务的功能、工作原理以及常见问题的重要详情，请参阅[使用 Azure AD 自动将用户预配到软件即服务 (SaaS) 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能

> [!div class="checklist"]
> * 在 getAbstract 中创建用户。
> * 在用户不再有访问需求的情况下，在 getAbstract 中删除用户。
> * 使用户属性在 Azure AD 和 getAbstract 之间保持同步。
> * 在 getAbstract 中预配组和组成员身份。
> * 启用[单一登录 (SSO)](getabstract-tutorial.md) 到 getAbstract（推荐）。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中拥有配置预配的[权限](../roles/permissions-reference.md)的用户帐户。 示例有应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员。
* getAbstract 租户（getAbstract 公司许可证）。
* 已对 Azure AD 租户和 getAbstract 租户启用 SSO。
* 审批和跨域身份管理系统 (SCIM) 启用 getAbstract。 （将电子邮件发送到 b2b.itsupport@getabstract.com。）

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署

1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定[在 Azure AD 与 getAbstract 之间映射](../app-provisioning/customize-application-attributes.md)的数据。

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>步骤 2。 将 getAbstract 配置为支持使用 Azure AD 进行预配

1. 登录到 getAbstract。
1. 选择右上角的设置图标，然后选择“My Central Admin”选项。

    ![屏幕截图显示 getAbstract 的 My Central Admin。](media/getabstract-provisioning-tutorial/my-account.png)

1. 找到并选择“SCIM Admin”选项。

    ![屏幕截图显示 getAbstract 的 SCIM Admin。](media/getabstract-provisioning-tutorial/scim-admin.png)

1. 选择“转到”。

    ![屏幕截图显示 getAbstract 的 SCIM 客户端 ID。](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. 选择“生成新令牌”。

    ![屏幕截图显示 getAbstract 的 SCIM 令牌 1。](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. 如果确定，请选择“Generate new token”。 否则，请选择“取消”。

    ![屏幕截图显示 getAbstract 的 SCIM 令牌 2。](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. 选择复制到剪贴板图标或选择整个令牌并复制它。 另外请注意租户/基 URL 是 `https://www.getabstract.com/api/scim/v2`。 在 Azure 门户将这些值输入 getAbstract 应用程序的“Provisioning”选项卡的“Secret Token”和“Tenant URL”框中  。

    ![屏幕截图显示 getAbstract 的 SCIM 令牌 3。](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>步骤 3。 从 Azure AD 应用程序库添加 getAbstract

从 Azure AD 应用程序库中添加 getAbstract，开始管理到 getAbstract 的预配。 如果以前为 getAbstract 设置过 SSO，则可以使用同一应用程序。 我们建议你在初次测试集成时创建一个单独的应用。 若要了解有关如何从库中添加应用程序的详细信息，请参阅[本快速入门](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中

可以使用 Azure AD 预配服务根据对应用程序的分配或用户或组的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[通过范围筛选器预配应用](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中所述的范围筛选器。

* 将用户和组分配到 getAbstract 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以通过将一两个用户或组分配到应用来控制此选项。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>步骤 5。 配置到 getAbstract 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配在 TestApp 中创建、更新和禁用用户或组。

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>在 Azure AD 中为 getAbstract 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序”   。

    ![显示“企业应用程序”窗格的屏幕截图。](common/enterprise-applications.png)

1. 在应用程序列表中，选择“getAbstract”。

    ![屏幕截图显示应用程序列表中的 getAbstract 链接。](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![显示“预配”选项卡的屏幕截图。](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![显示“预配模式”设置为“自动”的屏幕截图。](common/provisioning-automatic.png)

1. 在“管理员凭据”部分中，输入你的 getAbstract“租户 URL”和“机密令牌”信息  。 选择“测试连接”以确保 Azure AD 可以连接到 getAbstract。 如果连接失败，请确保 getAbstract 帐户具有管理员权限，然后重试。

    ![屏幕截图显示“租户 URL”和“机密令牌”框。](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![显示“通知电子邮件”框的屏幕截图。](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 getAbstract” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 getAbstract 的用户属性。 选为“匹配”属性的特性用于匹配 getAbstract 中的用户帐户以执行更新操作。 如果更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 getAbstract API 支持基于该属性筛选用户。 选择“保存”，提交所有更改。

   |属性|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |externalId|字符串|
   |preferredLanguage|字符串|

1. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 getAbstract” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 getAbstract 的组属性。 选为“匹配”属性的特性用于匹配 getAbstract 中的组以执行更新操作。 选择“保存”，提交所有更改。

    |属性|类型|支持筛选|
    |---|---|---|
    |displayName|字符串|&check;|
    |externalId|字符串|
    |members|参考|

1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的说明。

1. 若要为 getAbstract 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![显示“预配状态”切换为“启用”的屏幕截图。](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 getAbstract 的用户或组 。

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
