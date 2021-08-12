---
title: 教程：使用 Azure Active Directory 为 Zscaler Beta 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Zscaler Beta 和取消其预配。
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
ms.openlocfilehash: 8c93eb34fe577684c212c0f22b5b724a7e3ac247
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760492"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>教程：为 Zscaler Beta 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Zscaler Beta 以及取消其预配需在 Zscaler Beta 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>


## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

* Azure AD 租户
* Zscaler Beta 租户
* Zscaler Beta 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于 Zscaler Beta SCIM API，该 SCIM API 可供拥有企业版套餐的帐户的 Zscaler Beta 开发人员使用。

## <a name="adding-zscaler-beta-from-the-gallery"></a>从库中添加 Zscaler Beta

在使用 Azure AD 为 Zscaler Beta 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Zscaler Beta 添加到托管的 SaaS 应用程序列表。

**要从 Azure AD 应用程序库中添加 Zscaler Beta，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Zscaler Beta”，在结果面板中选择“Zscaler Beta”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Zscaler Beta](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>将用户分配到 Zscaler Beta

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Zscaler Beta。 确定后，可以按照此处的说明将这些用户和/或组分配到 Zscaler Beta：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>有关将用户分配到 Zscaler Beta 的重要提示

* 建议将单个 Azure AD 用户分配到 Zscaler Beta 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Zscaler Beta，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>配置 Zscaler Beta 的自动用户预配

本部分逐步介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Zscaler Beta 中创建、更新以及禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Zscaler Beta 单一登录教程](zscaler-beta-tutorial.md)中提供的说明为 Zscaler Beta 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 预配或取消预配用户和组时，我们建议定期重启预配，以确保正确更新组成员身份。 执行重启操作将强制我们的服务重新评估所有组并更新成员身份。  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>要在 Azure AD 中为 Zscaler Beta 配置自动用户预配，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，依次选择“企业应用程序”、“所有应用程序”和“Zscaler Beta”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler Beta”。

    ![应用程序列表中的 Zscaler Beta 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![随即出现一个按类别排列的选项卡列表，标题为“ZScaler Beta - 预配/企业应用程序”。 “管理”类别的“预配”选项卡处于选中状态。](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”  设置为“自动”  。

    ![“预配模式”下拉列表中的“自动”模式已被选中。 屏幕上显示用于连接到 Zscaler Beta 的 API 的“管理员凭据”字段，以及“测试连接”按钮。](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. 在“管理员凭据”部分中，输入 Zscaler Beta 帐户的租户 URL 和机密令牌，如步骤 6 中所述  。

6. 若要获取租户 URL 和机密令牌，请在 Zscaler Beta 门户的用户界面中导航到“管理”>“身份验证设置”，然后在“身份验证类型”下单击“SAML”    。

    ![在“身份验证设置”的“身份验证配置文件”中，所选的“目录类型”为“托管数据库”，所选的“身份验证类型”为“SAML”。](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    单击“配置 SAML”打开“配置 SAML”选项 。

    ![在“配置 SAML”上，“启用 SAML 自动预配”和“启用基于 SCIM 的预配”选项已被选中。 “基 URL”和“持有者令牌”文本框已突出显示。](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    选择“启用基于 SCIM 的预配”以检索基 URL 和持有者令牌，然后保存设置  。 在 Azure 门户中，将基 URL 复制到“租户 URL”，并将持有者令牌复制到“机密令牌”   。

7. 填入步骤 5 中所示的字段后，单击“测试连接”以确保 Azure AD 可以连接到 Zscaler Beta。 如果连接失败，请确保 Zscaler Beta 帐户具有管理员权限，然后重试。

    ![在“管理员凭据”上，“租户 URL”和“机密令牌”字段中有值，且“测试连接”按钮已突出显示。](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![“通知电子邮件”文本框为空，并且“发生故障时发送电子邮件通知”复选框已被清除。](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. 单击“保存”  。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Zscaler Beta” 。

    ![“将 Azure Active Directory 用户同步到 ZScalerBeta”已选择或启用。](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Zscaler Beta 的用户属性。 选为“匹配”属性的特性用于匹配 Zscaler Beta 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![在用户属性的“属性映射”部分中，Active Directory 属性将显示在与之同步的 Zscalar Beta 属性旁边。 一对属性显示为“匹配”。](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Zscaler Beta” 。

    ![“将 Azure Active Directory 组同步到 ZScalerBeta”已选择或启用。](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. 在“属性映射”部分中，查看从 Azure AD 同步到 Zscaler Beta 的组属性。 选为“匹配”属性的特性用于匹配 Zscaler Beta 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![在组属性的“属性映射”部分中，Active Directory 属性将显示在与之同步的 Zscalar Beta 属性旁边。 一对属性显示为“匹配”。](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 若要为 Zscaler Beta 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![“设置状态”将显示并设置为“开”。](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Zscaler Beta 的用户和/或组 。

    ![“范围”下拉列表将显示，且“仅同步已分配的用户和组”为选中状态。 其他可用值为“同步所有用户和组”。](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. 已准备好预配时，单击“保存”  。

    ![Zscaler Beta 顶部的“保存”按钮 - “预配”已突出显示。 还有一个“放弃”按钮。](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Zscaler Beta 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png