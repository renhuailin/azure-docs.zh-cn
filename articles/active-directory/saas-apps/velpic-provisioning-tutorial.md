---
title: 教程：使用 Azure Active Directory 为 Velpic 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Velpic 和取消其预配。
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
ms.openlocfilehash: 6250bfb7b8c657e532c0bb1405ea25a23fef96d3
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113761009"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>教程：为 Velpic 配置自动用户预配

本教程的目的是说明从 Azure AD 自动将用户帐户预配到 Velpic 和取消其预配所需在 Velpic 和 Azure AD 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

* 一个 Azure Active Directory 租户
* 启用了[企业计划](https://www.velpic.com/pricing.html)或更佳计划的 Velpic 租户
* Velpic 中具有管理员权限的用户帐户

## <a name="assigning-users-to-velpic"></a>将用户分配到 Velpic

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只有已“分配”到 Azure AD 中的应用程序的用户和组才进行同步。 

在配置和启用预配服务之前，需要确定 Azure AD 中的哪些用户和/或组表示需要访问 Velpic 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 Velpic 应用：

[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>将用户分配到 Velpic 的重要提示

* 建议将单个 Azure AD 用户分配到 Velpic 以测试预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 Velpic，必须在分配对话框中选择 **用户** 角色或其他特定于应用程序的有效角色（如果有）。 注意，**默认访问权限** 角色不可用于预配，将跳过这些用户。

## <a name="configuring-user-provisioning-to-velpic"></a>为 Velpic 配置用户预配

本部分指导完成将 Azure AD 连接到 Velpic 的用户帐户预配 API 和配置预配服务，以便在 Velpic 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Velpic 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>若要在 Azure AD 中为 Velpic 配置自动用户帐户预配，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 Velpic 配置单一登录，请使用搜索字段搜索 Velpic 实例。 否则，请选择 **添加** 并在应用程序库中搜索 **Velpic**。 从搜索结果中选择 Velpic，并将其添加到应用程序列表。

3. 选择 Velpic 实例，然后选择 **预配** 选项卡。

4. 将“预配模式”  设置为“自动”  。

    ![Velpic 预配](./media/velpic-provisioning-tutorial/Velpic1.png)

5. 在 **管理员凭据** 部分中，输入 Velpic 的 **租户 URL 和机密令牌**。（您可以在 Velpic 帐户下找到这些值：**管理** > **集成** > **插件** > **SCIM**）

    ![授权值](./media/velpic-provisioning-tutorial/Velpic2.png)

6. 在 Azure 门户中，单击 **测试连接** 确保 Azure AD 可以连接到 Velpic 应用。 如果连接失败，请确保 Velpic 帐户具有管理员权限，然后重试步骤 5。

7. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

8. 单击“ **保存**”。

9. 在 **映射** 部分下，选择将 Azure Active Directory 用户同步到 Velpic。

10. 在 **属性映射** 部分中，查看将从 Azure AD 同步到 Velpic 的用户属性。 请注意，选为 **匹配** 属性的特性将用于匹配 Velpic 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

11. 若要为 Velpic 启用 Azure AD 预配服务，请在 **设置** 部分将 **预配状态** 更改为 **打开**

12. 单击“保存”  。

这会开始将“用户和组”部分中分配的任何用户和/或组初始同步到 Velpic。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)