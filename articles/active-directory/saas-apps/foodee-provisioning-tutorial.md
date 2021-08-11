---
title: 教程：使用 Azure Active Directory 为 Foodee 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Foodee 和取消其预配。
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
ms.openlocfilehash: 74ebece9d3db5fb1d4781ece16492441c351235e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113763974"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>教程：为 Foodee 配置自动用户预配

本文介绍如何在 Foodee 和 Azure AD 中配置 Azure Active Directory (Azure AD) 以自动将用户或组预配到 Foodee 并取消其预配。

> [!NOTE]
> 本文介绍在 Azure AD 用户预配服务基础上构建的连接器。 若要了解此服务的功能、工作原理以及常见问题解答，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动化用户预配和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 若要详细了解 Azure 预览功能的使用条款功能，请转到 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程假设你已满足以下先决条件：

* Azure AD 租户
* [Foodee 租户](https://www.food.ee/about/)
* Foodee 中具有管理员权限的用户帐户

## <a name="assign-users-to-foodee"></a>将用户分配到 Foodee 

Azure AD 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户或组需要访问 Foodee。 确定后，可按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明将这些用户或组分配到 Foodee。

## <a name="important-tips-for-assigning-users-to-foodee"></a>将用户分配到 Foodee 的重要提示 

分配用户时，请记住以下提示：

* 建议仅将单个 Azure AD 用户分配到 Foodee，以测试自动用户预配的配置。 可稍后再分配其他用户或组。

* 将用户分配到 Foodee 时，在“分配”窗格中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-foodee-for-provisioning"></a>设置 Foodee 以进行预配

通过 Azure AD 为 Foodee 配置自动用户预配之前，需要在 Foodee 上启用跨域身份管理系统 (SCIM) 预配。

1. 登录到 [Foodee](https://www.food.ee/login/)，然后选择租户 ID。

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Foodee 企业门户主菜单的屏幕截图。租户 ID 占位符在菜单中可见。" border="false":::

1. 在“企业门户”下选择“单一登录” 。

    ![Foodee 企业门户左窗格菜单](media/Foodee-provisioning-tutorial/scim.png)

1. 复制“API 令牌”框中的值供以后使用。 将在 Azure 门户中 Foodee 应用程序“预配”选项卡的“机密令牌”框输入此值 。

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Foodee 企业门户中页面的屏幕截图。突出显示了 API 令牌值。" border="false":::

## <a name="add-foodee-from-the-gallery"></a>从库中添加 Foodee

若要使用 Azure AD 为 Foodee 配置自动用户预配，需要从 Azure AD 应用程序库将 Foodee 添加到托管的 SaaS 应用程序列表。

若要从 Azure AD 应用程序库添加 Foodee，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”  。

    ![Azure Active Directory 命令](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加新应用程序，请在窗格顶部选择“新建应用程序”。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中输入 Foodee，在结果面板中选择“Foodee”，然后选择“添加”以添加该应用程序  。

    ![结果列表中的 Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>配置 Foodee 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配在 Foodee 中创建、更新以及禁用用户或组。

> [!TIP]
> 还可为 Foodee 启用基于 SAML 的单一登录，请按照 [Foodee 单一登录教程](Foodee-tutorial.md)中的说明进行操作。 可以独立于自动用户预配来配置单一登录，尽管这两个功能可互相补充。

若要在 Azure AD 中为 Foodee 配置自动用户预配，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“企业应用程序” > “所有应用程序” 。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在“应用程序”列表中，选择“Foodee” 。

    ![应用程序列表中的 Foodee 链接](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

1. 在“预配模式”下拉列表中，选择“自动” 。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

1. 在“管理员凭据”下执行以下操作：

   a. 在“租户 URL”中输入之前检索到的 https:\//concierge.food.ee/scim/v2 值 。

   b. 在“机密令牌”框中输入之前检索到的 API 令牌值 。
   
   c. 选择“测试连接”以确保 Azure AD 可以连接到 Foodee。 如果连接失败，请确保 Foodee 帐户具有管理员权限，然后重试。

    ![“测试连接”链接](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”框中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中“发生故障时发送电子邮件通知”复选框 。

    ![“通知电子邮件”文本框](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”下，选择“将 Azure Active Directory 用户同步到 Foodee” 。

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="“映射”部分的屏幕截图。在“名称”下，突出显示了“将 Azure Active Directory 用户同步到 Foodee”。" border="false":::

1. 在“属性映射”中，查看从 Azure AD 同步到 Foodee 的用户属性。 选为“匹配”属性的特性用于匹配 Foodee 中的用户帐户以执行更新操作。 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="“属性映射”页的屏幕截图。表列出了 Azure Active Directory 和 Foodee 属性以及匹配的优先级。" border="false":::

1. 若要提交更改，请选择“保存”。
1. 在“映射”下，选择“将 Azure Active Directory 组同步到 Foodee” 。

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="“映射”部分的屏幕截图。在“名称”下，突出显示了“将 Azure Active Directory 组同步到 Foodee”。" border="false":::

1. 在“属性映射”中，查看从 Azure AD 同步到 Foodee 的用户属性。 选为“匹配”属性的特性用于匹配 Foodee 中的组帐户以执行更新操作。

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="“属性映射”页的屏幕截图。表列出了 Azure Active Directory 属性、Foodee 属性以及匹配的优先级。" border="false":::

1. 若要提交更改，请选择“保存”。
1. 配置范围筛选器。 若要了解如何配置，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

1. 若要为 Foodee 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。  

    ![“预配状态”切换开关](common/provisioning-toggle-on.png)

1. 在“设置”的“范围”下拉列表中，定义要将其预配到 Foodee 的用户或组 。

    ![“预配范围”下拉列表](common/provisioning-scope.png)

1. 准备好预配时，选择“保存”。

    ![预配配置“保存”按钮](common/provisioning-configuration-save.png)

前面的操作将启动“范围”下拉列表中定义的用户或组的初始同步。 初始同步所需的时间比后续同步要长。 有关详细信息，请参阅[预配用户需要多长时间](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

可使用“当前状态”部分监视进度并跟踪指向预配活动报告的链接。 该报告描述了 Azure AD 预配服务在 Foodee 上执行的所有操作。 有关详细信息，请参阅[检查用户预配状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
