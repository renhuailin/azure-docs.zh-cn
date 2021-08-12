---
title: 教程：使用 Azure Active Directory 为 OpenText Directory Services 配置自动用户预配 | Microsoft 文档
description: 了解如何将用户帐户从 Azure AD 自动预配到 OpenText Directory Services 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: thwimmer
ms.openlocfilehash: f82c46c6853f33e2137cd54c03b2bfa1df9dca8a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760253"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>教程：为 OpenText Directory Services 配置自动用户预配

本教程介绍了在 OpenText Directory Services 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 OpenText Directory Services 和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 OpenText Directory Services 中创建用户
> * 在用户不再有访问需求的情况下，在 OpenText Directory Services 中删除用户
> * 使用户属性在 Azure AD 和 OpenText Directory Services 之间保持同步
> * 在 OpenText Directory Services 中预配组和组成员身份
> * [单一登录](./opentext-directory-services-tutorial.md)到 OpenText Directory Services（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* Azure AD 可访问的 OTDS 安装。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 OpenText Directory Services 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 OpenText Directory Services 以支持通过 Azure AD 进行预配

> [!NOTE]
> 以下步骤适用于 OpenText Directory Services 安装。 它们不适用于 OpenText CoreShare 或 OpenText OT2 租户。

1. 创建专用机密 OAuth 客户端。
2. 设置较长的访问令牌生存期。

      ![访问令牌生存期](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. 不要指定任何重定向 URL。 不需要它们。 
4. OTDS 会生成并显示客户端密码。 在安全位置保存“客户端 id”和“客户端密码” 。

      ![客户端机密](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. 为要从 Azure AD 同步的用户和组创建分区。

      ![分区页](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. 将管理权限授予在将用于要同步的 Azure AD 用户和组的分区上创建的 OAuth 客户端。    
      * 分区 > 操作 -> 编辑管理员

      ![管理员页](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. 必须在 Azure AD 中检索和配置机密令牌。 任何 HTTP 客户端应用程序都可用于此用途。 下面是使用 OTDS 中包含的 Swagger API 应用程序进行检索的步骤。
      * 在 Web 浏览器中，转到 {OTDS URL}/otdsws/oauth2
      * 转到 /token，然后单击右上角的锁定图标。 输入先前分别作为用户名和密码检索到的 OAuth 客户端 ID 和机密。 单击“授权”。

      ![授权按钮](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. 为 grant_type 选择“client_credentials”，然后单击“执行” 。

      ![执行按钮](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. 响应中的访问令牌应在 Azure AD 的“机密令牌”字段中使用。

      ![访问令牌](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 OpenText Directory Services

从 Azure AD 应用程序库中添加 OpenText Directory Services，开始管理到 OpenText Directory Services 的预配。 如果以前为 SSO 设置过 OpenText Directory Services，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 OpenText Directory Services 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>步骤 5。 配置到 OpenText Directory Services 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>若要在 Azure AD 中为 OpenText Directory Services 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“OpenText 目录服务”。

    ![应用程序列表中的 OpenText Directory Services 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，输入 OpenText Directory Services 租户 URL
   * 非特定租户 url：{OTDS URL}/scim/{partitionName}
   * 特定租户 url：{OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. 输入在步骤 2 中检索到的机密令牌。 单击“测试连接”以确保 Azure AD 可以连接到 OpenText Directory Services。 如果连接失败，请确保 OpenText Directory Services 帐户具有管理员权限，然后重试。

      ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 OpenText Directory Services” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 OpenText Directory Services 的用户属性。 选为“匹配”属性的特性用于匹配 OpenText Directory Services 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 OpenText Directory Services API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |活动|Boolean|
   |displayName|字符串|
   |title|字符串|
   |emails[type eq "work"].value|字符串|
   |preferredLanguage|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |addresses[type eq "work"].formatted|字符串|
   |addresses[type eq "work"].streetAddress|字符串|
   |addresses[type eq "work"].locality|字符串|
   |addresses[type eq "work"].region|字符串|
   |addresses[type eq "work"].postalCode|字符串|
   |addresses[type eq "work"].country|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "fax"].value|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考| 

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 OpenText Directory Services” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 OpenText Directory Services 的组属性。 选为“匹配”属性的特性用于匹配 OpenText Directory Services 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|
      |---|---|
      |displayName|字符串|
      |externalId|字符串|
      |members|参考|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 OpenText Directory Services 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 OpenText Directory Services 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

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