---
title: 教程：使用 Azure Active Directory 为 Federated Directory 配置自动用户预配 | Microsoft 文档
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Federated Directory 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: thwimmer
ms.openlocfilehash: 14560565b3d1d0f12bea1e74a164a7596cfc8b7a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767986"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>教程：为 Federated Directory 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Federated Directory 以及取消其预配需在 Federated Directory 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [Federated Directory 租户](https://www.federated.directory/pricing)。
* Federated Directory 中具有管理员权限的用户帐户。

## <a name="assign-users-to-federated-directory"></a>将用户分配到 Federated Directory
Azure Active Directory 使用“分配”这一概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Federated Directory。 确定后，可以按照此处的说明将这些用户和/或组分配到 Federated Directory：

 * [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>有关将用户分配到 Federated Directory 的重要提示
 * 建议将单个 Azure AD 用户分配到 Federated Directory 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Federated Directory，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>设置 Federated Directory 以进行预配

使用 Azure AD 为 Federated Directory 配置自动用户预配之前，需要在 Federated Directory 上启用 SCIM 预配。

1. 登录 [Federated Directory 管理控制台](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Federated Directory 管理控制台的屏幕截图，其中显示了用于输入公司名称的字段。还显示了“登录”按钮。" border="false":::

2. 导航到“目录”>“用户目录”，选择租户。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text=" 管理控制台的屏幕截图，其中突出显示了目录和 Federated Directory Azure A D 测试。" border="false":::

3.  若要生成永久持有者令牌，请导航到“目录密钥”>“创建新密钥”。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Federated Directory 管理控制台的“目录密钥”页的屏幕截图。其中突出显示了“创建新密钥”按钮。" border="false":::

4. 创建目录密钥。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Federated Directory 管理控制台的“创建目录密钥”页的屏幕截图，其中包含“名称”和“说明”字段以及“创建密钥”按钮。" border="false":::
    

5. 复制“访问令牌”  值。 在 Azure 门户的 Federated Directory 应用程序的“预配”选项卡中，该值将被输入“机密令牌”字段。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Federated Directory 管理控制台中页面的屏幕截图。其中显示了访问令牌占位符和密钥名称、说明和颁发者。" border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>从库中添加 Federated Directory

若要使用 Azure AD 为 Federated Directory 配置自动用户预配，需要从 Azure AD 应用程序库将 Federated Directory 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Federated Directory，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入“Federated Directory”，在结果面板中选择“Federated Directory” 。

    ![结果列表中的 Federated Directory](common/search-new-app.png)

5. 导航到下面单独浏览器中突出显示的 URL。 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Azure 门户中显示 Federated Directory 相关信息的页面的屏幕截图。其中突出显示了 U R L 值。" border="false":::

6. 单击“登录”。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text=" 站点上主菜单的屏幕截图。其中突出显示了“登录”按钮。" border="false":::

7.  由于 Federated Directory 是 OpenIDConnect 应用，因此请选择使用 Microsoft 工作帐户登录 Federated Directory。
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Federated Directory 站点上 S C I M A D 测试页面的屏幕截图。其中突出显示了使用 Microsoft 帐户登录。" border="false":::
 
8. 身份验证成功后，请接受同意页面的同意提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 Federated Directory 帐户。

    ![Federated Directory 添加 SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>配置 Federated Directory 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Federated Directory 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>若要在 Azure AD 中为 Federated Directory 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Federated Directory”。

    ![“应用程序”列表中的 Federated Directory 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，在“租户 URL”中输入 `https://api.federated.directory/v2/`。 在“机密令牌”中，输入先前从 Federated Directory 检索到并保存的值。 单击“测试连接”以确保 Azure AD 可以连接到 Federated Directory。 如果连接失败，请确保 Federated Directory 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“ **保存**”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Federated Directory” 。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="“映射”部分的屏幕截图。其中突出显示了“名称”下的“将 Azure Active Directory 用户同步到 Federated Directory”。" border="false":::
    
    
11. 在“属性映射”部分中，查看从 Azure AD 同步到 Federated Directory 的用户属性。 选为“匹配”属性的特性用于匹配 Federated Directory 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="“属性映射”页的屏幕截图。表列出了 Azure Active Directory 和 Federated Directory 属性以及匹配状态。" border="false":::
    

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Federated Directory 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Federated Directory 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Federated Directory 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
