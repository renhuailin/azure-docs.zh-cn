---
title: 教程：使用 Azure Active Directory 为 Netskope User Authentication 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Netskope User Authentication 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: thwimmer
ms.openlocfilehash: 25f665c0750650c395713e1c7f1a8ad3240b7615
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769174"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>教程：为 Netskope User Authentication 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Netskope User Authentication 以及取消其预配需在 Netskope User Authentication 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Netskope User Authentication 租户](https://www.netskope.com/)
* Netskope User Authentication 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-netskope-user-authentication"></a>将用户分配到 Netskope User Authentication

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Netskope User Authentication。 确定后，可以按照此处的说明将这些用户和/或组分配到 Netskope User Authentication：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>将用户分配到 Netskope User Authentication 的重要提示

* 建议将单个 Azure AD 用户分配到 Netskope User Authentication 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Netskope User Authentication，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>设置 Netskope User Authentication 以进行预配

1. 登录 [Netskope User Authentication 管理员控制台](https://netskope.goskope.com/)。 导航到“主页”>“设置”。

    ![Netskope User Authentication 管理控制台](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  导航到“工具”。 在“工具”菜单下，导航到“目录工具”>“SCIM 集成” 。

    ![Netskope User Authentication 工具](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope User Authentication 添加 SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. 向下滚动并单击“添加令牌”按钮。 在“添加 OAuth 客户端名称”对话框中，提供客户端名称并单击“保存”按钮  。

    ![Netskope User Authentication 添加令牌](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope User Authentication 客户端名称](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  复制 SCIM 服务器 URL 和令牌 。 在 Azure 门户的 Netskope User Authentication 应用程序的“预配”选项卡中，这些值将被分别输入“租户 URL”和“机密令牌”字段。

    ![Netskope User Authentication 创建令牌](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>从库中添加 Netskope 用户身份验证

在使用 Azure AD 为 Netskope User Authentication 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Netskope User Authentication 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Netskope User Authentication，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入“Netskope User Authentication”，在结果面板中选择“Netskope User Authentication”，然后单击“添加”按钮以添加该应用程序  。

    ![结果列表中的 Netskope User Authentication](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>配置 Netskope User Authentication 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Netskope User Authentication 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Netskope User Authentication 单一登录教程](./netskope-cloud-security-tutorial.md)中提供的说明为 Netskope User Authentication 启用基于 SAML 的单一登录。 可独立于自动用户预配来配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 若要了解有关 Netskope User Authentication 的 SCIM 终结点的详细信息，请参阅[本文](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)。

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>若要在 Azure AD 中为 Netskope User Authentication 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“Netskope 用户身份验证”  。

    ![“应用程序”列表中的 Netskope User Authentication 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入之前检索到的 SCIM 服务器 URL 值  。 在“机密令牌”中，输入之前检索到的令牌值 。 单击“测试连接”，以确保 Azure AD 可以连接到 Netskope User Authentication。 如果连接失败，请确保 Netskope User Authentication 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Netskope User Authentication” 。

    ![Netskope User Authentication 用户映射](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Netskope User Authentication 的用户属性。 选为“匹配”属性的特性用于匹配 Netskope User Authentication 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![Netskope User Authentication 用户属性](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Netskope User Authentication” 。

    ![Netskope User Authentication 组映射](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Netskope User Authentication 的组属性。 选为“匹配”属性的特性用于匹配 Netskope User Authentication 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![Netskope User Authentication 组属性](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 Netskope User Authentication 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Netskope User Authentication 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Netskope User Authentication 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)