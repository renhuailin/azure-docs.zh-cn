---
title: 教程：使用 Azure Active Directory 为 Peakon 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Peakon 和取消其预配。
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
ms.openlocfilehash: 65416e8e0117f91d97374ae10fbc633d1783b823
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766508"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>教程：为 Peakon 配置自动用户预配

本教程的目的是演示需要执行哪些步骤在 Peakon 和 Azure Active Directory (Azure AD) 中配置 Azure AD 来使其自动将用户和/或组预配到 Peakon 以及对其取消预配。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有下列必备组件

* Azure AD 租户。
* [Peakon 租户](https://peakon.com/us/pricing/)。
* 在 Peakon 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-peakon"></a>将用户分配到 Peakon

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Peakon。 确定后，可按照此处的说明将这些用户和/或组分配到 Peakon：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>有关将用户分配到 Peakon 的重要提示 

* 建议将单个 Azure AD 用户分配到 Peakon 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Peakon，必须在分配对话框中选择应用程序特定的所有有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-peakon-for-provisioning"></a>设置 Peakon 以进行预配

1.  登录到 [Peakon 管理控制台](https://app.Peakon.com/login)。 单击“配置”。 

    ![Peakon 管理控制台](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  选择“集成”。
    
    ![“配置”选项的屏幕截图，其中调出了“集成”选项。](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  启用“员工预配”。

    ![“员工预配”部分的屏幕截图，其中调出了“启用”选项。](media/Peakon-provisioning-tutorial/peakon05.png)

4.  复制 SCIM 2.0 URL 和 OAuth 持有者令牌的值 。 在 Azure 门户的 Peakon 应用程序的“预配”选项卡中，将这些值输入“租户 URL”和“机密令牌”字段 。

    ![Peakon 创建令牌](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>从库中添加 Peakon

若要使用 Azure AD 为 Peakon 配置自动用户预配，需要从 Azure AD 应用程序库将 Peakon 添加到托管的 SaaS 应用程序列表。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 Peakon，在结果面板中选择 Peakon，然后单击“添加”按钮来添加该应用程序  。

    ![结果列表中的 Peakon](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>配置到 Peakon 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Peakon 中创建、更新以及禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Peakon 单一登录教程](peakon-tutorial.md)中提供的说明为 Peakon 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>若要在 Azure AD 中为 Peakon 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Peakon”。

    ![应用程序列表中的 Peakon 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，分别在“租户 URL”和“机密令牌”中输入之前检索到的 SCIM 2.0 URL 和 OAuth 持有者令牌值    。 单击“测试连接”，确保 Azure AD 可连接到 Peakon。 如果连接失败，请确保 Peakon 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Peakon” 。

    ![Peakon 用户映射](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. 在“特性映射”部分中，查看从 Azure AD 同步到 Peakon 的用户特性你。 选为“匹配”属性的特性用于匹配 Peakon 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![Peakon 用户特性](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。
    
    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Peakon 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* 必须从 Peakon 的自定义 SCIM 用户扩展 `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` 来扩展 Peakon 中的所有自定义用户特性。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)