---
title: 教程：使用 Azure Active Directory 为 Visitly 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Visitly 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: thwimmer
ms.openlocfilehash: 2870aa2ab4d3a35c0bbbeae3fd3c2565db8f3035
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766820"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>教程：为 Visitly 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户或组预配到 Visitly 以及取消其预配需在 Visitly 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详情，请参阅[使用 Azure Active Directory 自动将用户预配到软件即服务 (SaaS) 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Visitly 租户](https://www.visitly.io/pricing/)
* Visitly 中具有管理员权限的用户帐户

## <a name="assign-users-to-visitly"></a>将用户分配到 Visitly 

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户或组需要访问 Visitly。 然后按照此处的说明将这些用户或组分配到 Visitly：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>将用户分配到 Visitly 的重要提示 

* 建议将单个 Azure AD 用户分配到 Visitly，以测试自动用户预配配置。 其他用户或组可以稍后分配。

* 将用户分配到 Visitly 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-visitly-for-provisioning"></a>设置 Visitly 以进行预配

通过 Azure AD 为 Visitly 配置自动用户预配之前，需要在 Visitly 上启用跨域身份管理系统 (SCIM) 预配。

1. 登录到 [Visitly](https://app.visitly.io/login)。 选择“集成” > “主机同步” 。

    ![主机同步](media/Visitly-provisioning-tutorial/login.png)

2. 选择“Azure AD”部分。

    ![“Azure AD”部分](media/Visitly-provisioning-tutorial/integration.png)

3. 复制“API 密钥”。 将在 Azure 门户中 Visitly 应用程序“预配”选项卡的“机密令牌”框输入这些值 。

    ![API 密钥](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>从库中添加 Visitly

若要使用 Azure AD 为 Visitly 配置自动用户预配，将 Visitly 从 Azure AD 应用程序库添加到托管的 SaaS 应用程序列表。

若要从 Azure AD 应用程序库添加 Visitly，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 Visitly，在结果面板中选择“Visitly”，然后选择“添加”以添加该应用程序  。

    ![结果列表中的 Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>配置 Visitly 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配在 Visitly 中创建、更新以及禁用用户或组。

> [!TIP]
> 若要为 Visitly 启用基于 SAML 的单一登录，请按照 [Visitly 单一登录教程](Visitly-tutorial.md)中的说明进行操作。 可独立于自动用户预配来配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>在 Azure AD 中为 Visitly 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序” 。

    ![所有应用程序](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Visitly”。

    ![应用程序列表中的 Visitly 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![将“预配模式”设置为“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，分别在“租户 URL”和“机密令牌”中输入之前检索到的 `https://api.visitly.io/v1/usersync/SCIM` 和 API 密钥值  。 选择“测试连接”以确保 Azure AD 可以连接到 Visitly。 如果连接失败，请确保 Visitly 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址。 选中“发生故障时发送电子邮件通知”复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Visitly” 。

    ![Visitly 用户映射](media/visitly-provisioning-tutorial/usermapping.png)

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Visitly 的用户属性。 选为“匹配”属性的特性用于匹配 Visitly 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。

    ![Visitly 用户属性](media/visitly-provisioning-tutorial/userattribute.png)

10. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

11. 若要为 Visitly 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![“预配状态”已切换为“启用”](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Visitly 的用户或组 。

    ![预配范围](common/provisioning-scope.png)

13. 准备好预配时，选择“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户或组启动初始同步 。 初始同步所需的时间比后续同步要长。 有关预配用户或组所需时间的详细信息，请参阅[预配用户需要多长时间](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

可使用“当前状态”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Visitly 执行的所有操作。 有关详细信息，请参阅[检查用户预配状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

Visitly 不支持硬删除。 只可软删除任意内容。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
