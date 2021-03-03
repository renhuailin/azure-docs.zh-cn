---
title: 教程：为 Preciate 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何自动将用户 Azure AD 帐户预配到 Preciate 以及取消其预配。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: fa640971-87e7-49f2-933b-bc7c95fe51e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2020
ms.author: Zhchia
ms.openlocfilehash: ab6b17d2db2fd45578f74da9750d3f50dba2636f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645648"
---
# <a name="tutorial-configure-preciate-for-automatic-user-provisioning"></a>教程：为 Preciate 配置自动用户预配

本教程介绍了需要在 Preciate 和 Azure Active Directory (Azure AD) 中执行的步骤，以配置自动用户预配。 配置后，Azure AD 使用 Azure AD 预配服务自动设置用户和组并取消其预配到 [Preciate](https://www.preciate.org/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Preciate 中创建用户
> * 当用户不再需要访问权限时，删除 Preciate 中的用户
> * 使用户属性在 Azure AD 和 Preciate 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* Preciate 租户。
* Preciate 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要 [在 Azure AD 与 Preciate 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-preciate-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 Preciate 以支持 Azure AD 的预配

1.  登录到 [Preciate 管理门户](https://preciate.com/web/admin/keys) ，并导航到 " **集成** " 页。

    ![Preciate 机密](media/preciate-provisioning-tutorial/preciate-secret-path.png)

2.  选择 " **生成** " 按钮，其中显示 Active Directory 集成密钥 "。 
 
    ![Preciate 生成](media/preciate-provisioning-tutorial/preciate-secret-generate.png)

3.  将显示一个新的 **密钥** 。 复制并保存 **密钥**。 另外，请注意租户 URL 是 `https://preciate.com/api/v1/scim` 。 这些值将输入到 Azure 门户中 Preciate 应用程序的 "预配" 选项卡的 " **机密令牌** " 和 " **租户 URL** " 字段中。
 
> [!NOTE]
>每次单击 "生成" 按钮时，都会创建一个新的密钥。 这将立即使当前无效。 如果集成已在使用当前密钥，则生成新的密钥会导致集成停止运行，直到在 Azure porta 中的 Preciate 应用程序中更新机密令牌。


## <a name="step-3-add-preciate-from-the-azure-ad-application-gallery"></a>步骤 3。 从 Azure AD 应用程序库添加 Preciate

从 Azure AD 应用程序库中添加 Preciate，开始管理预配到 Preciate。 如果以前为 SSO 设置了 Preciate，则可以使用相同的应用程序。 但建议你在最初测试集成时创建一个单独的应用。 详细了解如何从 [库](../manage-apps/add-application-portal.md)中添加应用程序。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Preciate 时，必须选择 " **默认" 访问权限** 以外的其他角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以 [更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md) 来添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，可以通过将一两个用户或组分配到应用来控制它。 如果预配范围设置为所有用户和组，可以指定[基于特性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-preciate"></a>步骤 5。 配置 Preciate 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-preciate-in-azure-ad"></a>若要在 Azure AD 中配置 Preciate 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **Preciate**"。

    ![应用程序列表中的 Preciate 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入你的 PRECIATE 租户 URL 和机密令牌。 单击 " **测试连接** " 以确保 Azure AD 可以连接到 Preciate。 如果连接失败，请确保 Preciate 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分下，选择 " **将 Azure Active Directory 用户同步到 Preciate**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Preciate 的用户属性。 选为 " **匹配** " 属性的特性用于匹配 Preciate 中的用户帐户以执行更新操作。 如果选择更改 [匹配的目标属性](../app-provisioning/customize-application-attributes.md)，将需要确保 Preciate API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |displayName|字符串|
   |title|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |externalId|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 Preciate 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Preciate 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)