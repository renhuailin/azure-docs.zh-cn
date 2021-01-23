---
title: 教程：ThousandEyes 的用户预配 - Azure AD
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 ThousandEyes 和取消其预配。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735077"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>教程：为 ThousandEyes 配置自动用户预配

本教程的目的是说明从 Azure AD 自动将用户帐户预配到 ThousandEyes 和取消其预配而需要在 ThousandEyes 和 Azure 中执行的步骤。 

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

* Azure Active Directory 租户
* 已启用[标准计划](https://www.thousandeyes.com/pricing)或更高版本的 ThousandEyes 租户 
* 在 ThousandEyes 中具有管理员权限的用户帐户 

> [!NOTE]
> Azure AD 预配集成依赖于 [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)，标准计划或更高版本中向 ThousandEyes 团队提供 ThousandEyes SCIM API。

## <a name="assigning-users-to-thousandeyes"></a>将用户分配到 ThousandEyes

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。 

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 ThousandEyes 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 ThousandEyes 应用：

[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>将用户分配到 ThousandEyes 的重要提示

* 建议将单个 Azure AD 用户分配到 ThousandEyes 以测试预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 ThousandEyes，必须在分配对话框中选择用户角色或其他特定于应用程序的有效角色（如果有）。 “默认访问权限”角色不可用于预配，将跳过这些用户。

## <a name="configuring-user-provisioning-to-thousandeyes"></a>向 ThousandEyes 配置用户预配 

本部分将指导用户完成以下操作：将 Azure AD 连接到 ThousandEyes 的用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 ThousandEyes 中创建、更新和禁用所分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 ThousandEyes 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>在 Azure AD 中为 ThousandEyes 配置自动用户帐户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 如果已为 ThousandEyes 配置单一登录，请使用搜索字段搜索 ThousandEyes 实例。 否则，请选择“添加”并在应用程序库中搜索“ThousandEyes”。 从搜索结果中选择 ThousandEyes，并将其添加到应用程序列表。

    ![“应用程序”列表中的“ThousandEyes”链接](common/all-applications.png)
    
3. 选择 ThousandEyes 实例，并选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

![屏幕截图显示了 ThousandEyes 的“预配”选项卡，其中预配模式选择了“自动”。](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. 在“管理员凭据”部分下，输入 ThousandEyes 帐户生成的“OAuth 持有者令牌”（可在 ThousandEyes 帐户的“配置文件”部分下找到或生成令牌）。

    ![屏幕截图显示在何处可以找到当前帐户组的“帐户设置”链接。](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. 在 Azure 门户中，单击“测试连接”以确保 Azure AD 可以连接到 ThousandEyes 应用。 如果连接失败，请确保 ThousandEyes 帐户具有管理员权限，然后重试步骤 5。

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 ThousandEyes”。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 ThousandEyes 的用户属性。 选为“匹配”属性的特性用于匹配 Parsable 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 Parsable Suite API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

     |Attribute|类型|支持筛选|
     |---|---|---|
     |externalId|字符串|&check;|
     |userName|字符串|&check;|
     |活动|Boolean|
     |displayName|字符串|
     |emails[type eq "work"].value|字符串|
     |name.formatted|字符串|


11. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

12. 若要为 ThousandEyes 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

13. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 ThousandEyes 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

14. 已准备好预配时，单击“保存”。

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