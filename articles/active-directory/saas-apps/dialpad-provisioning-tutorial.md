---
title: 教程：使用 Azure Active Directory 为 Dialpad 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Dialpad 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: thwimmer
ms.openlocfilehash: e559ff8f99a8cc5869ff200a95d527681bd4774c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113764058"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>教程：为 Dialpad 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Dialpad 以及取消其预配需在 Dialpad 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

> 此连接器目前提供预览版。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [Dialpad 租户](https://www.dialpad.com/pricing/)。
* Dialpad 中具有管理员权限的用户帐户。

## <a name="assign-users-to-dialpad"></a>将用户分配到 Dialpad
Azure Active Directory 使用“分配”这一概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Dialpad。 确定后，可以按照此处的说明将这些用户和/或组分配到 Dialpad：
 
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>将用户分配到 Dialpad 的重要提示

 * 建议将单个 Azure AD 用户分配到 Dialpad 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Dialpad，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="setup-dialpad-for-provisioning"></a>设置 Dialpad 以进行预配

使用 Azure AD 为 Dialpad 配置自动用户预配之前，需要从 Dialpad 检索一些预配信息。

1. 登录到 [Dialpad 管理控制台](https://dialpadbeta.com/login)并选择“管理设置”。 确保从下拉列表中选择“我的公司”。 导航到“身份验证”>“API 密钥”。

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Dialpad 管理控制台的屏幕截图，其中突出显示了设置图标、“我的公司”、“身份验证”和“API 密钥”，并选择了“我的公司”。" border="false":::

2. 单击“添加密钥”并配置机密令牌的属性，以生成新密钥。

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Dialpad 管理控制台中“API 密钥”页的屏幕截图。突出显示了“添加密钥”。" border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Dialpad 管理控制台中“编辑 API 密钥”页的屏幕截图。突出显示了“保存”按钮。" border="false":::

3. 针对最近创建的 API 密钥，单击“单击以显示值”按钮，然后复制显示的值。 将在 Azure 门户中 Dialpad 应用程序的“预配”选项卡的“机密令牌”字段中输入此值。 

    ![Dialpad 创建令牌](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>从库中添加 Dialpad

若要使用 Azure AD 为 Dialpad 配置自动用户预配，需要从 Azure AD 应用程序库将 Dialpad 添加到托管的 SaaS 应用程序列表。

**要从 Azure AD 应用程序库中添加 Dialpad，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 Dialpad，在结果面板中选择“Dialpad” 。
    ![结果列表中的 Dialpad](common/search-new-app.png)

5. 导航到下面单独浏览器中突出显示的 URL。 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="显示有关 Dialpad 应用信息的页面的屏幕截图。在 URL 下，列出并突出显示某个地址。" border="false":::

6. 在右上角，选择“登录”>“联机使用 Dialpad”。

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Dialpad 网站的屏幕截图。突出显示了“登录”，且“登录”选项卡处于打开状态。还突出显示了“联机使用 Dialpad”。" border="false":::

7. 由于 Dialpad 是一个 OpenIDConnect 应用，因此请选择使用你的 Microsoft 工作帐户登录到 Dialpad。

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Dialpad 网站中“开始发起呼叫”页的屏幕截图。突出显示了“使用 Office 365 登录”按钮。" border="false":::

8. 身份验证成功后，请接受同意页面的同意提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 Dialpad 帐户。

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="显示 Microsoft 身份验证页的屏幕截图，指出 Dialpad 应用已请求访问某些数据。突出显示了“接受”按钮。" border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>配置 Dialpad 的自动用户预配

本部分逐步介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Dialpad 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>要在 Azure AD 中为 Dialpad 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Dialpad”。

    ![应用程序列表中的 Dialpad 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://dialpad.com/scim` 。 在“机密令牌”中，输入先前从 Dialpad 检索到并保存的值。 单击“测试连接”以确保 Azure AD 可以连接到 Dialpad。 如果连接失败，请确保 Dialpad 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Dialpad” 。

    ![Dialpad 用户映射](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Dialpad 的用户属性。 选为“匹配”属性的特性用于匹配 Dialpad 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![Dialpad 用户属性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为 Dialpad 启用 Azure AD 预配服务，请在“设置”部分将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Dialpad 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Dialpad 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>连接器限制
* Dialpad 目前不支持组重命名。 这意味着，对 Azure AD 中组 displayName 的任何更改都不会在 Dialpad 中更新和反映。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
