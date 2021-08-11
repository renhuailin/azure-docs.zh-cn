---
title: 教程：使用 Azure Active Directory 为 SAP Analytics 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 SAP Analytics Cloud 及如何取消预配。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: thwimmer
ms.openlocfilehash: 16fbc64f77407c02ec189e9f0bd0de40d9a7f2bd
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759638"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>教程：为 SAP Analytics Cloud 配置自动用户预配

本教程介绍了在 SAP Analytics Cloud 和 Azure Active Directory (Azure AD) 中配置自动用户预配需执行的步骤。 配置后，Azure AD 会使用 Azure AD 预配服务自动将用户和组预配到 [SAP Analytics Cloud](https://www.sapanalytics.cloud/) 和取消预配。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支持的功能
> [!div class="checklist"]
> * 在 SAP Analytics Cloud 中创建用户
> * 在用户不再有访问需求的情况下，在 SAP Analytics Cloud 中删除用户
> * 使用户属性在 Azure AD 和 SAP Analytics Cloud 之间保持同步
> * [单一登录](sapboc-tutorial.md)到 SAP Analytics Cloud（推荐）

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* [Azure AD 租户](../develop/quickstart-create-new-tenant.md) 
* 具有配置预配[权限](../roles/permissions-reference.md)的 Azure AD 用户帐户（例如应用程序管理员、云应用程序管理员、应用程序所有者或全局管理员）。 
* SAP Analytics Cloud 租户
* SAP 标识预配管理控制台上具有管理员权限的用户帐户。 请确保你有权访问标识预配管理控制台中的代理系统。 如果看不到“代理系统”磁贴，请为组件 BC-IAM-IPS 创建事件，以请求访问此磁贴 。
* SAP Analytics Cloud 中具有授权客户端凭据的 OAuth 客户端。 若要了解操作方法，请参阅：[管理 OAuth 客户端和受信任的标识提供者](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>步骤 1。 计划预配部署

1. 了解[预配服务的工作原理](../app-provisioning/user-provisioning.md)。
2. 确定谁在[预配范围](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中。
3. 确定[在 Azure AD 与 SAP Analytics Cloud 之间映射](../app-provisioning/customize-application-attributes.md)的数据。 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>步骤 2。 配置 SAP Analytics Cloud 以支持通过 Azure AD 进行预配

1. 用管理员帐户登录 [SAP 标识预配管理控制台](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/)，然后选择“代理系统”。

   ![SAP 代理系统](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. 选择“属性”。

   ![SAP 代理系统属性](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. 复制 URL 并将 `/api/v1/scim` 追加到该 URL 后面。 保存此项以便稍后在“租户 URL”字段中使用。

   ![SAP 代理系统属性 URL](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. 使用 [POSTMAN](https://www.postman.com/) 执行对以下地址的 POST HTTPS 调用：`<Token URL>?grant_type=client_credentials`，其中的 `Token URL` 是“OAuth2TokenServiceURL”字段中的 URL。 在配置自动预配时，生成要在“机密令牌”字段中使用的访问令牌需要使用此步骤。

   ![SAP IP 代理系统属性 OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. 在 Postman 中，使用“基本身份验证”，并将 OAuth 客户端 ID 设置为用户，将机密设置为密码。 此调用将返回访问令牌。 复制此项以便稍后用于“机密令牌”字段。

   ![Postman POST 请求](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>步骤 3. 从 Azure AD 应用程序库添加 SAP Analytics Cloud

从 Azure AD 应用程序库添加 SAP Analytics Cloud，开始管理 SAP Analytics Cloud 的预配。 如果以前为 SSO 设置过 SAP Analytics Cloud，则可以使用同一应用程序。 但建议你在最初测试集成时创建一个单独的应用。 若要详细了解如何从库中添加应用，可以单击[此处](../manage-apps/add-application-portal.md)。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步骤 4. 定义谁在预配范围中 

使用 Azure AD 预配服务，可以根据对应用的分配或用户/组的特性来限定谁在预配范围内。 如果选择根据分配来限定要将谁预配到应用，可以按照下面的[步骤](../manage-apps/assign-user-or-group-access-portal.md)操作，将用户和组分配到应用。 如果选择仅根据用户或组的属性来限定要对谁进行预配，可以使用[此处](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)所述的范围筛选器。 

* 将用户和组分配到 SAP Analytics Cloud 时，必须选择“默认访问”以外的角色。 具有“默认访问”角色的用户将从预配中排除，并在预配日志中被标记为未有效授权。 如果应用程序上唯一可用的角色是默认访问角色，则可以[更新应用程序清单](../develop/howto-add-app-roles-in-azure-ad-apps.md)以添加其他角色。 

* 先小部分测试。 在向全员推出之前，请先使用少量的用户和组进行测试。 如果预配范围设置为分配的用户和组，则可以先尝试将一两个用户或组分配到应用。 当预配范围设置为所有用户和组时，可以指定[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>步骤 5。 配置 SAP Analytics Cloud 的自动用户预配 

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 TestApp 中创建、更新和禁用用户和/或组。

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>若要在 Azure AD 中为 SAP Analytics Cloud 配置自动用户预配，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中选择“SAP Analytics Cloud”。

    ![“应用程序”列表中的 SAP Analytics Cloud 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入之前检索到的租户 URL 值。 在“机密令牌”中，输入之前检索到的访问令牌值。 单击“测试连接”以确保 Azure AD 可以连接到 InVision。 如果连接失败，请确保 SAP Analytics Cloud 帐户具有管理员权限，然后重试。

    ![屏幕截图显示“管理员凭据”对话框，可在该框中输入租户 URL 和机密令牌。](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中“发生故障时发送电子邮件通知”复选框 。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 选择“保存”。

8. 在“映射”部分下，选择“预配 Azure Active Directory 用户” 。

9. 在“属性映射”部分中，查看从 Azure AD 同步到 SAP Analytics Cloud 的用户属性。 选为“匹配”属性的特性用于匹配 SAP Analytics Cloud 中的用户帐户以执行更新操作。 如果选择更改[匹配目标属性](../app-provisioning/customize-application-attributes.md)，则需要确保 SAP Analytics Cloud API 支持基于该属性筛选用户。 选择“保存”按钮以提交任何更改。

   |Attribute|类型|支持筛选|
   |---|---|---|
   |userName|字符串|&check;|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|字符串|

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 SAP Analytics Cloud 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 SAP Analytics Cloud 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

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