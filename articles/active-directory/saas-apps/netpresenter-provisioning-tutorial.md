---
title: 教程：使用 Azure Active Directory 为 Netpresenter 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Netpresenter 及如何取消预配。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: fc948927-6169-4da7-9829-3f25ec5be5c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2021
ms.author: Zhchia
ms.openlocfilehash: 0a4961a23c8ea9a894028af0fc596d399c0f3e8f
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661563"
---
# <a name="tutorial-configure-netpresenter-for-automatic-user-provisioning"></a>教程：为 Netpresenter 配置自动用户预配

本教程介绍在 Netpresenter 和 Azure Active Directory (Azure AD) 中配置自动用户预配所需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Netpresenter](https://www.Netpresenter.com/) 以及将其取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Netpresenter 中创建用户
> * 当用户不再有访问需求时，在 Netpresenter 中删除用户
> * 使用户属性在 Azure AD 与 Netpresenter 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* Netpresenter 的管理员帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要[在 Azure AD 与 Netpresenter 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-netpresenter-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Netpresenter 以支持通过 Azure AD 进行预配

1. 使用管理员帐户登录到 Netpresenter。
2. 单击齿轮图标进入设置页面。
3. 在设置页面中，单击“系统”打开子菜单并单击“Azure AD”。
4. 单击“生成令牌”按钮。
5. 将 SCIM 终结点 URL 和令牌保存到一个安全的位置，你将在步骤 5 中用到它们。

   ![令牌和 URL](media/netpresenter/get-token-and-url.png)

1. 可选：在“登录选项”下，可以启用或禁用“通过 Microsoft 强制登录”。 启用后，拥有 Azure AD 帐户的用户将无法使用其本地帐户登录。

## <a name="step-3-add-netpresenter-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Netpresenter

从 Azure AD 应用程序库添加 Netpresenter，开始管理 Netpresenter 的预配。 如果之前为 Netpresenter 设置过 SSO，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Netpresenter 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果“默认访问”角色是应用程序上唯一可用的角色，则可以通过[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)来添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，可以通过将一两个用户或组分配到应用来控制它。 如果预配范围设置为所有用户和组，可以指定[基于特性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-netpresenter"></a>步骤 5。 配置 Netpresenter 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-netpresenter-in-azure-ad"></a>若要在 Azure AD 中为 Netpresenter 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Netpresenter”。

    ![应用程序列表中的 Netpresenter 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下，输入 Netpresenter 租户 URL 和机密令牌。 单击“测试连接”，确保 Azure AD 可以连接到 Netpresenter。 如果连接失败，请确保 Netpresenter 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Netpresenter” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Netpresenter 的用户属性。 选为“匹配”属性的特性用于匹配 Netpresenter 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 Netpresenter API 支持根据该属性筛选用户。 选择“保存”按钮以提交任何更改。

    |Attribute|类型|支持筛选
    |---|---|---|
    |userName|字符串|&check;
    |externalId|字符串|&check;
    |emails[type eq "work"].value|字符串|&check;
    |活动|Boolean|
    |name.givenName|字符串|
    |name.familyName|字符串|
    |phoneNumbers[type eq "work"].value|字符串|
    |phoneNumbers[type eq "mobile"].value|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Netpresenter 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Netpresenter 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
