---
title: 教程：使用 Azure Active Directory 为 Salesforce 配置自动用户预配 | Microsoft Docs
description: 了解为了从 Azure AD 自动将用户帐户预配到 Salesforce 以及取消其预配而需要在 Salesforce 和 Azure AD 中执行的步骤。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: c5df0a5fc054a12e3fa2ef1e352645c57c357b01
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798725"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>教程：为 Salesforce 配置自动用户预配

本教程旨在介绍为了从 Azure AD 自动将用户帐户预配到 Salesforce 以及取消其预配而需要在 Salesforce 和 Azure AD 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

* Azure Active Directory 租户
* Salesforce.com 租户

> [!Note]
> 导入角色时，不应在 Azure Active Directory 中手动编辑角色。

> [!IMPORTANT]
> 如果使用的是 Salesforce.com 试用帐户，则无法配置自动化的用户预配。 试用帐户没有所需的 API 访问权限，而只有在付费之后才拥有这种权限。 可使用免费的[开发人员帐户](https://developer.salesforce.com/signup)绕过此项限制，以完成本教程。

如果使用 Salesforce 沙盒环境，请参阅 [Salesforce 沙箱集成教程](./salesforce-sandbox-tutorial.md)。

## <a name="assigning-users-to-salesforce"></a>将用户分配到 Salesforce

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务之前，需要确定 Azure AD 中的哪些用户或组需要访问 Salesforce 应用。 确定后，可按照此处的说明将这些用户分配到 Salesforce 应用：[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>将用户分配到 Salesforce 的重要提示

* 建议将单个 Azure AD 用户分配到 Salesforce 以测试预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Salesforce 时，必须选择有效用户角色。 “默认访问权限”角色不可用于预配

    > [!NOTE]
    > 此应用会在预配过程中从 Salesforce 导入配置文件，客户在 Azure AD 中分配用户时可能会想要选择该配置文件。 请注意，从 Salesforce 导入的配置文件在 Azure AD 中显示为角色。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分将指导您完成将 Azure AD 连接到 [Salesforce 的用户帐户预配 API - v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm) 并配置预配服务，以便在 Salesforce 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

> [!Tip]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Salesforce 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-account-provisioning"></a>配置用户帐户自动预配

本部分的目的是概述如何对 Salesforce 启用 Active Directory 用户帐户的用户预配。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 Salesforce 配置单一登录，请使用搜索字段搜索 Salesforce 实例。 否则，请选择“添加”并在应用程序库中搜索“Salesforce”。 从搜索结果中选择 Salesforce，并将其添加到应用程序列表。

3. 选择 Salesforce 实例，然后选择“预配”选项卡。

4. 将“预配模式”  设置为“自动”  。

    ![屏幕截图显示 Salesforce“预配”页，其中“预配模式”设置为“自动”或者可以设置的其他值。](./media/salesforce-provisioning-tutorial/provisioning.png)

5. 在“管理员凭据”部分中，提供以下配置设置：

    a. 在“管理员用户名”文本框中，键入在 Salesforce.com 中已分配“系统管理员”配置文件的 Salesforce 帐户名称。

    b. 在“管理员密码”文本框中，键入此帐户的密码。

6. 若要获取 Salesforce 安全令牌，请打开新选项卡并登录到同一个 Salesforce 管理员帐户。 在页面右上角单击你的名字，然后单击“设置”。

    ![屏幕截图显示选择了“设置”链接](./media/salesforce-provisioning-tutorial/sf-my-settings.png "启用自动预配")。

7. 在左侧导航窗格中，单击“我的个人信息”展开相关部分，然后单击“重置我的安全令牌”。
  
    ![屏幕截图显示从“我的个人信息”选择了“重置我的安全令牌”。](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "启用自动预配")

8. 在“重置安全令牌”页上，单击“重置安全令牌”按钮。

    ![屏幕截图显示“重置安全令牌”页，其中包含说明性文本和用于重置安全令牌的选项](./media/salesforce-provisioning-tutorial/sf-reset-token.png "启用自动预配")

9. 查看与此管理员帐户关联的电子邮件收件箱。 查找来自 Salesforce.com 的包含新安全令牌的电子邮件。

10. 复制令牌，转到 Azure AD 窗口，然后将令牌粘贴到“机密令牌”字段中。

11. 如果 Salesforce 实例在 Salesforce 政府云中，则应输入 **租户 URL**。 否则，它是可选项。 使用格式“https://\<your-instance\>.my.salesforce.com”输入租户 URL，并将 \<your-instance\> 替换为 Salesforce 实例的名称。

12. 在 Azure 门户中，单击“测试连接”以确保 Azure AD 可以连接到 Salesforce 应用。

13. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

14. 单击“保存”   

15. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Salesforce”。

16. 在“属性映射”部分中，查看将从 Azure AD 同步到 Salesforce 的用户属性。 请注意，选为“匹配”属性的属性将用于匹配 Salesforce 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

17. 若要为 Salesforce 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

18. 单击“保存” 

> [!NOTE]
> 在 Salesforce 应用程序中预配用户后，管理员需要为其配置特定于语言的设置。 有关语言配置的详细信息，请参阅[本](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)文。

这会开始将“用户和组”部分中分配的任何用户和/或组初始同步到 Salesforce。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务对 Salesforce 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="common-issues"></a>常见问题
* 如果在授权访问 Salesforce 时遇到问题，请确保以下各项：
    * 使用的凭据具有对 Salesforce 的管理员访问权限。
    * 您使用的 Salesforce 版本支持 Web 访问（例如，开发人员、企业版、沙盒和 Salesforce 的不受限版本。）
    * 为用户启用了 Web API 访问权限。
* Azure AD 预配服务支持预配用户的语言、区域设置和时区。 这些属性位于默认属性映射中，但没有默认的源属性。 请确保选择默认的源属性，并且源属性的格式为 SalesForce 预期的格式。 例如，英语（美国） 的 localeSidKey 为 en_US。 查看[此处](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)提供的指南，以确定正确的 localeSidKey 格式。 可在[此处](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)找到 languageLocaleKey 格式。 除了确保格式正确以外，可能还需要确保为用户启用语言，如 [此处](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)所述。 
* **SalesforceLicenseLimitExceeded:** 由于用户没有可用的许可证，无法在目标应用程序中创建该用户。 为目标应用程序购买其他许可证，或查看用户分配和属性映射配置，以确保为正确的用户分配正确的属性。
* **SalesforceDuplicateUserName:** 无法预配用户，因为它的 Salesforce.com 'Username' 在其他 Salesforce.com 租户中重复。  在 Salesforce.com 中，'Username' 属性的值在所有 Salesforce.com 租户中都必须是唯一的。  默认情况下，用户在 Azure Active Directory 中的 userPrincipalName 成为其在 Salesforce.com 中的 'Username'。可以使用两个选项。  一种选择是在这另一个 Salesforce.com 租户中查找并重命名具有重复 'Username' 的用户（如果也管理这个租户）。  另一种选择是删除 Azure Active Directory 用户对您的目录集成到的 Salesforce.com 租户的访问权限。 我们将在下一次同步尝试时重试此操作。 
* **SalesforceRequiredFieldMissing:** Salesforce 要求用户提供某些属性才能成功创建或更新用户。 此用户缺少其中一个所需的属性。 确保在要预配到 Salesforce 的所有用户上填充了电子邮件和别名等属性。 您可以使用[基于属性的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)来限定不具有这些属性的用户的范围。 
* 预配到 Salesforce 的默认属性映射包括 SingleAppRoleAssignments 表达式，以将 Azure AD 中的 appRoleAssignments 映射到 Salesforce 中的 ProfileName。 确保用户在 Azure AD 中没有多个应用角色分配，因为属性映射仅支持预配一个角色。 
* Salesforce 要求电子邮件更新在进行更改之前手动批准。 因此，您可能会在预配日志中看到多个条目来更新用户的电子邮件（直到电子邮件更改获得批准）。


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](./salesforce-tutorial.md)
