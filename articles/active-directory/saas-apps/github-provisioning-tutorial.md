---
title: 教程：GitHub 的用户预配 - Azure AD
description: 了解如何配置 Azure Active Directory 以在 GitHub Enterprise Cloud 中自动预配和取消预配用户的组织成员身份。
services: active-directory
author: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: thwimmer
ms.openlocfilehash: b8de34165c327d335bff399319852ef035a7094e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728222"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>教程：为 GitHub 配置自动用户预配

本教程的目标是展示自动预配 GitHub Enterprise Cloud 组织成员身份时需要在 GitHub 和 Azure AD 中执行的步骤。

> [!NOTE]
> Azure AD 预配集成依赖于可供 [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) 客户在 [GitHub 企业计费计划](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)中使用的 [GitHub SCIM API](https://developer.github.com/v3/scim/)。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

* Azure Active Directory 租户
* 一家在 [GitHub 企业云](https://help.github.com/articles/github-s-products/#github-enterprise)（需要 [GitHub Enterprise 计费计划](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)）中创建的 GitHub 组织
* GitHub 中具有组织的管理员权限的用户帐户
* [为 GitHub Enterprise Cloud 组织配置的 SAML](./github-tutorial.md)
* 请确保已为组织提供 OAuth 访问权限，如[此处](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)所述
* 仅在组织级别启用 SSO 时，才支持单个组织的 SCIM 预配

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="assigning-users-to-github"></a>将用户分配到 GitHub

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。 

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 GitHub 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 GitHub 应用：

有关详细信息，请参阅[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)。

### <a name="important-tips-for-assigning-users-to-github"></a>将用户分配到 GitHub 的重要提示

* 建议将单个 Azure AD 用户分配到 GitHub 以测试预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 GitHub，必须在分配对话框中选择“用户”角色或其他特定于应用程序的有效角色（如果有）。 “默认访问权限”角色不可用于预配，将跳过这些用户。

## <a name="configuring-user-provisioning-to-github"></a>为 GitHub 配置用户预配

本部分将指导你完成将 Azure AD 连接到 GitHub 的 SCIM 预配 API，以自动预配 GitHub 组织成员身份。 此集成利用 [OAuth 应用](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/authorizing-oauth-apps#oauth-apps-and-organizations)，可根据 Azure AD 中的用户和组分配自动添加、管理和删除成员对 GitHub Enterprise Cloud 组织的访问权限。 当[通过 SCIM 将用户预配到 GitHub 组织](https://docs.github.com/en/free-pro-team@latest/rest/reference/scim#provision-and-invite-a-scim-user)时，将向用户的电子邮件地址发送电子邮件邀请。

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>在 Azure AD 中为 GitHub 配置自动用户帐户预配

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 GitHub 配置单一登录，请使用搜索字段搜索 GitHub 实例。 否则，请选择“添加”并在应用程序库中搜索“GitHub”。 从搜索结果中选择 GitHub，并将其添加到应用程序列表。

3. 选择 GitHub 实例，并选择“预配”选项卡。

4. 将“预配模式”  设置为“自动”  。

   ![GitHub 预配](./media/github-provisioning-tutorial/github1.png)

5. 在“管理员凭据”部分下，单击“授权”。 此操作将在新的浏览器窗口中打开“GitHub 授权”对话框。 请注意，需要确保已获得批准可以进行访问授权。 按照[此处](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)的说明进行操作。

6. 在新窗口中，使用管理员帐户登录到 GitHub。 在显示的授权对话框中，选择要启用预配的 GitHub 团队，并选择“授权”。 完成后，返回到 Azure 门户完成预配配置。

   ![屏幕截图显示 GitHub 的登录页。](./media/github-provisioning-tutorial/github2.png)

7. 在 Azure 门户中，输入“租户 URL”并单击“测试连接”，确保 Azure AD 可以连接到 GitHub 应用。 如果连接失败，请确保 GitHub 帐户具有管理员权限，且输入的“租户 URl”正确无误，然后再次尝试“授权”步骤（可以按规则：`https://api.github.com/scim/v2/organizations/<Organization_name>` 来编写“租户 URL”，可在 GitHub 帐户：“设置” > “组织”下找到你的组织）。

   ![屏幕截图显示 GitHub 中的组织页。](./media/github-provisioning-tutorial/github3.png)

8. 在“通知电子邮件”字段中输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

9. 单击“ **保存**”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 GitHub”。

11. 在“属性映射”部分，查看从 Azure AD 同步到 GitHub 的用户属性。 选为“匹配”属性的特性用于匹配 GitHub 中的用户帐户以执行更新操作。 请勿为“预配”部分中的其他默认属性启用“匹配优先级”设置，因为可能会发生错误 。 选择“保存”，提交所有更改。

12. 要为 GitHub 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

13. 单击“ **保存**”。

此操作会对“用户和组”部分中分配到 GitHub 的任何用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务执行的所有操作。 

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
