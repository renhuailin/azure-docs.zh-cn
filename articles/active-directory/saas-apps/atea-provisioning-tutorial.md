---
title: 教程：使用 Azure Active Directory 为 Atea 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Atea 以及如何取消预配。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: thwimmer
ms.openlocfilehash: 6c97d363899373e1fc41d7629c2b53fb705cf03c
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195133"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>教程：为 Atea 配置自动用户预配

本教程介绍了在 Atea 和 Azure Active Directory (Azure AD) 中配置自动用户预配时需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Atea](https://www.atea.com/) 以及取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参考[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Atea 中创建用户
> * 在用户不再有访问需求的情况下，在 Atea 中删除用户
> * 使用户特性在 Azure AD 和 Atea 之间保持同步

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* Azure AD 中[有权](../roles/permissions-reference.md)配置预配的用户帐户（例如应用管理员、云应用管理员、应用所有者或全局管理员）。 
* 在 Atea 中具有管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Atea 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>步骤 2. 配置 Atea 以支持通过 Azure AD 进行预配

若要配置 Atea 以支持 Azure AD 的预配，请向 Atea 支持团队 <SSO.Support@atea.com> 发送电子邮件

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 Atea

从 Azure AD 应用程序库添加 Atea，开始管理 Atea 的预配。 如果以前为 SSO 设置过 Atea，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户和组的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Atea 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果“默认访问”角色是应用程序上唯一可用的角色，则可以通过[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)来添加其他角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，可以通过将一两个用户或组分配到应用来控制它。 如果预配范围设置为所有用户和组，可以指定[基于特性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>步骤 5。 配置 Atea 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和组分配在 Atea 中创建、更新以及禁用用户和组。

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>要在 Azure AD 中为 Atea 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中选择“Atea”。

    ![“应用程序”列表中的 Atea 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，选择“授权” 。 它会在新的浏览器窗口中打开 Atea 登录对话框。

     ![Atea 授权](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. 在 Atea 的登录对话框中，登录到 Atea 的租户并验证身份。
       
      ![Atea 登录对话框](media/atea-provisioning-tutorial/atea-login.png)

7. 在完成步骤 5 和 6 后，请单击“测试连接”确保 Azure AD 可以连接到 Atea。 如果连接失败，请确保你的 Atea 具有管理员权限，然后重试。
        
      ![Atea 测试连接](media/atea-provisioning-tutorial/test-connection.png)

8. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址。 然后，选中“发生故障时发送电子邮件通知”复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Atea” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Atea 的用户属性。 选为“匹配”属性的特性用于匹配 Atea 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 Atea API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

      |Attribute|类型|支持筛选|
      |---|---|---|
      |userName|字符串|&check;|
      |活动|Boolean|
      |emails[type eq "work"].value|字符串|
      |name.givenName|字符串|
      |name.familyName|字符串|
      |name.formatted|字符串|
      |phoneNumbers[type eq "mobile"].value|字符串|
      |区域设置|字符串|
      |nickName|字符串|

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 Atea 启用 Azure AD 预配服务，请在“设置”部分将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择相关的值，定义要预配到 Atea 的用户和组 。

    ![预配范围](common/provisioning-scope.png)

15. 在已准备好预配时，请单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期完成的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6. 监视部署
配置预配后，请使用以下资源来监视部署：

* 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
* 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度。
* 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)