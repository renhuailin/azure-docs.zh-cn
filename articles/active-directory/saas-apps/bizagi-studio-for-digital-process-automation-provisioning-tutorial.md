---
title: 教程：使用 Azure Active Directory 为 Bizagi Studio for Digital Process Automation 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Bizagi Studio for Digital Process Automation 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: thwimmer
ms.openlocfilehash: ac1a38662172e12cebfcab952ca076062f03f0ec
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324524"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>教程：为 Bizagi Studio for Digital Process Automation 配置自动用户预配

本教程介绍在 Bizagi Studio for Digital Process Automation 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置为执行此操作后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) 以及将其取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Bizagi Studio for Digital Process Automation 中创建用户
> * 在用户不再有访问需求的情况下，在 Bizagi Studio for Digital Process Automation 中删除用户
> * 使用户属性在 Azure AD 和 Bizagi Studio for Digital Process Automation 之间保持同步
> * [单一登录](./bizagi-studio-for-digital-process-automation-tutorial.md)到 Bizagi Studio for Digital Process Automation（建议）

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md)。 
* Azure AD 中拥有配置预配的[权限](../roles/permissions-reference.md)的用户帐户。 示例包括应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员。 
* Bizagi Studio for Digital Process Automation 版本 11.2.4.2X 或更高版本。

## <a name="plan-your-provisioning-deployment"></a>计划预配部署
按照以下步骤进行计划：

1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁将在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 Bizagi Studio for Digital Process Automation 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>配置以支持通过 Azure AD 进行预配
若要配置 Bizagi Studio for Digital Process Automation 以支持通过 Azure AD 进行预配，请执行以下步骤：

1. 以具有管理员权限的用户身份登录工作门户。

2. 转到“管理” > “安全性” > “OAuth 2 应用程序”。

   ![Bizagi 的屏幕截图，其中突出显示了 OAuth 2 应用程序。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. 选择“添加”。
4. 对于“授权类型”，请选择“持有者令牌” 。 对于“允许的范围”，选择“API”和“用户同步”  。 再选择“保存”。

   ![注册应用程序的屏幕截图，其中突出显示了授权类型和允许的范围。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. 复制并保存“客户端密码”。 在 Azure 门户中，对于 Bizagi Studio for Digital Process Automation 应用程序，在的“预配”选项卡上，在“机密令牌”字段中输入客户端密码值 。

   ![Oauth 的屏幕截图，其中突出显示了“客户端密码”。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要开始管理对 Bizagi Studio for Digital Process Automation 的预配，请从 Azure AD 应用程序库添加应用。 如果以前为 Bizagi Studio for Digital Process Automation 设置过单一登录，则可以使用同一应用程序。 但是在最初测试集成时，应创建单独的应用。 有关详细信息，请参阅[快速入门：向 Azure Active Directory (Azure AD) 租户添加应用程序](../manage-apps/add-application-portal.md)。 

## <a name="define-who-is-in-scope-for-provisioning"></a>定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用程序的分配和/或用户或组的属性来限定谁在预配范围内。 如果基于分配限定范围，请参阅[使用图形 API 为应用分配或取消分配用户和组](../manage-apps/assign-user-or-group-access-portal.md)中的步骤，将用户和组分配到应用程序。 如果仅根据用户或组的属性来限定范围，可以使用范围筛选器。 有关详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

请注意以下有关范围限定的几点：

* 将用户和组分配到 Bizagi Studio for Digital Process Automation 时，必须选择“默认访问”以外的角色。 具有默认访问角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加更多角色。 

* 先小部分测试。 在向所有用户推出之前，先对一小部分用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="configure-automatic-user-provisioning"></a>配置自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以创建、更新以及禁用用户和组。 你在根据 Azure AD 中的用户和组分配，在测试应用中执行此操作。

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>在 Azure AD 中为 Bizagi Studio for Digital Process Automation 配置自动用户预配

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“企业应用程序” > “所有应用程序” 。

    ![Azure 门户的屏幕截图，其中突出显示了“企业应用程序”和“所有应用程序”。](common/enterprise-applications.png)

2. 在“应用程序”列表中选择“Bizagi Studio for Digital Process Automation”。

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”控件的屏幕截图，其中突出显示了“自动”。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分中，输入 Bizagi Studio for Digital Process Automation 的租户 URL 和机密令牌。 

      * 租户 URL：输入具有以下结构的 Bizagi SCIM 终结点：`<Your_Bizagi_Project>/scim/v2/`。
         例如：`https://my-company.bizagi.com/scim/v2/`。

      * 机密令牌：此值从本文前面讨论的步骤中进行检索。

      若要确保 Azure AD 可以连接到 Bizagi Studio for Digital Process Automation，请选择“测试连接”。 如果连接失败，请确保 Bizagi Studio for Digital Process Automation 帐户具有管理员权限，然后重试。

   ![管理员凭据的屏幕截图，其中突出显示了“测试连接”。](common/provisioning-testconnection-tenanturltoken.png)

6. 对于“通知电子邮件”，输入应接收预配错误通知的人员或组的电子邮件地址。 选择“发生故障时发送电子邮件通知”的选项。

    ![“通知电子邮件”选项的屏幕截图。](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Bizagi Studio for Digital Process Automation” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Bizagi Studio for Digital Process Automation 的用户属性。 选为“匹配”属性的特性用于匹配 Bizagi Studio for Digital Process Automation 中的用户帐户以执行更新操作。 如果更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则必须确保 Bizagi Studio for Digital Process Automation API 支持基于该属性筛选用户。 选择“保存”，提交所有更改。

   |属性|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |phoneNumbers[type eq "mobile"].value|字符串|

   可以通过导航到“显示高级选项”>“编辑 Bizagi 的属性列表”来添加自定义扩展属性。 自定义扩展属性的前缀必须为“urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:”。 例如，如果自定义扩展属性为 IdentificationNumber，则必须将属性添加为 urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:IdentificationNumber 。 选择“保存”，提交所有更改。
   
    ![编辑属性列表。](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   有关如何添加自定义属性的详细信息位于[自定义应用程序属性](../app-provisioning/customize-application-attributes.md)。

> [!NOTE]
> 仅支持基本类型属性（例如，String、Integer、Boolean、DateTime 等）。 尚不支持链接到参数表或多个类型的属性。

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的说明。

11. 若要为 Bizagi Studio for Digital Process Automation, 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用” 。

    ![预配状态切换的屏幕截图。](common/provisioning-toggle-on.png)

12. 定义要预配到 Bizagi Studio for Digital Process Automation 的用户和组。 在“设置”部分中，在“范围”中选择所需值 。

    ![范围选项的屏幕截图。](common/provisioning-scope.png)

13. 准备好预配时，选择“保存”。

    ![“保存”控件的屏幕截图。](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="monitor-your-deployment"></a>监视部署
配置预配后，请使用以下资源来监视部署：

- 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败。
- 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度。
- 如果怀疑预配配置处于运行不正常状态，则应用程序会进入隔离状态。 有关详细信息，请参阅[隔离状态本地的应用程序预配](../app-provisioning/application-provisioning-quarantine-status.md)。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)