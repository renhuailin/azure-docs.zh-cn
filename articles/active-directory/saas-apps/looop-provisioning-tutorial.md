---
title: 教程：使用 Azure Active Directory 为 Looop 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 来使其自动将用户帐户预配到 Looop 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: thwimmer
ms.openlocfilehash: c17c56651dda65d5bd151f0f274af6ed377fb7df
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229770"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>教程：为 Looop 配置自动用户预配

本教程的目的是演示需要执行哪些步骤在 Looop 和 Azure Active Directory (Azure AD) 中配置 Azure AD 来使其自动将用户和/或组预配到 Looop 以及对其取消预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Looop 租户](https://www.looop.co/pricing/)
* 具有管理员权限的 Looop 用户帐户。

## <a name="assign-users-to-looop"></a>将用户分配到 Looop

Azure Active Directory 使用“分配”这一概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Looop。 确定后，可按照此处的说明将这些用户和/或组分配到 Looop：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>有关将用户分配到 Looop 的重要提示

* 建议将单个 Azure AD 用户分配到 Looop 来测试自动用户预配配置。 稍后可分配更多用户和/或组。

* 如果将用户分配到 Looop，必须在分配对话框中选择应用程序特定的所有有效角色（若可用）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-looop-for-provisioning"></a>设置 Looop 以进行预配

在使用 Azure AD 为 Looop 配置自动用户预配之前，需要从 Looop 检索一些预配信息。

1. 登录 [Looop 管理控制台](https://app.looop.co/#/login)，然后选择“帐户”。 在“帐户设置”下选择“身份验证” 。

    ![Looop 管理员](media/looop-provisioning-tutorial/admin.png)

2. 单击“SCIM 集成”下的“重置令牌”来生成新令牌 。

    ![Looop 令牌](media/looop-provisioning-tutorial/resettoken.png)

3. 复制 SCIM 终结点和令牌 。 在 Azure 门户的 Looop 应用程序的“预配”选项卡中，将这些值输入“租户 URL”和“机密令牌”字段 。 

    ![Looop 创建令牌](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>从库中添加 Looop

若要使用 Azure AD 为 Looop 配置自动用户预配，需要从 Azure AD 应用程序库将 Looop 添加到托管的 SaaS 应用程序列表。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 Looop，在结果面板中选择“Looop” 。 

    ![结果列表中的“Looop”](common/search-new-app.png)

5. 选择“注册 Looop”按钮，它会将你重定向到 Looop 的登录页面。 

    ![Looop OIDC 添加](media/looop-provisioning-tutorial/signup.png)

6. Looop 是一款 OpenIDConnect 应用，因此请选择使用 Microsoft 工作帐户登录到 Looop。

    ![Looop OIDC 登录](media/looop-provisioning-tutorial/msftlogin.png)

7. 身份验证成功后，请接受同意页面的同意提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 Looop 帐户。

    ![Looop OIDC 同意](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>配置到 Looop 的自动用户预配 

本部分逐步介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Looop 中创建、更新以及禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>若要在 Azure AD 中为 Looop 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Looop”。

    ![应用程序列表中的“Looop”链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://<organisation_domain>.looop.co/scim/v2` 。 例如，`https://demo.looop.co/scim/v2`。 在“机密令牌”中，输入先前从 Looop 检索到并保存的值。 单击“测试连接”，确保 Azure AD 可连接到 Looop。 如果连接失败，请确保 Looop 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Looop” 。

    ![Looop 用户映射](media/looop-provisioning-tutorial/usermappings.png)

9. 在“特性映射”部分中，查看从 Azure AD 同步到 Looop 的用户特性。 选为“匹配”属性的特性用于匹配 Looop 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|字符串|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|字符串|

10. 在“映射”部分下，选中“将 Azure Active Directory 组同步到 Meta Networks Connector” 。

    ![Looop 组映射](media/looop-provisioning-tutorial/groupmappings.png)

11. 在“特性映射”部分中，审阅从 Azure AD 同步到 Meta Networks Connector 的组特性。 选为“匹配”属性的特性是用于匹配 Meta Networks Connector 中用于更新操作的组。 选择“保存”按钮以提交任何更改  。

    |Attribute|类型|支持筛选|
    |---|---|---|
    |displayName|字符串|&check;|
    |members|参考|
    |externalId|字符串|


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为 Looop 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“开”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Looop 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Looop 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="change-log"></a>更改日志

* 07/15/2021 - 已添加了企业扩展用户属性 **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department**、**urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber** 和 **urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager**。
* 07/15/2021 - 已删除了自定义扩展用户属性 **urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department** 和 **urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id**。

## <a name="more-resources"></a>更多资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)


