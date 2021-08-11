---
title: 教程：使用 Azure Active Directory 为 Storegate 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 来使其自动将用户帐户预配到 Storegate 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: thwimmer
ms.openlocfilehash: fbe15ecbb602859aba7d0bce930885f61e679898
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759219"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>教程：为 Storegate 配置自动用户预配

本教程的目的是演示需要执行哪些步骤在 Storegate 和 Azure Active Directory (Azure AD) 中配置 Azure AD 来使其自动将用户和/或组预配到 Storegate 以及对其取消预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Storegate 租户](https://www.storegate.com)
* 具有管理员权限的 Storegate 用户帐户。

## <a name="assign-users-to-storegate"></a>将用户分配到 Storegate

Azure Active Directory 使用“分配”这一概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Storegate。 确定后，可按照此处的说明将这些用户和/或组分配到 Storegate：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>有关将用户分配到 Storegate 的重要提示

* 建议将单个 Azure AD 用户分配到 Storegate 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Storegate，必须在“分配”对话框中选择应用程序特定的所有有效角色（若可用）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="set-up-storegate-for-provisioning"></a>设置 Storegate 以进行预配

在使用 Azure AD 为 Storegate 配置自动用户预配之前，需要从 Storegate 检索一些预配信息。

1. 登录到 [Storegate 管理控制台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)，单击右上角的用户图标导航到设置，然后选择“帐户设置”。

    ![Storegate 添加 SCIM](media/storegate-provisioning-tutorial/admin.png)

2. 在设置中，导航到“团队”>“设置”，然后验证“单一登录”部分上的切换开关是否已切换为“开” 。

    ![Storegate 设置](media/storegate-provisioning-tutorial/team.png)

    ![Storegate 切换按钮](media/storegate-provisioning-tutorial/sso.png)

3. 复制租户 URL 和令牌 。 在 Azure 门户的 Storegate 应用程序的“预配”选项卡中，将这些值分别输入“租户 URL”和“机密令牌”字段 。 

    ![Storegate 创建令牌](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>从库中添加 Storegate

若要使用 Azure AD 为 Storegate 配置自动用户预配，需要从 Azure AD 应用程序库将 Storegate 添加到托管的 SaaS 应用程序列表。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 Storegate，然后在结果面板中选择 Storegate 。 

    ![结果列表中的 Storegate](common/search-new-app.png)

5. 选择“注册 Storegate”按钮，它会将你重定向到 Storegate 的登录页面。 

    ![Storegate OIDC 添加](media/storegate-provisioning-tutorial/signup.png)

6.  登录到 [Storegate 管理控制台](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)，单击右上角的用户图标导航到设置，然后选择“帐户设置”。

    ![Storegate 登录](media/storegate-provisioning-tutorial/admin.png)

7. 在设置中，导航到“团队”>“设置”，然后单击“单一登录”部分上的切换开关，这将启动同意流。 单击“激活”。

    ![Storegate 团队](media/storegate-provisioning-tutorial/team.png)

    ![Storegate SSO](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate 激活](media/storegate-provisioning-tutorial/activate.png)

8. Storegate 是一款 OpenIDConnect 应用，因此请选择使用 Microsoft 工作帐户登录到 Storegate。

    ![Storegate OIDC 登录](media/storegate-provisioning-tutorial/login.png)

9. 身份验证成功后，请接受同意页面的同意提示。 然后，该应用程序将自动添加到你的租户，你会被重定向到你的 Storegate 帐户。

    ![Storegate OIDC 同意](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>配置到 Storegate 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Storegate 中创建、更新和禁用用户和/或组。

> [!NOTE]
> 若要详细了解 Storegate 的 SCIM 终结点，请参阅[此处](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)。

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>若要在 Azure AD 中为 Storegate 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Storegate”。

    ![应用程序列表中的 Storegate 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://dialpad.com/scim` 。 在“机密令牌”中，输入先前从 Storegate 检索到并保存的值。 单击“测试连接”，确保 Azure AD 可连接到 Storegate。 如果连接失败，请确保 Storegate 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Storegate” 。

    ![Storegate 用户映射](media/storegate-provisioning-tutorial/usermappings.png)

9. 在“特性映射”部分中，查看从 Azure AD 同步到 Storegate 的用户特性。 选为“匹配”属性的特性用于匹配 Storegate 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![Storegate 用户特性](media/storegate-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Storegate 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“开”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Storegate 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 Storegate 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
