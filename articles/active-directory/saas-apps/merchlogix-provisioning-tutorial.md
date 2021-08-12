---
title: 教程：使用 Azure Active Directory 为 MerchLogix 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 MerchLogix 和解除其预配。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: thwimmer
ms.openlocfilehash: e4b1022c166d42ec3ab8825cd5b919dd07a5c68c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113761091"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>教程：为 MerchLogix 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 MerchLogix 以及解除其预配，需在 MerchLogix 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* MerchLogix 租户
* MerchLogix 上的技术联系人，可提供用户预配所需的 SCIM 终结点 URL 和机密令牌

## <a name="adding-merchlogix-from-the-gallery"></a>从库中添加 MerchLogix

在使用 Azure AD 为 MerchLogix 配置自动用户预配之前，需将 MerchLogix 从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 MerchLogix，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]

3. 若要添加 MerchLogix，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 MerchLogix。

5. 在结果面板中，选择 MerchLogix，然后单击“添加”按钮将 MerchLogix 添加到 SaaS 应用程序列表 。

    ![“从库中添加”部分的屏幕截图，其中标注了“输入名称”文本框。][4]

## <a name="assigning-users-to-merchlogix"></a>将用户分配到 MerchLogix

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。 

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 MerchLogix。 确定后，可以按照此处的说明将这些用户和/或组分配到 MerchLogix：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>将用户分配到 MerchLogix 的重要提示

* 建议将单个 Azure AD 用户分配到 MerchLogix，以测试初始自动用户预配配置。 测试成功后，可以稍后分配其他用户和/或组。

* 将用户分配到 MerchLogix 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>配置 MerchLogix 的自动用户预配 

本部分介绍如何配置 Azure AD 预配服务，以便根据 Azure AD 中的用户和/或组分配在 MerchLogix 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [MerchLogix 单一登录教程](merchlogix-tutorial.md)中提供的说明为 MerchLogix 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>在 Azure AD 中为 MerchLogix 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 MerchLogix。

3. 选择“预配”  选项卡。

4. 将“预配模式”  设置为“自动”  。

    ![“MerchLogix - 预配”部分的屏幕截图，其中标注了“预配”选项和“测试连接”选项，“预配模式”设置为“自动”。](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. 在“管理员凭据”部分下：

    * 在“租户 URL”字段中，输入 MerchLogix 技术联系人提供的 SCIM 终结点 URL。

    * 在“机密令牌”字段中，输入 MerchLogix 技术联系人提供的机密令牌。

6. 填充步骤 5 中所示的字段后，单击“测试连接”以确保 Azure AD 可以连接到 MerchLogix。 如果连接失败，请确保 MerchLogix 帐户具有管理员权限，然后重试。

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 MerchLogix” 。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 MerchLogix 的用户属性。 选为“匹配”属性的特性用于匹配 MerchLogix 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

11. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 MerchLogix” 。

12. 在“属性映射”部分中，查看从 Azure AD 同步到 MerchLogix 的组属性。 选为“匹配”属性的特性用于匹配 MerchLogix 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

13. 若要为 MerchLogix 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用” 。

14. 已准备好预配时，单击“保存”  。

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 MerchLogix 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
