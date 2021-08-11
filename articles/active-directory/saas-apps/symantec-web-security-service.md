---
title: 教程：使用 Azure Active Directory 为 Symantec Web Security Service (WSS) 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Symantec Web Security Service (WSS) 和取消其预配。
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: thwimmer
ms.openlocfilehash: fdc1751189835a3f4e935f9143a050e31d7b5206
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759160"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>教程：为 Symantec Web Security Service (WSS) 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 Symantec Web Security Service (WSS) 以及取消其预配需在 Symantec Web Security Service (WSS) 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Symantec Web Security Service (WSS) 租户](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Symantec Web Security Service (WSS) 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>将用户分配到 Symantec Web Security Service (WSS)

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Symantec Web Security Service (WSS)。 确定后，可以按照此处的说明将这些用户和/或组分配到 Symantec Web Security Service (WSS)：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>将用户分配到 Symantec Web Security Service (WSS) 的重要提示

* 建议将单个 Azure AD 用户分配到 Symantec Web Security Service (WSS) 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Symantec Web Security Service (WSS)，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>设置 Symantec Web Security Service (WSS) 以进行预配

使用 Azure AD 为 Symantec Web Security Service (WSS) 配置自动用户预配之前，需要在 Symantec Web Security Service (WSS) 上启用 SCIM 预配。

1. 登录到 [ Symantec Web Security Service 管理控制台](https://portal.threatpulse.com/login.jsp)。 导航到“解决方案” > “服务” 。

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. 导航到“帐户维护” > “集成” > “新建集成”  。

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  选择“第三方用户与组同步”。 

    ![“第三方用户与组同步”选项的屏幕截图。](media/symantec-web-security-service/third-party-users.png)

4.  复制“CIM URL”和“令牌” 。 在 Azure 门户的 Symantec Web Security Service (WSS) 的“预配”选项卡中，将这些值输入“租户 URL”和“机密令牌”字段中 。

    ![“新建集成”对话框的屏幕截图，其中突出显示了“SCIM URL”和“令牌”文本框。](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>从库添加 Symantec Web Security Service (WSS)

若要使用 Azure AD 为 Symantec Web Security Service (WSS) 配置自动用户预配，需要从 Azure AD 应用程序库将 Symantec Web Security Service (WSS) 添加到托管的 SaaS 应用程序列表。

**要从 Azure AD 应用程序库添加 Symantec Web Security Service (WSS)，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 Symantec Web Security Service，在结果面板中选择“Symantec Web Security Service”，然后单击“添加”按钮添加该应用程序  。

    ![结果列表中的 Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>配置 Symantec Web Security Service (WSS) 的自动用户预配

本部分逐步介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Symantec Web Security Service (WSS) 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [Symantec Web Security Service (WSS) 单一登录教程](symantec-tutorial.md)中提供的说明为 Symantec Web Security Service (WSS) 启用基于 SAML 的单一登录。 可独立于自动用户预配来配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>要在 Azure AD 中为 Symantec Web Security Service (WSS) 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Symantec Web Security Service”。

    ![应用程序列表中的 Symantec Web Security Service (WSS) 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，分别在“租户 URL”和“机密令牌”中输入之前检索到的“SCIM URL”和“令牌”值   。 单击“测试连接”以确保 Azure AD 可以连接到 Symantec Web Security Service。 如果连接失败，请确保 Symantec Web Security Service (WSS) 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“ **保存**”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Symantec Web Security Service (WSS)” 。

    ![“映射”部分的屏幕截图，其中突出显示了“将 Azure Active Directory 用户同步到 Symantec Web Security Service (WSS)”选项。](media/symantec-web-security-service/usermapping.png)

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Symantec Web Security Service (WSS) 的用户属性。 选为“匹配”属性的特性用于匹配 Symantec Web Security Service (WSS) 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![“属性映射”部分的屏幕截图，其中显示了 16 个匹配属性。](media/symantec-web-security-service/userattribute.png)

10. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Symantec Web Security Service” 。

    ![“映射”部分的屏幕截图，其中突出显示了“将 Azure Active Directory 组同步到 Symantec Web Security Service (WSS)”选项。](media/symantec-web-security-service/groupmapping.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Symantec Web Security Service (WSS) 的组属性。 选为“匹配”属性的特性用于匹配 Symantec Web Security Service (WSS) 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![“属性映射”部分的屏幕截图，其中显示了三个匹配属性。](media/symantec-web-security-service/groupattribute.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Symantec Web Security Service (WSS) 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Symantec Web Security Service (WSS) 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步所需的时间比后续同步要长。 若要详细了解预配用户和/或组所需的时间，请参阅[预配用户所需的时间](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

可使用“当前状态”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Symantec Web Security Service (WSS) 执行的所有操作。 有关详细信息，请参阅[检查用户预配状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
