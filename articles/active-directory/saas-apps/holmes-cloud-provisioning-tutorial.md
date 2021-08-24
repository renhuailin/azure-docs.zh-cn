---
title: 教程：使用 Azure Active Directory 为 Holmes Cloud 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Holmes Cloud 及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: b1088904-2ea2-4440-b39e-c4b7712b8229
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: thwimmer
ms.openlocfilehash: 82fdfb7621762828a32ad67774e9b0141c6b0464
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195318"
---
# <a name="tutorial-configure-holmes-cloud-for-automatic-user-provisioning"></a>教程：为 Holmes Cloud 配置自动用户预配

本教程介绍在 Holmes Cloud 和 Azure Active Directory (Azure AD) 中配置自动用户预配所要执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Holmes Cloud](https://www.holmescloud.com/) 并自动取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Holmes Cloud 中创建用户。
> * 在用户不再有访问需求的情况下，在 Holmes Cloud 中删除用户。
> * 使用户特性在 Azure AD 与 Holmes Cloud 之间保持同步。
> * 在 Holmes Cloud 中预配组和组成员身份。
> * [单一登录](holmes-tutorial.md)到 Holmes Cloud（推荐）。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* [Holmes Cloud](https://www.holmescloud.com/) 租户。
* 在 Holmes Cloud 中具有管理员权限的用户帐户。
* 已启用单一登录和用户预配服务的 Holmes Cloud 订阅。


## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
1. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
1. 确定要[在 Azure AD 与 Holmes Cloud 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-holmes-cloud-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Holmes Cloud 以支持通过 Azure AD 进行预配

> [!NOTE]
> * 购买订阅后，你将收到 Holmes 云支持 <cs@holmescloud.com> 团队发送的 Holmes Cloud 租户 URL。
> * 只要订阅单一登录和用户预配服务，就可以在“公司设置”页中找到设置预配服务所需的信息（终结点 URL、令牌等）。
 
1. 使用 Holmes Cloud 凭据登录到 Holmes Cloud 帐户。
1. 选择“公司设置”菜单，然后选择帽形图标。
1. 在标题为“帐户预配”的卡片菜单上查看信息（包括 API 令牌）。
1. 对于令牌重新生成，请选择链接“发行 API 密钥”。


## <a name="step-3-add-holmes-cloud-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Holmes Cloud

从 Azure AD 应用程序库添加 Holmes Cloud，以开始管理 Holmes Cloud 的预配。 如果之前为 Holmes Cloud 设置过 SSO，则可使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Holmes Cloud 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-holmes-cloud"></a>步骤 5。 配置到 Holmes Cloud 的自动用户预配 

本部分逐步介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 Holmes Cloud 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-holmes-cloud-in-azure-ad"></a>在 Azure AD 中为 Holmes Cloud 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Holmes Cloud”。

    ![应用程序列表中的 Holmes Cloud 链接](common/all-applications.png)

1. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

1. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

1. 在“管理员凭据”部分下，输入 Holmes Cloud 的“租户 URL”和“机密令牌”  。 单击“测试连接”以确保 Azure AD 可以连接到 Holmes Cloud。 如果连接失败，请确保 Holmes Cloud 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

1. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

1. 选择“保存”。

1. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Holmes Cloud” 。

1. 在“特性映射”部分中，查看从 Azure AD 同步到 Holmes Cloud 的用户特性。 选为“匹配”属性的特性用于匹配 Holmes Cloud 中的用户帐户以执行更新操作。 如果选择更改[匹配目标特性](../app-provisioning/customize-application-attributes.md)，需要确保 Holmes Cloud API 支持基于该特性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;
   |活动|Boolean|   
   |displayName|字符串|
   |externalId|字符串|


1. 在“映射”部分下，选择“将 Azure Active Directory 组同步到 Holmes Cloud” 。

1. 在“特性映射”部分中，查看从 Azure AD 同步到 Holmes Cloud 的组特性。 选为“匹配”属性的特性用于匹配 Holmes Cloud 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|支持筛选|
      |---|---|---|
      |displayName|字符串|&check;
      |members|参考|
      |externalId|字符串|      

1. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

1. 若要为 Holmes Cloud 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

1. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Holmes Cloud 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

1. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="more-resources"></a>更多资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)