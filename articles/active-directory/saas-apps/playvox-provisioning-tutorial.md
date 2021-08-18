---
title: 教程：使用 Azure Active Directory 为 Playvox 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Playvox 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: thwimmer
ms.openlocfilehash: 68342511a0cf4e6d432216c1d3a779c3bf19d051
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766394"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>教程：为 Playvox 配置自动用户预配

本教程介绍了在 Playvox 和 Azure Active Directory (Azure AD) 中配置自动用户预配时需遵循的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户或组预配到 [Playvox](https://www.playvox.com) 以及自动取消预配。 有关此服务的功能和工作原理的重要详细信息以及常见问题解答，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动进行用户预配和取消预配](../app-provisioning/user-provisioning.md)。

## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Playvox 中创建用户。
> * 当用户不再有访问需求时，在 Playvox 中删除用户。
> * 使用户属性在 Azure AD 与 Playvox 之间保持同步。

## <a name="prerequisites"></a>先决条件

本教程中的方案假定你已满足下列先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。
* Azure AD 中拥有配置预配的[权限](../roles/permissions-reference.md)的用户帐户。 例如，帐户可能有应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员角色。
* [Playvox](https://www.playvox.com) 中具有超级管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1：规划预配部署

1. 了解[预配服务工作原理](../app-provisioning/user-provisioning.md)。

2. 确定谁将在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。

3. 确定要[在 Azure AD 与 Playvox 之间映射](../app-provisioning/customize-application-attributes.md)哪些数据。

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>步骤 2：将 Playvox 配置为支持通过 Azure AD 进行预配

1. 登录到 Playvox 管理控制台，转到“设置”>“API 密钥”。

2. 选择“创建 API 密钥”。

    ![部分屏幕截图，其中显示了 Playvox 用户界面中的“创建 API 密钥”按钮的位置。](media/playvox-provisioning-tutorial/create.png)

3. 为 API 密钥输入一个有意义的名称，然后选择“保存”。 生成 API 密钥后，选择“关闭”。

4. 在你创建的 API 密钥上，选择“详细信息”图标。

    ![部分屏幕截图，其中显示了 Playvox 用户界面中的“详细信息”图标的位置，该图标是一个放大镜。](media/playvox-provisioning-tutorial/api.png)

5. 复制并保存“BASE64 密钥”值。 稍后，在 Azure 门户中，你将在 Playvox 应用程序的“预配”选项卡的“机密令牌”文本框中输入此值。

    ![“详细信息 API 密钥”消息框的屏幕截图，其中突出显示了“BASE64 密钥”值。](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>步骤 3：从 Azure AD 应用程序库中添加 Playvox

若要开始管理 Playvox 的预配，请从应用程序库中将 Playvox 添加到你的 Azure AD 租户。 若要了解详细信息，请参阅[快速入门：向 Azure Active Directory (Azure AD) 租户添加应用程序](../manage-apps/add-application-portal.md)。

如果以前已为 Playvox 设置过单一登录 (SSO)，则可以使用同一应用程序。 但是，我们建议你在初次测试集成时创建一个单独的应用。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4：定义谁在预配范围中

使用 Azure AD 预配服务，根据对应用程序的分配或者用户或组的属性来限定谁在预配范围内。 若要根据分配来限定要将谁预配到应用，请参阅[在 Azure Active Directory 中管理应用的用户分配](../manage-apps/assign-user-or-group-access-portal.md)，了解如何将用户或组分配到应用程序。 若要仅根据用户或组的属性来限定要对谁进行预配，请使用[使用范围筛选器进行基于属性的应用程序预配](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。

请记住以下几点：

* 将用户分配到 Playvox 时，必须选择“默认访问权限”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果“默认访问权限”是应用程序上唯一可用的角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)来添加其他角色。

* 先小部分测试。 在向所有用户推出之前，请先在少量用户或组中进行测试。 如果预配作用域是基于分配的用户或组，则可以通过仅向应用分配一两个用户或组来控制集的大小。 如果预配作用域包括所有用户和组，则可以指定[基于属性的作用域筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)来限制测试集的大小。

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>步骤 5：配置 Playvox 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务，以便基于 Azure AD 中的用户或组分配来创建、更新和禁用用户或组。

若要在 Azure AD 中为 Playvox 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序”，然后选择“所有应用程序”。  

    ![Azure 门户的部分屏幕截图，其中突出显示了“企业应用程序”和“所有应用程序”项](common/enterprise-applications.png)

2. 在应用程序列表中，搜索并选择“Playvox”。

    ![应用程序列表的部分屏幕截图，其中突出显示了应用程序搜索框。](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![部分屏幕截图，其中显示了“预配”菜单项。](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![“预配”选项卡的部分屏幕截图，其中显示了在“预配模式”下拉列表框中选择的“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分，输入你的 Playvox 租户 URL，如下所示：

    `https://{tenant}.playvox.com/scim/v1`

    输入先前在步骤 2 中复制的“机密令牌”。 然后，选择“测试连接”以确保 Azure AD 可以连接到 Playvox。 如果连接失败，请确保 Playvox 帐户具有管理员权限，然后重试。

    ![部分屏幕截图，其中显示了“管理员凭据”部分，包括“租户 URL”和“机密令牌”文本框，并突出显示了“测试连接”链接。](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”文本框中，输入将接收预配错误通知的人员或组的电子邮件地址。 然后，选中“发生故障时发送电子邮件通知”复选框。

    ![部分屏幕截图，其中显示了“通知电子邮件”文本框和电子邮件通知复选框。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Playvox” 。

9. 在“属性映射”部分，查看从 Azure AD 同步到 Playvox 的用户属性。 选为“匹配”属性的属性用于匹配 Playvox 中的用户帐户以执行更新操作。 如果你选择更改[匹配的目标属性](../app-provisioning/customize-application-attributes.md)，请确保 Playvox API 支持基于该属性筛选用户的功能。 选择“保存”，提交所有更改。

   |属性|类型|支持筛选|
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

11. 若要为 Playvox 启用 Azure AD 预配服务，请在“设置”部分将“预配状态”更改为“启用”  。

    ![“设置”部分的部分屏幕截图，其中显示“预配状态”已设置为“启用”。](common/provisioning-toggle-on.png)

12. 仍然在“设置”中，通过在“作用域”中选择所需的值，定义要预配到 Playvox 的用户或组。

    ![“设置”部分的部分屏幕截图，其中显示了“作用域”下拉列表框。](common/provisioning-scope.png)

13. 准备好预配时，选择“保存”。

    ![部分屏幕截图，其中显示了“保存”和“放弃”选项。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期比后续周期花费的时间要长。 假设 Azure AD 预配服务正在运行，则后续周期大约每 40 分钟就会出现一次。

## <a name="step-6-monitor-your-deployment"></a>步骤 6：监视部署

配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)以查看预配周期的状态以及完成进度。
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 若要了解有关隔离状态的详细信息，请参阅[隔离状态下的应用程序预配](../app-provisioning/application-provisioning-quarantine-status.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)