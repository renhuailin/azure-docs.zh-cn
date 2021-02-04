---
title: 教程：为 getAbstract 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何自动将用户 Azure AD 帐户预配到 getAbstract 以及取消其预配。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 50be022f04a5c9fde9758d533adce657763db269
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566711"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>教程：为 getAbstract 配置自动用户预配

本教程介绍了需要在 getAbstract 和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置后，Azure AD 使用 Azure AD 预配服务自动设置用户和组并取消其预配到 [getAbstract](https://www.getabstract.com) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 getAbstract 中创建用户。
> * 如果用户不需要访问权限，请删除 getAbstract 中的用户。
> * 使用户属性在 Azure AD 和 getAbstract 之间保持同步。
> * 预配 getAbstract 中的组和组成员身份。
> * [单一登录](getabstract-tutorial.md) 到 getAbstract (建议) 

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD 中[有权](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* GetAbstract 租户 (getAbstract 公司许可证) 。
* 已在 Azure AD 租户和 getAbstract 租户上启用 SSO。
* 批准和 SCIM 启用 getAbstract (向) 发送电子邮件 b2b.itsupport@getabstract.com 。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定谁在[预配范围](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)内。
3. 确定要 [在 Azure AD 与 getAbstract 之间映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的数据。 

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 getAbstract 以支持 Azure AD 的预配
1. 登录到 getAbstract
2. 单击右上角的 "设置" 图标，然后单击 " **我的中心管理员** " 选项。
 
    ![getAbstract 我的管理中心](media/getabstract-provisioning-tutorial/my-account.png)

3. 找到并单击 " **SCIM 管理** " 选项
 
    ![getAbstract SCIM 管理员](media/getabstract-provisioning-tutorial/scim-admin.png) 

4. 单击 " **开始** " 按钮 

    ![getAbstract SCIM 客户端 Id](media/getabstract-provisioning-tutorial/scim-client-go.png)

5. 单击 "**生成新令牌**"

    ![getAbstract SCIM 标记1](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

6. 如果确定，请单击 " **生成新令牌** " 按钮。 否则，单击 " **取消** " 按钮

    ![getAbstract SCIM 标记2](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

7. 最后，你可以单击 "复制到剪贴板" 图标或选择整个令牌并复制它。 另外请注意租户/基 URL 是 `https://www.getabstract.com/api/scim/v2` 。 这些值将在 Azure 门户中的 getAbstract 应用程序的 "预配" 选项卡的 " **机密令牌** " 和 " **租户 URL** " 字段中输入。

    ![getAbstract SCIM 标记3](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)


## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 getAbstract

从 Azure AD 应用程序库中添加 getAbstract，开始管理预配到 getAbstract。 如果以前为 SSO 设置了 getAbstract，则可以使用相同的应用程序。 不过，建议在最初测试集成时单独创建一个应用。 若要详细了解如何从库中添加应用，可以单击[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择只根据用户或组的特性来限定谁在预配范围内，可以使用范围筛选器，如[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述。 

* 将用户和组分配到 getAbstract 时，必须选择 " **默认" 访问权限** 以外的其他角色。 具有“默认访问”角色的用户被排除在预配范围之外，并在预配日志中被标记为无有效资格。 如果应用上唯一可用的角色是“默认访问”角色，可以[更新应用清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)来添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>步骤 5。 配置 getAbstract 的自动用户预配 

此部分逐步介绍了如何将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>若要在 Azure AD 中配置 getAbstract 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用”和“所有应用”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **getAbstract**"。

    ![应用程序列表中的 getAbstract 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入你的 GETABSTRACT 租户 URL 和机密令牌。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 getAbstract。 如果连接失败，请确保 getAbstract 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 getAbstract**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 getAbstract 的用户属性。 选为 " **匹配** " 属性的特性用于匹配 getAbstract 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，将需要确保 getAbstract API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |externalId|字符串|
   |preferredLanguage|字符串|

10. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 组同步到 getAbstract**"。

11. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 getAbstract 的组属性。 选为 " **匹配** " 属性的特性用于匹配 getAbstract 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    |Attribute|类型|支持筛选|
    |---|---|---|
    |displayName|字符串|&check;|
    |externalId|字符串|
    |members|参考|
12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 getAbstract 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 getAbstract 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败
* 检查[进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)了解有关隔离状态的详细信息。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
