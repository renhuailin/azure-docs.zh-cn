---
title: 教程：使用 Azure Active Directory 为 Cisco Umbrella User Management 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 Cisco Umbrella User Management 及如何解除预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 1aa20f40-19ec-4213-9a3b-5eb2bcdd9bbd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: thwimmer
ms.openlocfilehash: 38b4c7cc04e5a9cc0d3dd35875ed94af804ca383
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122325084"
---
# <a name="tutorial-configure-cisco-umbrella-user-management-for-automatic-user-provisioning"></a>教程：为 Cisco Umbrella User Management 配置自动用户预配

本教程介绍了在 Cisco Umbrella User Management 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [Cisco Umbrella User Management](https://umbrella.cisco.com/)，并解除预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 Cisco Umbrella User Management 中创建用户
> * 在用户不再有访问需求的情况下，在 Cisco Umbrella User Management 中删除用户
> * 使用户属性在 Azure AD 与 Cisco Umbrella User Management 之间保持同步
> * 在 Cisco Umbrella User Management 中预配组和组成员身份

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* [Cisco Umbrella 订阅](https://signup.umbrella.com)。
* Cisco Umbrella 中具有完全管理员权限的用户帐户。

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署
1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定要[在 Azure AD 与 Cisco Umbrella User Management 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-import-objectguid-attribute-via-azure-ad-connect-optional"></a>步骤 2. 通过 Azure AD Connect 导入 ObjectGUID 属性（可选）
如果之前已将本地 AD 中的用户和组标识预配到了 Cisco Umbrella，现在想从 Azure AD 预配相同的用户和组，则需要同步 ObjectGUID 属性，这样之前预配的标识就可保留在 Umbrella 策略中。

> [!NOTE]
> 导入 ObjectGUID 属性之前应关闭本地 Umbrella AD 连接器。
  
使用 Microsoft Azure AD Connect 时，默认情况下，用户和组的 ObjectGUID 属性不会从本地 AD 同步到 Azure AD。 若要同步此属性，请启用可选的“目录扩展属性同步”，并为组和用户选择 objectGUID 属性。  

   ![Azure Active Directory Connect 向导的“可选功能”页](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extension-attribute-sync.png)

> [!NOTE]
> “可用属性”下的搜索区分大小写。

   ![显示“目录扩展”选择页的屏幕截图](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extensions.png)

## <a name="step-3-configure-cisco-umbrella-user-management-to-support-provisioning-with-azure-ad"></a>步骤 3. 配置 Cisco Umbrella User Management 以支持通过 Azure AD 进行预配

1. 登录到 [Cisco Umbrella 仪表板](https://login.umbrella.com )。 导航到“部署” > “核心标识” > “用户和组”  。

2. 如果导入机制设置为手动导入，请单击“从 IdP 导入”来切换导入机制。 
  
3. 展开“Azure Active Directory”卡，并单击“API 密钥”页。

   ![API](./media/cisco-umbrella-user-management-provisioning-tutorial/keys.png)

4. 展开“API 密钥”页上的“Azure Active Directory”卡，然后单击“生成令牌”。

   ![Generate](./media/cisco-umbrella-user-management-provisioning-tutorial/token.png)

5. 生成的令牌将仅显示一次。 复制并保存 URL 和令牌。 在 Azure 门户的 Cisco Umbrella User Management 应用程序的“预配”选项卡中，在“租户 URL”和“机密令牌”字段中分别输入这些值 。  


## <a name="step-4-add-cisco-umbrella-user-management-from-the-azure-ad-application-gallery"></a>步骤 4. 从 Azure AD 应用程序库中添加 Cisco Umbrella User Management

从 Azure AD 应用程序库中添加 Cisco Umbrella User Management，开始管理 Cisco Umbrella User Management 的预配。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-5-define-who-will-be-in-scope-for-provisioning"></a>步骤 5。 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 Cisco Umbrella User Management 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 

## <a name="step-6-configure-automatic-user-provisioning-to-cisco-umbrella-user-management"></a>步骤 6. 为 Cisco Umbrella User Management 配置自动用户预配 

本部分逐步介绍了如何配置 Azure AD 预配服务，以基于 Azure AD 中的用户和/或组分配在 Cisco Umbrella User Management 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-cisco-umbrella-user-management-in-azure-ad"></a>在 Azure AD 中为 Cisco Umbrella User Management 配置自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cisco Umbrella User Management”。

    ![应用程序列表中的 Cisco Umbrella User Management 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![“预配”选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配”选项卡“自动”](common/provisioning-automatic.png)

5. 在“管理员凭据”部分，输入 Cisco Umbrella User Management 的租户 URL 和机密令牌。 单击“测试连接”，确保 Azure AD 可以连接到 Cisco Umbrella User Management。 如果连接失败，请确保 Cisco Umbrella User Management 帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Cisco Umbrella User Management” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 Cisco Umbrella User Management 的用户属性。 选为“匹配”属性的特性用于匹配 Cisco Umbrella User Management 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 Cisco Umbrella User Management API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |externalId|字符串|
   |活动|Boolean|
   |displayName|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId|字符串|

> [!NOTE]
> 如果已通过 Azure AD Connect 导入了用户的 objectGUID 属性（请参阅步骤 2），请添加从 objectGUID 到 urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId 的映射。

10. 在“映射”部分中，选择“将 Azure Active Directory 组同步到 Cisco Umbrella User Management” 。

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Cisco Umbrella User Management 的组属性。 选为“匹配”属性的特性用于匹配 Cisco Umbrella User Management 中的组以执行更新操作。 选择“保存”按钮以提交任何更改  。

      |Attribute|类型|支持筛选|
      |---|---|---|
      |displayName|字符串|&check;|
      |externalId|字符串|
      |members|参考|
      |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:Group:nativeObjectId|字符串|

> [!NOTE]
> 如果已通过 Azure AD Connect 导入了组的 objectGUID 属性（请参阅步骤 2），请添加从 objectGUID 到 urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:Group:nativeObjectId 的映射。

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Cisco Umbrella User Management 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Cisco Umbrella User Management 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步周期 。 初始周期执行的时间比后续周期长，只要 Azure AD 预配服务正在运行，后续周期大约每隔 40 分钟就会进行一次。 

## <a name="step-7-monitor-your-deployment"></a>步骤 7. 监视部署
配置预配后，请使用以下资源来监视部署：

1. 通过[预配日志](../reports-monitoring/concept-provisioning-logs.md)来确定哪些用户已预配成功或失败
2. 检查[进度栏](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)来查看预配周期的状态以及完成进度
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 可在[此处](../app-provisioning/application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。  

## <a name="connector-limitations"></a>连接器限制
* Cisco Umbrella User Management 最多支持预配 200 组。 范围内任何超出此数目的组都不能预配给 Cisco Umbrella。 

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)