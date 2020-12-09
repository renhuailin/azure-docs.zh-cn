---
title: 教程：使用 Azure Active Directory 为自动用户预配配置 Playvox |Microsoft Docs
description: 了解如何自动将用户 Azure AD 帐户预配到 Playvox 以及取消其预配。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862473"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>教程：为 Playvox 配置自动用户预配

本教程介绍了 Playvox 和 Azure Active Directory (Azure AD) 配置自动用户预配的步骤。 配置后，Azure AD 通过使用 Azure AD 预配服务自动预配用户或 [组，并取消其预](https://www.playvox.com) 配。 有关此服务的用途和工作原理以及常见问题的重要详细信息，请参阅 [使用 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Playvox 中创建用户。
> * 如果用户不再需要访问，请将其删除 Playvox。
> * 使用户属性在 Azure AD 和 Playvox 之间保持同步。

## <a name="prerequisites"></a>必备条件

本教程中的方案假定你已具备以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中拥有配置预配的[权限](../roles/permissions-reference.md)的用户帐户。 例如，帐户可能具有 "应用程序管理员"、"云应用程序管理员"、"应用程序所有者" 或 "全局管理员" 角色。
* [Playvox](https://www.playvox.com)中具有超级管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤1：规划预配部署

1. 了解 [预配服务的工作原理](../app-provisioning/user-provisioning.md)。

2. 确定将 [处于预配范围内的](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)用户。

3. 确定要 [在 Azure AD 与 Playvox 之间映射](../app-provisioning/customize-application-attributes.md)的数据。

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>步骤2：使用 Azure AD 配置 Playvox 以支持预配

1. 登录到 Playvox 管理控制台，并 **> API 密钥**"中转到" 设置 "。

2. 选择 " **创建 API 密钥**"。

    ![部分屏幕截图，显示 Playvox 用户界面中的 "创建 API 密钥" 按钮的位置。](media/playvox-provisioning-tutorial/create.png)

3. 为 API 密钥输入有意义的名称，然后选择 " **保存**"。 生成 API 密钥后，选择 " **关闭**"。

4. 在创建的 API 密钥上，选择 **详细信息** 图标。

    ![部分屏幕截图，显示 Playvox 用户界面中的 "详细信息" 图标的位置。](media/playvox-provisioning-tutorial/api.png)

5. 复制并保存 **BASE64 密钥** 值。 稍后，在 Azure 门户中，你将在 Playvox 应用程序的 "**预配**" 选项卡的 "**机密令牌**" 文本框中输入此值。

    !["详细信息 API 密钥消息" 框的屏幕截图，其中突出显示了 "BASE64" 键值。](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>步骤3：从 Azure AD 应用程序库添加 Playvox

若要开始管理 Playvox 的预配，请从应用程序库中将 Playvox 添加到 Azure AD 租户。 若要了解详细信息，请参阅 [快速入门：将应用程序添加到 Azure Active Directory (Azure AD) 租户](../manage-apps/add-application-portal.md)。

如果以前已设置了 Playvox (SSO) 的单一登录，则可以使用相同的应用程序。 但是，我们建议您在最初测试集成时创建一个单独的应用程序。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤4：定义将在预配范围内的人员

使用 Azure AD 预配服务来确定将预配的用户的作用域，具体取决于分配给应用程序或用户或组的属性。 若要确定将根据分配将哪些用户预配到你的应用，请参阅 [在 Azure Active Directory 中管理应用的用户分配](../manage-apps/assign-user-or-group-access-portal.md) ，了解如何将用户或组分配到应用程序。 若要将仅根据用户或组的属性进行设置的作用域，请使用范围筛选器，如使用 [范围筛选器进行基于属性的应用程序设置](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中所述。

请记住以下几点：

* 将用户分配到 Playvox 时，必须选择 "默认" 访问权限以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果默认访问是应用程序上唯一可用的角色，则可以 [更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md) 来添加其他角色。

* 先小部分测试。 使用少量的用户或组进行测试，然后再向所有人推出。 如果预配作用域基于分配的用户或组，则可以通过仅向应用分配一个或两个用户或组来控制集的大小。 如果预配作用域包括所有用户和组，则可以指定 [基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 来限制测试集的大小。

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>步骤5：配置 Playvox 的自动用户预配

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户或组分配来创建、更新和禁用用户或组的步骤。

若要在 Azure AD 中配置 Playvox 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序”，然后选择“所有应用程序”。  

    ![Azure 门户的部分屏幕截图，其中突出显示了 "企业应用程序" 和 "所有应用程序" 项](common/enterprise-applications.png)

2. 在应用程序列表中，搜索并选择 " **Playvox**"。

    ![应用程序列表的部分屏幕截图，其中突出显示了应用程序搜索框。](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![部分屏幕截图，显示 "设置" 菜单项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    !["预配" 选项卡的部分屏幕截图，显示在 "预配模式" 下拉列表框中选择的 "自动" 选项。](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入你的 PLAYVOX **租户 URL** ，如下所示：

    `https://{tenant}.playvox.com/scim/v1`

    输入先前在步骤2中复制的 **机密令牌** 。 然后，选择 " **测试连接** " 以确保 Azure AD 可以连接到 Playvox。 如果连接失败，请确保 Playvox 帐户具有管理员权限，然后重试。

    ![部分屏幕截图，显示 "管理员凭据" 部分，包括 "租户 URL" 和 "机密令牌" 文本框，并突出显示了 "测试连接" 链接。](common/provisioning-testconnection-tenanturltoken.png)

6. 在 " **通知电子邮件** " 文本框中，输入将接收设置错误通知的人员或组的电子邮件地址。 然后，选中“发生故障时发送电子邮件通知”复选框。

    ![部分屏幕截图，显示通知电子邮件文本框和电子邮件通知复选框。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分，选择 " **将 Azure Active Directory 用户同步到 Playvox**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Playvox 的用户属性。 选为 " **匹配** " 属性的特性用于匹配 Playvox 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](../app-provisioning/customize-application-attributes.md)，请确保 Playvox API 支持基于该属性筛选用户。 选择“保存”，提交所有更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |displayName|字符串|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |externalId|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

11. 若要为 Playvox 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![设置部分的部分屏幕截图，显示设置为 On 的设置状态。](common/provisioning-toggle-on.png)

12. 仍在 " **设置**" 中，通过在 " **范围**" 中选择所需的值，定义要预配到 Playvox 的用户或组。

    !["设置" 部分的部分屏幕截图，显示 "作用域" 下拉列表框。](common/provisioning-scope.png)

13. 准备好预配时，选择“保存”。

    ![部分屏幕截图，显示保存和放弃选项。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期比稍后周期长。 如果 Azure AD 预配服务正在运行，则更晚的周期大约每40分钟发生一次。

## <a name="step-6-monitor-your-deployment"></a>步骤6：监视部署

配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)以查看预配周期的状态以及完成进度。
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 若要了解有关隔离状态的详细信息，请参阅[隔离状态下的应用程序预配](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)