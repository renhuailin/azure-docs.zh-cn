---
title: 教程：配置 Bizagi Studio 以实现数字流程自动化，以便 Azure Active Directory 的自动用户预配 |Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配和取消设置为数字流程自动化的 Bizagi Studio。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: b739cd50c4d4477d3622350a9a9c96b600794c7d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632021"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Bizagi Studio 以实现数字流程自动化

本教程介绍了需要在 Bizagi Studio 中执行数字流程自动化和 Azure Active Directory (Azure AD) 配置自动用户预配的步骤。 配置为执行此操作时，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配和取消设置到 [Bizagi Studio 以实现数字流程自动化](https://www.bizagi.com/) 。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Bizagi Studio 中创建用户以实现数字流程自动化
> * 如果用户不再需要访问，请在 Bizagi Studio 中删除用户以进行数字流程自动化
> * 使用户属性在 Azure AD 与 Bizagi Studio 之间保持同步以实现数字流程自动化
> * [单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) 到 Bizagi Studio 以实现数字流程自动化 (建议) 

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

* [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。 
* Azure AD 中拥有配置预配的[权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的用户帐户。 例如，应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员。 
* Bizagi Studio for 数字流程自动化版本 11.2.4.2 X 或更高版本。

## <a name="plan-your-provisioning-deployment"></a>规划预配部署
请按照以下步骤进行规划：

1. 了解[预配服务的工作原理](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
2. 确定将 [处于预配范围内的](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)用户。
3. 确定用于 [数字流程自动化 Azure AD 和 Bizagi Studio 之间要映射的](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)数据。 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>配置以支持 Azure AD 的预配
若要配置 Bizagi Studio 以支持 Azure AD 的预配，请执行以下步骤：

1. 以具有 **管理员权限** 的用户身份登录到你的工作门户。

2. 请参阅 **管理**  >  **安全**  >  **OAuth 2 应用程序**。

   ![突出显示了 OAuth 2 应用程序的 Bizagi 屏幕截图。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. 选择 **添加** 。
4. 对于 " **授权类型**"，请选择 " **持有者令牌**"。 对于 " **允许的作用域**"，选择 " **API** 和 **用户同步**"。 再选择“保存”。

   ![注册应用程序的屏幕截图，其中突出显示了 Grant 类型和允许的范围。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. 复制并保存 **客户端密码**。 在 Azure 门户的 Bizagi Studio for 数字流程自动化应用程序的 " **预配** " 选项卡上，在 " **机密令牌** " 字段中输入客户端密码值。

   ![具有客户端密码 highlighed 的 Oauth 的屏幕截图。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要开始管理预配到 Bizagi Studio 以实现数字流程自动化，请从 Azure AD 应用程序库添加应用。 如果以前为单一登录设置了用于数字流程自动化的 Bizagi Studio，则可以使用相同的应用程序。 但是，在您最初测试集成时，应创建一个单独的应用程序。 有关详细信息，请参阅 [快速入门：将应用程序添加到 Azure Active Directory (Azure AD) 租户](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)。 

## <a name="define-who-is-in-scope-for-provisioning"></a>定义要在范围内进行预配的人员 

利用 Azure AD 预配服务，可以根据分配给应用程序的用户和/或组的属性来确定哪些用户已预配。 如果基于分配进行作用域，请参阅使用图形 API 将用户和组分配到应用程序中 [分配或取消分配用户和组](../manage-apps/assign-user-or-group-access-portal.md) 的步骤。 如果仅基于用户或组的属性进行作用域，则可以使用范围筛选器。 有关详细信息，请参阅 [通过范围筛选器进行基于属性的应用程序设置](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

请注意以下有关范围的几点：

* 将用户和组分配到用于数字流程自动化的 Bizagi Studio 时，必须选择 " **默认访问**" 以外的角色。 将从设置中排除具有默认访问角色的用户，并将其标记为 "设置日志"，将其标记为 "不有效"。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)以添加更多角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果设置的作用域设置为 "分配的用户和组"，则可以通过将一个或两个用户或组分配到应用来对此进行控制。 当作用域设置为 "所有用户和组" 时，可以指定 [基于属性的范围筛选器](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="configure-automatic-user-provisioning"></a>配置自动用户预配 

本部分将指导你完成配置 Azure AD 预配服务以创建、更新和禁用用户和组的步骤。 在测试应用中执行此操作，具体取决于 Azure AD 中的用户和组分配。

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>在 Azure AD 中配置 Bizagi Studio 的自动用户预配以实现数字流程自动化

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序” 。

    ![Azure 门户屏幕截图，其中突出显示了企业应用程序和所有应用程序。](common/enterprise-applications.png)

2. 在应用程序列表中，选择 " **Bizagi Studio For 数字流程自动化**"。

3. 选择“预配”  选项卡。

    !["管理选项" 的屏幕截图，其中突出显示了预配。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![Screenshotof 预配模式控件，自动突出显示。](common/provisioning-automatic.png)

5. 在 " **管理员凭据** " 部分中，输入你的租户 URL 和 Bizagi Studio 的机密令牌用于数字流程自动化。 

      * **租户 URL：** 输入 Bizagi SCIM 终结点，其中包含以下结构：  `<Your_Bizagi_Project>/scim/v2/` 。
         例如：`https://my-company.bizagi.com/scim/v2/`。

      * **机密令牌：** 从本文前面讨论的步骤中检索此值。

      若要确保 Azure AD 可以连接到用于数字流程自动化的 Bizagi Studio，请选择 " **测试连接**"。 如果连接失败，请确保 Bizagi Studio for 数字流程自动化帐户具有管理员权限，然后重试。

   ![管理凭据的屏幕截图，其中突出显示了 "测试连接"。](common/provisioning-testconnection-tenanturltoken.png)

6. 对于 " **通知电子邮件**"，输入应接收预配错误通知的人员或组的电子邮件地址。 选择该选项以在 **发生故障时发送电子邮件通知**。

    ![通知电子邮件选项的屏幕截图。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在 " **映射** " 部分中，选择 " **将 Azure Active Directory 用户同步到 Bizagi Studio 以实现数字流程自动化**"。

9. 在 " **属性映射** " 部分中，查看从 Azure AD 同步到 Bizagi Studio 以实现数字流程自动化的用户属性。 选为 " **匹配** " 属性的属性用于匹配 Bizagi Studio 中的用户帐户以执行更新操作。 如果更改了 [匹配的目标属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)，则必须确保适用于数字流程自动化 API 的 Bizagi Studio 支持基于该属性筛选用户。 选择“保存”，提交所有更改。

   |属性|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|

   可以通过导航到 " **显示高级选项" > 编辑 Bizagi 的属性列表** 来添加自定义扩展属性。 自定义扩展属性必须带有 **urn： ietf： params： scim：架构： extension： bizagi：2.0： UserProperties：**。 例如，如果自定义扩展属性为 **IdentificationNumber**，则必须将属性添加为 **urn： ietf： params： scim：架构： extension： bizagi：2.0： UserProperties： IdentificationNumber**。 选择“保存”，提交所有更改。
   
    ![编辑属性列表。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   有关如何添加自定义属性的详细信息，请参阅 [自定义应用程序属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。


10. 若要配置范围筛选器，请参阅 [范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)。

11. 若要为 Bizagi Studio 启用适用于数字流程自动化的 Azure AD 预配服务，请在 " **设置** " 部分中将 " **预配状态** " 更改为 **"开**"。

    ![预配状态切换屏幕截图。](common/provisioning-toggle-on.png)

12. 定义要预配到 Bizagi Studio 以实现数字流程自动化的用户和组。 在 " **设置** " 部分的 " **范围**" 中选择所需的值。

    ![作用域选项的屏幕截图。](common/provisioning-scope.png)

13. 准备好预配时，选择“保存”。

    ![保存控件的屏幕截图。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="monitor-your-deployment"></a>监视部署
配置预配后，请使用以下资源来监视部署：

- 通过[预配日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)来确定哪些用户已预配成功或失败。
- 检查 [进度栏](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) 以查看设置周期的状态，以及完成操作的方式。
- 如果预配配置处于不正常状态，则应用程序将进入隔离区。 有关详细信息，请参阅 [处于隔离状态的应用程序设置](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
