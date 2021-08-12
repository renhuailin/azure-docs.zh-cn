---
title: 教程：使用 Azure Active Directory 为 RFPIO 配置自动用户设置 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 来使其自动将用户帐户预配到 RFPIO 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: thwimmer
ms.openlocfilehash: c42519dc16343ef7d4eb39d83c005d82f8bdb1ae
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766251"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>教程：为 RFPIO 配置自动用户设置

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 RFPIO 以及解除其预配，需在 RFPIO 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [RFPIO 租户](https://www.rfpio.com/product/)。
* 在 RFPIO 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-rfpio"></a>将用户分配到 RFPIO

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

配置和启用自动用户设置之前，应确定 Azure AD 中的哪些用户和/或组需要访问 RFPIO。 确定后，可以按照此处的说明将这些用户和/或组分配到 RFPIO：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>有关将用户分配到 RFPIO 的重要提示

* 建议将单个 Azure AD 用户分配到 RFPIO，以测试自动用户设置配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 RFPIO 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="setup-rfpio-for-provisioning"></a>设置 RFPIO 以进行预配

使用 Azure AD 为 RFPIO 配置自动用户设置之前，需要在 RFPIO 上启用 SCIM 预配。

1.  登录到 RFPIO 管理控制台。 在管理控制台的左下角，单击“租户”。

    ![RFPIO 管理控制台](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  单击“组织设置”。
    
    ![RFPIO 管理员](media/rfpio-provisioning-tutorial/aadtest.png)

3.  导航到“用户管理” > “安全性” > “SCIM”  。

    ![RFPIO 添加 SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  请确保已启用“自动用户设置”。 单击“生成 SCIM API 令牌”。

    ![SCIM 部分的屏幕截图，其中标注了“生成 SCIM API 令牌”选项。](media/rfpio-provisioning-tutorial/generate.png)

5.  保存 SCIM API 令牌，因为出于安全考虑，此令牌将不再显示。 在 Azure 门户的 RFPIO 应用程序的“预配”选项卡中，将此值输入“机密令牌”字段。

    ![SCIM 部分的屏幕截图，其中包含选择“提交”后出现的“警告”对话框。](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>从库中添加 RFPIO

若要使用 Azure AD 为 RFPIO 配置自动用户设置，需要从 Azure AD 应用程序库将 RFPIO 添加到托管的 SaaS 应用程序列表。

若要从 Azure AD 应用程序库添加 RFPIO，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 RFPIO，在结果面板中选择“RFPIO”，然后单击“添加”按钮添加该应用程序  。

    ![结果列表中的 RFPIO](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>配置 RFPIO 的自动用户设置 

本部分介绍如何配置 Azure AD 预配服务，以便根据 Azure AD 中的用户和/或组分配在 RFPIO 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [RFPIO 单一登录教程](rfpio-tutorial.md)中提供的说明为 RFPIO 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>在 Azure AD 中为 RFPIO 配置自动用户设置：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“RFPIO”  。

    ![应用程序列表中的 RFPIO 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` 。 示例值如下：`https://Azure-test1.rfpio.com/rfpserver/scim/v2`。 在“机密令牌”中，输入之前检索到的“SCIM API 令牌”值 。 单击“测试连接”，确保 Azure AD 可连接到 RFPIO。 如果连接失败，请确保 RFPIO 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 RFPIO” 。

    ![RFPIO 用户映射](media/rfpio-provisioning-tutorial/usermapping.png)

9. 在“特性映射”部分中，查看从 Azure AD 同步到 RFPIO 的用户特性。 选为“匹配”属性的特性用于匹配 RFPIO 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![RFPIO 用户特性](media/rfpio-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 RFPIO 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 RFPIO 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 RFPIO 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* RFPIO 目前不支持组预配。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
