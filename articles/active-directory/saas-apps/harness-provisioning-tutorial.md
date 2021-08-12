---
title: 教程：使用 Azure Active Directory 为 Harness 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Harness 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: thwimmer
ms.openlocfilehash: 9a6c88ebbe0244dadc440e66a2d8423be66731ee
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767750"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>教程：为 Harness 配置自动用户预配

在本文中，您将学习如何将 Azure Active Directory (Azure AD) 配置为自动为 Harness 预配和取消预配用户和/或组。

> [!NOTE]
> 本文介绍在 Azure AD 用户预配服务基础上构建的连接器。 有关此服务的重要信息以及常见问题解答，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动化用户预配和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本文中概述的方案假定您已具有下列先决条件：

* Azure AD 租户
* [Harness 租户](https://harness.io/pricing/)
* Harness 中具有 *管理员* 权限的用户帐户

## <a name="assign-users-to-harness"></a>将用户分配到 Harness

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，请确定 Azure AD 中的哪些用户或组需要访问 Harness。 然后，可按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明将这些用户或组分配到 Harness。

## <a name="important-tips-for-assigning-users-to-harness"></a>有关将用户分配到 Harness 的重要提示

* 建议将单个 Azure AD 用户分配到 Harness，以测试自动用户预配配置。 其他用户或组可以稍后分配。

* 将用户分配到 Harness 时，必须在 **分配** 对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-harness-for-provisioning"></a>设置 Harness 以进行预配

1. 登录到您的 [Harness 管理控制台](https://app.harness.io/#/login)，然后进入 **持续安全** > **访问管理**

    ![Harness 管理控制台](media/harness-provisioning-tutorial/admin.png)

1. 选择 **API 密钥**。

    ![Harness 的“API 密钥”链接](media/harness-provisioning-tutorial/apikeys.png)

1. 选择 **添加 API 密钥**。 

    ![Harness 添加 API 密钥链接](media/harness-provisioning-tutorial/addkey.png)

1. 在 **添加 Api 密钥** 窗格中，执行以下操作：

    ![Harness 的“添加 Api 密钥”窗格](media/harness-provisioning-tutorial/title.png)
   
   a. 在 **名称** 框中，为密钥提供名称。  
   b. 在 **权限继承自** 下拉列表中，选择一个选项。 
   
1. 选择“提交”。

1. 复制该 **密钥**，供本教程稍后使用。

    ![Harness 创建令牌](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>从库中添加 Harness

在使用 Azure AD 为 Harness 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Harness 添加到托管的 SaaS 应用程序列表。

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“所有应用程序”链接](common/enterprise-applications.png)

1. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

1. 在搜索框中输入 **Harness**，在结果面板中选择 **Harness**，然后选择 **添加** 按钮添加该应用程序。

    ![结果列表中的 Harness](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>配置 Harness 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配在 Harness 中创建、更新和禁用用户或组。

> [!TIP]
> 还可选择按照 [Harness 单一登录教程](./harness-tutorial.md)中提供的说明为 Harness 启用基于 SAML 的单一登录。 可以独立于自动用户预配来配置单一登录，尽管这两个功能可互相补充。

> [!NOTE]
> 若要了解有关 Harness SCIM 终结点的详细信息，请参阅 Harness [API 密钥](https://docs.harness.io/article/smloyragsm-api-keys)文章。

若要在 Azure AD 中为 Harness 配置自动用户预配，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“企业应用程序” > “所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Harness”。 

    ![应用程序列表中的 Harness 链接](common/all-applications.png)

1. 选择 **预配**。

    ![“预配”按钮](common/provisioning.png)

1. 在“预配模式”下拉列表中，选择“自动” 。

    ![“预配模式”下拉列表](common/provisioning-automatic.png)

1. 在“管理员凭据”下执行以下操作：

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)
 
   a. 在 **租户 URL** 框中，输入 **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** 。 登录到 Harness 后，可以在浏览器中从该 URL 获取 Harness 的帐户 ID。
   b. 在 **机密令牌** 框中，输入您在“设置 Harness 进行预配”部分的步骤 6 中保存的 SCIM 身份验证令牌值。  
   c. 选择 **测试连接** 以确保 Azure AD 可以连接到 Harness。 如果连接失败，请确保 Harness 帐户具有 *管理员* 权限，然后重试。

1. 在 **通知电子邮件** 框中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中 **发生故障时发送一封电子邮件通知** 复选框。

    ![“通知电子邮件”框](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在 **映射** 下，选择 **将 Azure Active Directory 用户同步到 Harness**。

    ![Harness“将 Azure Active Directory 用户同步到 Harness”链接](media/harness-provisioning-tutorial/usermappings.png)

1. 在 **属性映射** 下，查看从 Azure AD 同步到 Harness 的用户属性。 选为 *匹配* 的特性用于匹配 Harness 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。

    ![Harness 用户“属性映射”窗格](media/harness-provisioning-tutorial/userattributes.png)

1. 在 **映射** 下，选择 **将 Azure Active Directory 组同步到 Harness**。

    ![Harness“将 Azure Active Directory 组同步到 Harness”链接](media/harness-provisioning-tutorial/groupmappings.png)

1. 在 **属性映射** 下，查看从 Azure AD 同步到 Harness 的组属性。 选为 *匹配* 属性的特性用于匹配 Harness 中的组以执行更新操作。 选择“保存”，提交所有更改。

    ![Harness 组“属性映射”窗格](media/harness-provisioning-tutorial/groupattributes.png)

1. 要配置作用域筛选器，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

1. 在 **设置** 下，要为 Harness 启用 Azure AD 预配服务，将 **预配状态** 开关切换为 **打开**。

    ![预配状态开关切换到“打开”](common/provisioning-toggle-on.png)

1. 在 **设置** 下的 **作用域** 下拉列表中，选择要将正在预配的用户或组同步到 Harness 的方式。

    ![预配范围](common/provisioning-scope.png)

1. 准备好预配时，选择“保存”。

    ![预配“保存”按钮](common/provisioning-configuration-save.png)

此操作将启动正在预配的用户或组的初始同步。 初始同步所需的时间比后续同步要长。 只要 Azure AD 预配服务正在运行，后续同步大约每 40 分钟就会进行一次。 可在 **同步详细信息** 部分中监视进度。 还可以单击预配活动报告的链接，其中描述了 Azure AD 预配服务对 Harness 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)