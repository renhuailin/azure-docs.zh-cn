---
title: 教程：使用 Azure Active Directory 为 Templafy OpenID Connect 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Templafy OpenID Connect 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: thwimmer
ms.openlocfilehash: 56046874155cd54a380d31607331d76f9982c167
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759117"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Tutorial:为 Templafy OpenID Connect 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Templafy OpenID Connect 以及取消其预配需在 Templafy OpenID Connect 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [Templafy 租户](https://www.templafy.com/pricing/)。
* Templafy 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Templafy OpenID Connect 之间映射](../app-provisioning/customize-application-attributes.md)哪些数据。 

## <a name="assigning-users-to-templafy-openid-connect"></a>将用户分配到 Templafy OpenID Connect

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Templafy OpenID Connect。 确定后，可以按照此处的说明将这些用户和/或组分配到 Templafy OpenID Connect：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>将用户分配到 Templafy OpenID Connect 的重要提示

* 建议将单个 Azure AD 用户分配到 Templafy OpenID Connect 以测试自动用户预配配置。 稍后可分配更多用户和/或组。

* 如果将用户分配到 Templafy OpenID Connect，必须在“分配”对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Templafy OpenID Connect 以支持通过 Azure AD 进行预配

使用 Azure AD 为 Templafy OpenID Connect 配置自动用户预配之前，需要在 Templafy OpenID Connect 上启用 SCIM 预配。

1. 登录到 Templafy 管理控制台。 单击“管理”。

    ![Templafy 管理控制台](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. 单击“身份验证方法”。

    ![Templafy 管理部分的屏幕截图，其中突出显示了“身份验证方法”选项。](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. 复制“SCIM Api-key”值。 将在 Azure 门户中 Templafy OpenID Connect 应用程序的“预配”选项卡的“机密令牌”字段中输入此值。

    ![SCIM API 密钥的屏幕截图。](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>步骤 3. 从库中添加 Templafy OpenID Connect

若要使用 Azure AD 为 Templafy OpenID Connect 配置自动用户预配，需要从 Azure AD 应用程序库将 Templafy OpenID Connect 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Templafy OpenID Connect，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 Templafy OpenID Connect，在结果面板中选择“Templafy OpenID Connect”，然后单击“添加”按钮添加该应用程序  。

    ![结果列表中的 Templafy OpenID Connect](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>步骤 4. 为 Templafy OpenID Connect 配置自动用户预配 

本部分逐步介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Templafy OpenID Connect 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Templafy 单一登录教程](templafy-tutorial.md)中提供的说明为 Templafy 启用基于 OpenID Connect 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>在 Azure AD 中为 Templafy OpenID Connect 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Templafy OpenID Connect”。

    ![应用程序列表中的 Templafy OpenID Connect 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://scim.templafy.com/scim` 。 在“机密令牌”中，输入之前检索到的“SCIM API-key”值 。 单击“测试连接”以确保 Azure AD 可以连接到 Templafy。 如果连接失败，请确保 Templafy 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Templafy OpenID Connect” 。

    ![Templafy OpenID Connect 用户映射](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Templafy OpenID Connect 的用户属性。 选为“匹配”属性的特性用于匹配 Templafy OpenID Connect 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |displayName|字符串|
   |title|字符串|
   |preferredLanguage|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |phoneNumbers[type eq "work"].value|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|
   |phoneNumbers[type eq "fax"].value|字符串|
   |externalId|字符串|
   |addresses[type eq "work"].locality|字符串|
   |addresses[type eq "work"].postalCode|字符串|
   |addresses[type eq "work"].region|字符串|
   |addresses[type eq "work"].streetAddress|字符串|
   |addresses[type eq "work"].country|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|字符串|

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Templafy” 。

    ![Templafy OpenID Connect 组映射](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Templafy OpenID Connect 的组属性。 选为“匹配”属性的特性用于匹配 Templafy OpenID Connect 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

      |Attribute|类型|支持筛选|
      |---|---|---|
      |displayName|字符串|&check;|
      |members|参考|
      |externalId|字符串|      

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Templafy OpenID Connect 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Templafy OpenID Connect 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

    此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Templafy OpenID Connect 执行的所有操作。

## <a name="step-5-monitor-your-deployment"></a>步骤 5。 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)