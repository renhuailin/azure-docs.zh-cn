---
title: 教程：使用 Azure Active Directory 为 Clarizen One 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Clarizen One 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: thwimmer
ms.openlocfilehash: 1e1ea7a7207486a9fced1cbd330df6b00153a14c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764258"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>教程：为 Clarizen One 配置自动用户预配

本教程介绍了在 Clarizen One 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Clarizen One](https://www.clarizen.com/) 以及将其取消预配。 有关此服务的功能、工作原理以及常见问题的信息，请参阅[使用 Azure AD 自动将用户预配到软件即服务 (SaaS) 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能

> [!div class="checklist"]
> * 在 Clarizen One 中创建用户。
> * 在用户不再有访问需求的情况下，在 Clarizen One 中删除用户。
> * 使用户属性在 Azure AD 和 Clarizen One 之间保持同步。
> * 在 Clarizen One 中预配组和组成员身份。
> * 建议[单一登录 (SSO)](./clarizen-tutorial.md) 到 Clarizen One。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中拥有配置预配的[权限](../roles/permissions-reference.md)的用户帐户。 示例有应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员。
* Clarizen One 中拥有集成用户和精简管理员 [权限](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署

1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定[在 Azure AD 与 Clarizen One 之间映射](../app-provisioning/customize-application-attributes.md)的数据。

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Clarizen One 以支持通过 Azure AD 进行预配

1. 根据 Clarizen One 环境和数据中心，选择以下四个租户 URL 中的一个：
      * 美国生产数据中心： https://servicesapp2.clarizen.com/scim/v2
      * 欧洲生产数据中心： https://serviceseu1.clarizen.com/scim/v2
      * 美国沙盒数据中心： https://servicesapp.clarizentb.com/scim/v2
      * 欧洲沙盒数据中心： https://serviceseu.clarizentb.com/scim/v2

1. 生成 [API 密钥](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)。 将在 Azure 门户中 Clarizen One 应用程序“预配”选项卡上的“机密令牌”框中输入此值 。

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库中添加 Clarizen One

从 Azure AD 应用程序库中添加 Clarizen One，开始管理到 Clarizen One 的预配。 如果以前为 Clarizen One 设置过 SSO，则可以使用同一应用程序。 最初测试集成时，请创建单独的应用。 若要了解有关如何从库中添加应用程序的详细信息，请参阅[向 Azure AD 租户添加应用程序](../manage-apps/add-application-portal.md)。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中

使用 Azure AD 预配服务，可以根据对应用程序的分配或是用户或组的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，请按照[在 Azure Active Directory 中管理应用的用户分配](../manage-apps/assign-user-or-group-access-portal.md)中的步骤将用户和组分配到应用程序。 如果选择仅根据用户或组的属性来限定要对谁进行预配，请使用[使用范围筛选器进行基于属性的应用程序预配](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。

* 将用户和组分配到 Clarizen One 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。
* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，可以通过将一两个用户或组分配到应用来维持控制。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>步骤 5。 配置到 Clarizen One 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配在 TestApp 中创建、更新和禁用用户或组。

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>在 Azure AD 中为 Clarizen One 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序”   。

      ![显示“企业应用程序”窗格的屏幕截图。](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Clarizen One”。

      ![显示应用程序列表中的 Clarizen One 链接的屏幕截图。](common/all-applications.png)

1. 选择“预配”  选项卡。

      ![显示“预配”选项卡的屏幕截图。](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

      ![显示“预配”选项卡“自动”选项的屏幕截图。](common/provisioning-automatic.png)

1. 在“管理员凭据”部分，输入 Clarizen One 租户 URL 和机密令牌  。 选择“测试连接”以确保 Azure AD 可以连接到 Clarizen One。 如果连接失败，请确保 Clarizen One 帐户具有管理员权限，然后重试。

    ![显示“机密令牌”框的屏幕截图。](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![显示“通知电子邮件”框的屏幕截图。](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Clarizen One” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 Clarizen One 的用户属性。 选为“匹配”属性的特性用于匹配 Clarizen One 中的用户帐户以执行更新操作。 如果更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则必须确保 Clarizen One API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |displayName|字符串|
   |活动|Boolean|
   |title|字符串|
   |emails[type eq "work"].value|字符串|
   |emails[type eq "home"].value|字符串|
   |emails[type eq "other"].value|字符串|
   |preferredLanguage|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |name.honorificPrefix|String|
   |name.honorificSuffix|字符串|
   |addresses[type eq "other"].formatted|字符串|
   |addresses[type eq "work"].formatted|字符串|
   |addresses[type eq "work"].country|字符串|
   |addresses[type eq "work"].region|字符串|
   |addresses[type eq "work"].locality|字符串|
   |addresses[type eq "work"].postalCode|字符串|
   |addresses[type eq "work"].streetAddress|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "fax"].value|字符串|
   |phoneNumbers[type eq "home"].value|字符串|
   |phoneNumbers[type eq "other"].value|字符串|
   |phoneNumbers[type eq "pager"].value|字符串|
   |externalId|字符串|
   |nickName|String|
   |区域设置|字符串|
   |roles[primary eq "True".type]|字符串|
   |roles[primary eq "True".value]|字符串|
   |timezone|字符串|
   |userType|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|字符串|

1. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Clarizen One” 。

1. 在“属性映射”部分中，查看从 Azure AD 同步到 Clarizen One 的组属性。 选为“匹配”属性的特性用于匹配 Clarizen One 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |externalId|字符串|
      |members|参考|

1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

1. 若要为 Clarizen One 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。  

      ![显示“预配状态”切换为“启用”的屏幕截图。](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Clarizen One 的用户或组 。

      ![显示预配范围的屏幕截图。](common/provisioning-scope.png)

1. 准备好预配时，选择“保存”。

      ![显示保存预配配置的屏幕截图。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署

配置预配后，请使用以下资源来监视部署。

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
1. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)以查看预配周期的状态以及完成进度。
1. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 若要了解有关隔离状态的详细信息，请参阅[隔离状态下的应用程序预配](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="troubleshooting-tips"></a>故障排除提示

将用户分配到 Clarizen One 库应用时，请仅选择“用户”角色。 以下角色无效：

* 管理员 (admin)
* 电子邮件报告用户
* 外部用户
* 财务用户
* 社交用户
* 超级用户
* 时间和支出用户

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)