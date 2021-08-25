---
title: 教程：使用 Azure Active Directory 为 Freshservice Provisioning 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Freshservice Provisioning 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e03ec65a-25ef-4c91-a364-36b2f007443c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: thwimmer
ms.openlocfilehash: 2f5ee2257652bdfa07081a558a8723c334c11ed9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322103"
---
# <a name="tutorial-configure-freshservice-provisioning-for-automatic-user-provisioning"></a>教程：为 Freshservice Provisioning 配置自动用户预配

本教程介绍在 Freshservice Provisioning 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户预配到 [Freshservice Provisioning](https://effy.co.in/) 并自动取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Freshservice Provisioning 中创建用户
> * 在用户不再有访问需求的情况下，在 Freshservice Provisioning 中删除用户
> * 使用户特性在 Azure AD 和 Freshservice Provisioning 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中[有权](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 具有组织管理员权限的 [Freshservice 帐户](https://www.freshservice.com)。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)中。
3. 确定[在 Azure AD 与 Freshservice Provisioning 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-freshservice-provisioning-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Freshservice Provisioning 以支持通过 Azure AD 进行预配

1. 在 Freshservice 帐户上，通过导航到“Freshservice 管理” > “应用” > “获取应用”，从市场安装“Azure Provisioning (SCIM)”应用   。
2. 在配置屏幕中，提供“Freshservice 域”（例如 `acme.freshservice.com`）和“组织管理 API 密钥” 。
3. 单击 **“继续”** 。
4. 突出显示并复制“持有者令牌”。 在 Azure 门户的 Freshservice Provisioning 应用程序的“预配”选项卡中，将此值输入到“机密令牌”字段中。
5. 单击“安装”以完成安装。
6. “租户 URL”为 `https://scim.freshservice.com/scim/v2`。 在 Azure 门户的 Freshservice Provisioning 应用程序的“预配”选项卡中，将此值输入到“租户 URL”字段中。

## <a name="step-3-add-freshservice-provisioning-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Freshservice Provisioning

从 Azure AD 应用程序库添加 Freshservice Provisioning，开始管理到 Freshservice Provisioning 的预配。 若要详细了解如何从库中添加应用，可以单击[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤](../manage-apps/assign-user-or-group-access-portal.md)将用户分配到应用程序。 如果选择仅根据用户的属性来限定要对谁进行预配，可以使用[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的范围筛选器。 

* 将用户分配到 Freshservice Provisioning 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，请先在少量用户中进行测试。 如果预配范围设置为分配的用户，则可以先尝试将一两个用户分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-freshservice-provisioning"></a>步骤 5。 配置 Freshservice Provisioning 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务，以基于 Azure AD 中的用户分配在 Freshservice Provisioning 中创建、更新和禁用用户。

### <a name="to-configure-automatic-user-provisioning-for-freshservice-provisioning-in-azure-ad"></a>在 Azure AD 中为 Freshservice Provisioning 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Freshservice Provisioning”。

    ![应用程序列表中的 Freshservice Provisioning 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分，输入 Freshservice Provisioning 租户 URL 和机密令牌。 单击“测试连接”以确保 Azure AD 可以连接到 Freshservice Provisioning。 如果连接失败，请确保 Freshservice Provisioning 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Freshservice Provisioning” 。

9. 在“特性映射”部分中，查看从 Azure AD 同步到 Freshservice Provisioning 的用户特性。 选为“匹配”属性的特性用于匹配 Freshservice Provisioning 中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则需要确保 Freshservice Provisioning API 支持基于该特性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |displayName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |addresses[type eq "work"].formatted|字符串|
   |区域设置|字符串|
   |title|字符串|
   |timezone|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|
   |urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent|String|

> [!NOTE]
> 可以按照以下步骤将自定义扩展属性添加到架构，以满足应用程序的需求：
> * 在“映射”下，选择“预配 Azure Active Directory 用户”。
> * 在页面底部，选择“显示高级选项”。
> * 选择“编辑 Freshservice 的属性列表”。
> * 在属性列表底部，在提供的字段中输入有关自定义属性的信息。 自定义属性 urn 命名空间必须遵循如以下示例所示的模式。 可根据应用程序的要求自定义“CustomAttribute”，例如：urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent 。
> * 必须为自定义属性选择适当的数据类型，然后单击“保存”。
> * 导航回默认映射屏幕，并单击“添加新映射”。 自定义属性将显示在“目标属性”列表下拉列表中。

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Freshservice Provisioning 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Freshservice Provisioning 的用户 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
2. 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
