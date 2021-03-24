---
title: 教程：在 Azure Active Directory 中配置 Zendesk 实现自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Zendesk 和解除其预配。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359708"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>教程：配置 Zendesk 实现自动用户预配

本教程演示要将 Azure AD 配置为自动将用户和组预配到 Zendesk 以及解除其预配，需在 Zendesk 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务基础上构建的连接器。 有关此服务的功能、工作原理以及常见问题的信息，请参阅[使用 Azure Active Directory 自动将用户预配到软件即服务 (SaaS) 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你具有：

* Azure AD 租户。
* 已启用专业计划或更佳计划的 Zendesk 租户。
* 在 Zendesk 中具有管理员权限的用户帐户。

## <a name="add-zendesk-from-the-azure-marketplace"></a>从 Azure 市场添加 Zendesk

在使用 Azure AD 为 Zendesk 配置自动用户预配之前，请将 Zendesk 从 Azure 市场添加到托管 SaaS 应用程序列表。

若要从市场添加 Zendesk，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择“Azure Active Directory”。

    ![Azure Active Directory 图标](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 Zendesk，并从结果面板中选择 Zendesk。 若要添加该应用程序，请选择“添加”。

    ![结果列表中的 Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>将用户分配到 Zendesk

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户或组需要访问 Zendesk。 若要将这些用户或组分配到 Zendesk，请按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明进行操作。

### <a name="important-tips-for-assigning-users-to-zendesk"></a>将用户分配到 Zendesk 的重要提示

* 现在，Zendesk 角色将在 Azure 门户 UI 中自动动态填充。 在将 Zendesk 角色分配给用户之前，请确保针对 Zendesk 完成初始同步，以检索 Zendesk 租户中的最新角色。

* 建议将单个 Azure AD 用户分配到 Zendesk，以测试初始自动用户预配配置。 可以在测试成功后再分配其他用户或组。

* 将用户分配到 Zendesk 时，请在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>配置 Zendesk 的自动用户预配 

本部分将指导你完成配置 Azure AD 预配服务的步骤。 使用它可以根据 Azure AD 中的用户或组分配在 Zendesk 中创建、更新和禁用用户或组。

> [!TIP]
> 还可以为 Zendesk 启用基于 SAML 的单一登录。 请按照 [Zendesk 单一登录教程](zendesk-tutorial.md)中的说明进行操作。 可独立于自动用户预配来配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>在 Azure AD 中为 Zendesk 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序” > “Zendesk”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zendesk”  。

    ![应用程序列表中的 Zendesk 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![Zendesk 预配](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. 将“预配模式”  设置为“自动”  。

    ![Zendesk 预配模式](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 在“管理员凭据”部分下，输入 Zendesk 帐户的管理员用户名、机密令牌和域。 这些值的示例如下：

   * 在“管理员用户名”框中，填入 Zendesk 租户的管理员帐户的用户名。 例如 admin@contoso.com。

   * 在“机密令牌”框中，填入步骤 6 中所述的机密令牌。

   * 在“域”框中，填入 Zendesk 租户的子域。 例如，对于租户 URL 为 `https://my-tenant.zendesk.com` 的帐户，子域为 my-tenant。

6. Zendesk 帐户的机密令牌位于“管理” > “API” > “设置”中  。 确保“令牌访问”设置为“已启用” 。

    ![Zendesk 管理设置](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk 机密令牌](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 填写步骤 5 中所示的框后，选择“测试连接”以确保 Azure AD 可以连接到 Zendesk。 如果连接失败，请确保 Zendesk 帐户具有管理员权限，然后重试。

    ![Zendesk 测试连接](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. 在“通知电子邮件”框中，输入要接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![Zendesk 通知电子邮件](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Zendesk” 。

    ![Zendesk 用户同步](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Zendesk 的用户属性。 选为“匹配”属性的特性用于匹配 Zendesk 中的用户帐户以执行更新操作。 若要保存任何更改，请选择“保存”。

    ![Zendesk 匹配用户属性](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Zendesk” 。

    ![Zendesk 组同步](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. 在“属性映射”部分中，查看从 Azure AD 同步到 Zendesk 的组属性。 选为“匹配”属性的特性用于匹配 Zendesk 中的组以执行更新操作。 若要保存任何更改，请选择“保存”。

    ![Zendesk 匹配组属性](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

15. 若要为 Zendesk 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用” 。

    ![Zendesk 预配状态](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. 定义要预配到 Zendesk 的用户或组。 在“设置”部分的“范围”中，选择所需的值。

    ![Zendesk 范围](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 准备好预配时，选择“保存”。

    ![Zendesk 保存](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

此操作会对“设置”部分的“范围”中定义的所有用户或组启动初始同步 。 初始同步所需的时间比后续同步要长。 只要运行 Azure AD 预配服务，大约每 40 分钟就会发生一次同步。 

可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务对 Zendesk 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Zendesk 仅支持对“代理”角色的用户使用组。 有关详细信息，请参阅 [Zendesk 文档](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)。

* 将自定义角色分配给用户或组时，Azure AD 自动用户预配服务还将分配默认角色“代理”。 只能为“代理”分配自定义角色。 有关详细信息，请参阅 [Zendesk API 文档](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)。 

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
