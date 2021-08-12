---
title: 教程：使用 Azure Active Directory 为 Elium 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Elium 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: thwimmer
ms.openlocfilehash: 6c97bd16978295b34e671a0af2598869f0218993
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767970"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教程：为 Elium 配置自动用户预配

本教程介绍如何配置 Elium 和 Azure Active Directory (Azure AD) 来使其自动为 Elium 预配和取消预配用户或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务基础上构建的连接器。 有关此服务的功能、工作原理的重要详细信息以及常见问题解答，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动化用户预配和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关 Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程假设你已具有以下先决条件：

* Azure AD 租户
* [Elium 租户](https://www.elium.com/pricing/)
* Elium 中具有管理员权限的用户帐户

## <a name="assigning-users-to-elium"></a>将用户分配到 Elium

Azure AD 使用称为“分配”的概念来确定哪些用户收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户和组需要访问 Elium。 然后，按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的步骤将这些用户和组分配到 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>有关将用户分配到 Elium 的重要提示 

建议将单个 Azure AD 用户分配到 Elium，以测试自动用户预配配置。 稍后可分配更多用户和组。

如果将用户分配到 Elium，必须在分配对话框中选择应用程序特定的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-elium-for-provisioning"></a>设置 Elium 以进行预配

通过 Azure AD 为 Elium 配置自动用户预配之前，需要在 Elium 上启用跨域身份管理系统 (SCIM) 预配。 执行以下步骤:

1. 登录到 Elium，然后转到“我的个人资料” > “设置” 。

    ![Elium 中的“设置”菜单项](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角的“高级”下，选择“安全” 。

    ![Elium 中的安全链接](media/Elium-provisioning-tutorial/security.png)

1. 复制“租户 URL”和“机密令牌”值 。 稍后你将在 Azure 门户 Elium 应用程序的“预配”选项卡的相应字段中使用这些值。

    ![Elium 中的“租户 URL”和“机密令牌”字段](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>从库中添加 Elium

若要使用 Azure AD 为 Elium 配置自动用户预配，必须从 Azure AD 应用程序库将 Elium 添加到托管的软件即服务 (SaaS) 应用程序列表。 执行以下步骤:

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory”。

    ![Azure Active Directory 菜单项](common/select-azuread.png)

1. 转到“企业应用程序”，并选择“所有应用程序”。 

     ![Azure AD 企业应用程序边栏选项卡](common/enterprise-applications.png)

1. 若要添加新应用程序，请在窗格顶部选择“新建应用程序”。

    ![新建应用程序链接](common/add-new-app.png)

1. 在搜索框中键入“Elium”，在结果列表中选择“Elium”，然后选择“添加”以添加该应用程序  。

    ![库搜索框](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>配置到 Elium 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和组分配在 Elium 中创建、更新以及禁用用户和组。

> [!TIP]
> 还可以遵循 [Elium 单一登录教程](Elium-tutorial.md)中的说明，基于安全断言标记语言 (SAML) 选择为 Elium 启用单一登录。 可以独立于自动用户预配来配置单一登录，尽管这两个功能可互相补充。

若要在 Azure AD 中为 Elium 配置自动用户预配，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，依次选择“企业应用程序”、“所有应用程序” 。

    ![Azure AD 企业应用程序边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Elium”。

    ![“企业应用程序”边栏选项卡中的应用程序列表](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![“企业应用程序”边栏选项卡中的“预配”选项卡](common/provisioning.png)

1. 将“预配模式”  设置为“自动”  。

    ![“预配模式”的自动设置](common/provisioning-automatic.png)

1. 在“管理员凭据”部分中，在“租户 URL”字段中键入“\<tenantURL\>/scim/v2”  。 （tenantURL 是之前从 Elium 管理控制台中检索到的值。）此外，在“机密令牌”字段中键入 Elium 的“机密令牌”值 。 最后，选择“测试连接”以验证 Azure AD 可以连接到 Elium。 如果连接失败，请确保 Elium 帐户具有管理员权限，然后重试。

    ![“管理员凭据”中的“租户 URL”和“机密令牌”字段](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”字段中输入会接收预配错误通知的人员或组的电子邮件地址。 然后，选中“发生故障时发送电子邮件通知”复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 单击“ **保存**”。

1. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Elium” 。

    ![同步将 Azure AD 用户映射到 Elium 的链接](media/Elium-provisioning-tutorial/usermapping.png)

1. 在“属性映射”部分中，查看从 Azure AD 同步到 Elium 的用户属性。 选为“匹配”属性的特性用于匹配 Elium 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。

    ![Azure AD 和 Elium 之间的属性映射](media/Elium-provisioning-tutorial/userattribute.png)

1. 若要配置范围筛选器，请按照[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明进行操作。

1. 要为 Elium 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“开”  。

    ![“预配状态”设置为“开”](common/provisioning-toggle-on.png)

1. 在“设置”部分的“范围”下拉列表框中选择所需的值，定义要预配到 Elium 的用户和组 。

    ![预配范围列表框](common/provisioning-scope.png)

1. 准备好预配时，选择“保存”。

    ![预配配置的“保存”按钮](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步 。 此初始同步进程所用的时间比后续同步要长。 有关预配所需时间的详细信息，请参阅[预配用户需要多长时间？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

使用“当前状态”部分监视进程并跟踪指向预配活动报告的链接。 此预配活动报告描述了 Azure AD 预配服务对 Elium 执行的所有操作。 有关详细信息，请参阅[检查用户预配状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
