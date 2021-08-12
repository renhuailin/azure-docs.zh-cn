---
title: 教程：使用 Azure Active Directory 为 Zscaler One 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Zscaler One 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 74f04491d1fd27fbe99aea8c84a8feb5d9280f2e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760475"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>教程：为 Zscaler One 配置自动用户预配

本教程演示要将 Azure AD 配置为自动将用户和组预配到 Zscaler One 以及解除其预配，需在 Zscaler One 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务基础上构建的连接器。 有关此服务的功能、工作原理以及常见问题的信息，请参阅[使用 Azure Active Directory 自动将用户预配到软件即服务 (SaaS) 应用程序和取消预配](../app-provisioning/user-provisioning.md)。


## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你具有：

* Azure AD 租户。
* Zscaler One 租户。
* Zscaler One 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler One SCIM API。 此 API 可供 Zscaler One 开发人员使用具有企业版包的帐户。

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>从 Azure 市场中添加 Zscaler One

在使用 Azure AD 为 Zscaler One 配置自动用户预配之前，请将 Zscaler One 从 Azure 市场添加到托管 SaaS 应用程序列表。

若要从市场添加 Zscaler One，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择“Azure Active Directory”。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 Zscaler One，然后从结果面板中选择“Zscaler One” 。 若要添加该应用程序，请选择“添加”。

    ![结果列表中的 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>将用户分配到 Zscaler One

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户或组需要访问 Zscaler One。 若要将这些用户或组分配到 Zscaler One，请按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>有关将用户分配到 Zscaler One 的重要提示

* 建议将一个 Azure AD 用户分配到 Zscaler One 以测试自动用户预配配置。 可稍后再分配其他用户或组。

* 将用户分配到 Zscaler One 时，请在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>配置到 Zscaler One 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务的步骤。 使用它可以根据 Azure AD 中的用户或组分配在 Zscaler One 中创建、更新和禁用用户或组。

> [!TIP]
> 还可以为 Zscaler One 启用基于 SAML 的单一登录。 请遵照 [Zscaler One 单一登录教程](zscaler-One-tutorial.md)中的说明操作。 可独立于自动用户预配来配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 预配或取消预配用户和组时，我们建议定期重启预配，以确保正确更新组成员身份。 执行重启操作将强制我们的服务重新评估所有组并更新成员身份。  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>在 Azure AD 中为 Zscaler One 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序” > “Zscaler One”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler One”  。

    ![应用程序列表中的 Zscaler One 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![Zscaler One 预配](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”  设置为“自动”  。

    ![Zscaler One 预配模式](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 在“管理员凭据”部分下，在“租户 URL”和“机密令牌”框中填写如步骤 6 中所述的 Zscaler One 帐户设置  。

6. 若要获取租户 URL 和机密令牌，请转到 Zscaler One 门户 UI 中的“管理” > “身份验证设置”。 在“身份验证类型”下选择“SAML”。

    ![Zscaler One 身份验证设置](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 选择“配置 SAML”打开“配置 SAML”选项 。

    ![Zscaler One 配置 SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 选择“启用基于 SCIM 的预配”以设置“基本 URL”和“持有者令牌”  。 然后保存设置。 将“基 URL”设置复制到 Azure 门户中的“租户 URL” 。 将“持有者令牌”设置复制到 Azure 门户中的“机密令牌” 。

7. 填写步骤 5 中所示的框后，选择“测试连接”以确保 Azure AD 可以连接到 Zscaler One。 如果连接失败，请确保 Zscaler One 帐户具有管理员权限，然后重试。

    ![Zscaler One 测试连接](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 在“通知电子邮件”框中，输入要接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![Zscaler One 通知电子邮件](./media/zscaler-one-provisioning-tutorial/notification.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Zscaler One” 。

    ![Zscaler One 用户同步](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Zscaler One 的用户属性。 选为“匹配”属性的特性用于匹配 Zscaler One 中的用户帐户以执行更新操作。 若要保存任何更改，请选择“保存”。

    ![Zscaler One 匹配用户属性](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Zscaler One” 。

    ![Zscaler One 组同步](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. 在“属性映射”部分中，查看从 Azure AD 同步到 Zscaler One 的组属性。 选为“匹配”属性的特性用于匹配 Zscaler One 中的组以执行更新操作。 若要保存任何更改，请选择“保存”。

    ![Zscaler One 匹配组属性](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

15. 若要为 Zscaler One 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![Zscaler One 预配状态](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. 定义要预配到 Zscaler One 的用户或组。 在“设置”部分的“范围”中，选择所需的值。

    ![Zscaler One 范围](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 准备好预配时，选择“保存”。

    ![Zscaler One 保存](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户或组启动初始同步 。 初始同步所需的时间比后续同步要长。 只要运行 Azure AD 预配服务，大约每 40 分钟就会发生一次同步。 

可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务对 Zscaler One 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png