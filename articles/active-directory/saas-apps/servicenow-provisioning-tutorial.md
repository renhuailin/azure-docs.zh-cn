---
title: 教程：使用 Azure Active Directory 为 ServiceNow 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 ServiceNow 及如何取消预配。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: 1d240d1a4fd37c9ec6371407969473e415a27065
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093680"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>教程：为 ServiceNow 配置自动用户预配

本教程介绍了在 ServiceNow 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [ServiceNow](https://www.servicenow.com/) 和取消预配。 

有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 ServiceNow 中创建用户
> * 在用户不再有访问需求的情况下，在 ServiceNow 中删除用户
> * 使用户属性在 Azure AD 和 ServiceNow 之间保持同步
> * 在 ServiceNow 中预配组和组成员身份
> * 允许[单一登录](servicenow-tutorial.md)到 ServiceNow（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）
* Calgary 或更高版本的 [ServiceNow 实例](https://www.servicenow.com/)
* Helsinki 或更高版本的 [ServiceNow Express 实例](https://www.servicenow.com/)
* ServiceNow 中具有管理员角色的用户帐户

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1：规划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 ServiceNow 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>步骤 2：配置 ServiceNow 以支持通过 Azure AD 进行预配

1. 确定 ServiceNow 实例名称。 可以在用于访问 ServiceNow 的 URL 中找到实例名称。 在下面的示例中，实例名称为 dev35214。

   ![显示 ServiceNow 实例的屏幕截图。](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. 获取 ServiceNow 中管理员的凭据。 转到 ServiceNow 中的用户配置文件，并验证该用户是否具有管理员角色。 

   ![显示 ServiceNow 管理员角色的屏幕截图。](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>步骤 3：从 Azure AD 应用程序库添加 ServiceNow

从 Azure AD 应用程序库添加 ServiceNow，开始管理到 ServiceNow 的预配。 如果以前为 ServiceNow 设置过单一登录 (SSO)，则可以使用同一应用程序。 但是，我们建议你在测试集成时创建一个单独的应用。 [详细了解如何从库中添加应用程序](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4：定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配，或用户或组的属性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以使用以下[步骤将用户和组分配到应用程序](../manage-apps/assign-user-or-group-access-portal.md)。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以[使用范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

请记住以下提示：

* 将用户和组分配到 ServiceNow 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户被排除在预配范围之外，并在预配日志中被标记为无有效资格。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>步骤 5：对 ServiceNow 配置自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以在 TestApp 中创建、更新以及禁用用户和组。 可以根据 Azure AD 中的用户和组分配进行配置。

若要在 Azure AD 中为 ServiceNow 配置自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序”   。

    ![显示“企业应用程序”窗格的屏幕截图。](common/enterprise-applications.png)

2. 在应用程序列表中，选择“ServiceNow”。

    ![显示应用程序列表的屏幕截图。](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，输入 ServiceNow 管理员凭据和用户名。 选择“测试连接”以确保 Azure AD 可以连接到 ServiceNow。 如果连接失败，请确保 ServiceNow 帐户具有管理员权限，然后重试。

    ![显示“预配服务”页的屏幕截图，你可以在其中输入“管理员凭据”。](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址  。 然后，选中“发生故障时发送电子邮件通知”复选框。

    ![显示通知电子邮件的框的屏幕截图。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 ServiceNow” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 ServiceNow 的用户属性。 选为“匹配”属性的特性用于匹配 ServiceNow 中的用户帐户以执行更新操作。 

   如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 ServiceNow API 支持基于该属性筛选用户。 
   
   选择“保存”按钮以提交任何更改。

10. 在“映射”部分中，选择“将 Azure Active Directory 组同步到 ServiceNow” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 ServiceNow 的组属性。 选为“匹配”属性的特性用于匹配 ServiceNow 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

13. 若要为 ServiceNow 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![显示“预配状态”切换为“启用”的屏幕截图。](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 ServiceNow 的用户和组 。

    ![显示预配范围选项的屏幕截图。](common/provisioning-scope.png)

15. 准备好预配时，选择“保存”。

    ![保存预配配置按钮的屏幕截图。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期所需的时间比后续周期要长。 只要 Azure AD 预配服务正在运行，后续周期大约每 40 分钟就会进行一次。 

## <a name="step-6-monitor-your-deployment"></a>步骤 6：监视部署
配置预配后，请使用以下资源来监视部署：

- 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
- 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)以查看预配周期的状态以及完成进度。
- 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 [了解有关隔离状态的详细信息](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="troubleshooting-tips"></a>故障排除提示
* 在 ServiceNow 中预配某些属性（如 Department 和 Location）时，这些值必须已存在于 ServiceNow 的引用表中 。 否则，你将收到 InvalidLookupReference 错误。 

   例如，在 ServiceNow 的某一表中，可能有两个地点（西雅图、洛杉矶）和三个部门（销售、财务、市场）。 如果尝试预配部门为“销售”以及地点为“西雅图”的用户，则该用户将预配成功。 如果尝试预配部门为“销售”以及地点为“洛杉矶”的用户，则该用户将预配失败。 必须将位置“洛杉矶”添加到 ServiceNow 的引用表中，或者必须更新 Azure AD 中的用户属性才能匹配 ServiceNow 中的格式。 
* 如果收到 **EntryJoiningPropertyValueIsMissing** 错误，请查看 [属性映射](../app-provisioning/customize-application-attributes.md)，以标识匹配的属性。 该值必须存在于要预配的用户或组上。 
* 查看 [ServiceNow SOAP API](https://docs.servicenow.com/bundle/rome-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) 以了解任何要求或限制（例如，为用户指定国家/地区代码的格式）。
* 默认情况下，预配请求将发送到 https://{你的实例名称}.service-now.com/{表名称}。 如果需要一个自定义租户 URL，可以提供整个 URL 作为实例名称。
* ServiceNowInstanceInvalid 错误表示与 ServiceNow 实例通信时出现问题。 以下是错误的文本：
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  如果遇到测试连接问题，请尝试在 ServiceNow 中为以下设置选择“否”：
   
  - “系统安全性” > “高安全性设置” > “需要对传入的 SCHEMA 请求进行基本身份验证”  
  - “系统属性” > “Web 服务” > “需要对传入的 SOAP 请求进行基本授权”  

     ![显示用于授权 SOAP 请求的选项的屏幕截图。](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  如果仍然不能解决问题，请联系 ServiceNow 支持，并请求他们启用 SOAP 调试，以帮助进行故障排除。 

* Azure AD 预配服务当前在特定 [IP 范围](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)下运行。 如有必要，你可以限制其他 IP 范围，并将这些特定的 IP 范围添加到你的应用程序的允许列表中。 该方法允许流量从 Azure AD 预配服务传递到应用程序。

* 不支持自托管 ServiceNow 实例。 

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
