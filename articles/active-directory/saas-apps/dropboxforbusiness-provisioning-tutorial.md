---
title: 教程：使用 Azure Active Directory 为 Dropbox for Business 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Dropbox for Business 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 75ae106ea3f974fab611ace28ac70ece5c0c7d32
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768134"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>教程：为 Dropbox for Business 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Dropbox for Business 以及取消其预配需在 Dropbox for Business 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!IMPORTANT]
> Microsoft 和 Dropbox 以后将弃用旧的 Dropbox 集成。 最初计划在 2021 年 4 月 1 日弃用，但此日期已无限期推迟。 但是，为了避免服务中断，我们建议迁移到支持组的新 SCIM 2.0 Dropbox 集成。 若要迁移到新的 Dropbox 集成，请使用以下步骤添加和配置 Dropbox 的新实例，以便在 Azure AD 租户中进行配置。 配置新的 Dropbox 集成后，请禁用旧 Dropbox 集成上的预配以避免预配冲突。 有关迁移到新 Dropbox 集成的更详细步骤，请参阅[使用 Azure AD 更新到最新的 Dropbox for Business 应用程序](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector)。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [DropBox for Business 租户](https://www.dropbox.com/business/pricing)
* Dropbox for Business 中具有管理员权限的用户帐户。

## <a name="add-dropbox-for-business-from-the-gallery"></a>从库中添加 Dropbox for Business

在使用 Azure AD 为 Dropbox for Business 配置自动用户预配之前，需要将 Dropbox for Business 从 Azure AD 应用程序库添加到托管 SaaS 应用程序的列表。

要从 Azure AD 应用程序库中添加 Dropbox for Business，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入“Dropbox for Business”，从结果面板中选择“Dropbox for Business”，然后单击“添加”按钮添加该应用程序  。

    ![结果列表中的 Dropbox for Business](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>将用户分配到 Dropbox for Business

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Dropbox for Business。 确定后，可以按照此处的说明将这些用户和/或组分配到 Dropbox for Business：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>将用户分配到 Dropbox for Business 的重要提示

* 建议将单个 Azure AD 用户分配到 Dropbox for Business 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Dropbox for Business，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>为 Dropbox for Business 配置自动用户预配 

本部分逐步介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Dropbox for Business 中创建、更新以及禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Dropbox for Business 单一登录教程](dropboxforbusiness-tutorial.md)中提供的说明为 Dropbox for Business 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>要在 Azure AD 中为 Dropbox for Business 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Dropbox for Business”。

    ![应用程序列表中的“Dropbox for Business”链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，单击“授权”。 随即将在新的浏览器窗口中打开 Dropbox for Business 登录对话框。

    ![设置 ](common/provisioning-oauth.png)

6. 在“登录 Dropbox for Business 以链接 Azure AD”对话框中，登录到 Dropbox for Business 租户并验证身份。

    ![Dropbox for Business 登录](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 完成步骤 5 和 6 后，请单击“测试连接”确保 Azure AD 可以连接到 Dropbox for Business。 如果连接失败，请确保 Dropbox for Business 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-oauth.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“ **保存**”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Dropbox” 。

    ![Dropbox 用户映射](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Dropbox 的用户属性。 选为“匹配”属性的特性用于匹配 Dropbox 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Dropbox 用户特性](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Dropbox” 。

    ![Dropbox 组映射](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. 在“特性映射”部分中，查看从 Azure AD 同步到 Dropbox 的组特性。 选为“匹配”属性的特性用于匹配 Dropbox 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Dropbox 组属性](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

15. 若要为 Dropbox 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

16. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Dropbox 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

17. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Dropbox 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制
 
* Dropbox 不支持暂停受邀用户。 如果受邀用户被暂停，则该用户将被删除。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

