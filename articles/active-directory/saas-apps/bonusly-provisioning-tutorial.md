---
title: 教程：使用 Azure Active Directory 为 Bonusly 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Bonusly 和取消其预配。
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
ms.openlocfilehash: bf14a381f58b484fd45060ed354b418d3f94d195
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764687"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>教程：为 Bonusly 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Bonusly 以及取消其预配需在 Bonusly 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

* Azure AD 租户
* [Bonusly 租户](https://bonus.ly/pricing)
* Bonusly 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于可供 Bonusly 开发人员使用的 [Bonusly Rest API](https://konghq.com/solutions/gateway/)。

## <a name="adding-bonusly-from-the-gallery"></a>从库中添加 Bonusly

在使用 Azure AD 为 Bonusly 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Bonusly 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Bonusly，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Bonusly”，在结果面板中选择“Bonusly”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 Bonusly](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>将用户分配到 Bonusly

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。 

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Bonusly。 确定后，可以按照此处的说明将这些用户和/或组分配到 Bonusly：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>将用户分配到 Bonusly 的重要提示

* 建议将单个 Azure AD 用户分配到 Bonusly 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Bonusly，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>配置 Bonusly 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Bonusly 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Bonusly 单一登录教程](bonus-tutorial.md)中提供的说明为 Bonusly 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>若要在 Azure AD 中为 Bonusly 配置自动用户预配，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，依次选择 **企业应用程序**、**所有应用程序** 和 **Bonusly**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Bonusly”  。

    ![应用程序列表中的 Bonusly 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="“Bonusly - 预配”选项卡的屏幕截图。在“管理”下，突出显示了“预配”。" border="false":::

4. 将“预配模式”  设置为“自动”  。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="显示“预配模式”列表框的屏幕截图，其中选中并突出显示“自动”。" border="false":::

5. 在“管理员凭据”部分下，按照步骤 6 中的说明输入 Bonusly 帐户的 **机密令牌**。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="“管理员凭据”部分的屏幕截图。“机密令牌”框为空，但已突出显示。" border="false":::

6. Bonusly 帐户的 **机密令牌** 位于“管理”>“公司”>“集成”中。 在“如果要编码”部分中，单击“API”>“新建 API 访问令牌”以创建新的机密令牌。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Bonusly 菜单的屏幕截图。在“管理”下，突出显示“公司”。在“公司”下，突出显示“集成”。" border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Bonusly 站点的“如果要编码”部分的屏幕截图，其中突出显示 A P I。" border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Bonusly 站点的屏幕截图。“服务”选项卡打开。在您的 A P I 访问令牌下，突出显示“创建新的 A P I 访问令牌”。" border="false":::

7. 在以下屏幕上，在提供的文本框中键入访问令牌的名称，然后按“创建 Api 密钥”。 新的访问令牌将在弹出窗口中显示几秒钟。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Bonusly 站点的“新访问令牌”页的屏幕截图。一个未标记的框包含“我的令牌”，并且突出显示“创建 A P I 密钥”按钮。" border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Bonusly 站点的屏幕截图。将显示一条通知，显示新创建的访问令牌，后跟一个难以辨认的令牌。" border="false":::

8. 填入步骤 5 中所示的字段后，单击“测试连接”以确保 Azure AD 可以连接到 Bonusly。 如果连接失败，请确保 Bonusly 帐户具有管理员权限，然后重试。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Azure 门户的“管理员凭据”部分的屏幕截图。突出显示“文本连接”按钮。" border="false":::

9. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="显示空“通知电子邮件”框的屏幕截图。显示一个标有“发生故障时发送一封电子邮件通知”的选项。" border="false":::

10. 单击“保存”  。

11. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Bonusly”。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="“映射”部分的屏幕截图。在“名称”下，突出显示了“将 Azure Active Directory 用户同步到 Bonusly”。" border="false":::

12. 在“属性映射”部分中，查看从 Azure AD 同步到 Bonusly 的用户属性。 选为“匹配”属性的特性用于匹配 Bonusly 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="“属性映射”页的屏幕截图。表列出了 Azure Active Directory 特性、相应的 Bonusly 特性以及匹配的状态。" border="false":::

13. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

14. 若要为 Bonusly 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="“设置”部分的屏幕截图。“预配状态”切换设置为“关”。" border="false":::

15. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Bonusly 的用户和/或组。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="显示“作用域”列表框的屏幕截图。已选择框中的“仅同步分配的用户和组”。" border="false":::

16. 已准备好预配时，单击“保存”  。

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="“Bonusly - 预配”页的屏幕截图，其中突出显示了“保存”按钮。" border="false":::

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Bonusly 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png