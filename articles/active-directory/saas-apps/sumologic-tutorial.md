---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SumoLogic 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Sugar SumoLogic 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: ecfaec5c9ea935efa67846f7c95f2d56a879cebc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124752113"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SumoLogic 集成

本教程介绍如何将 SumoLogic 与 Azure Active Directory (Azure AD) 进行集成。 将 SumoLogic 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SumoLogic。
* 让用户使用其 Azure AD 帐户自动登录到 SumoLogic。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 SumoLogic 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SumoLogic 支持 IDP 发起的 SSO。

## <a name="add-sumologic-from-the-gallery"></a>从库中添加 SumoLogic

若要配置 SumoLogic 与 Azure AD 的集成，需要从库中将 SumoLogic 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 SumoLogic   。
1. 从结果面板中选择 SumoLogic，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sumologic"></a>配置并测试 SumoLogic 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 SumoLogic 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 SumoLogic 相关用户之间建立链接关系。

若要配置并测试 SumoLogic 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SumoLogic SSO](#configure-sumologic-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SumoLogic 测试用户](#create-sumologic-test-user)** - 在 SumoLogic 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 SumoLogic 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    a. 在“标识符”文本框中，使用以下模式之一键入 URL：

    | 标识符 URL |
    |---|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|
    |

    b. 在“回复 URL”文本框中，使用以下模式之一键入 URL：

    | 回复 URL |
    |---|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>` |
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>` |
    |

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [SumoLogic 客户端支持团队](https://www.sumologic.com/contact-us/)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. SumoLogic 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，SumoLogic 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    |  名称 | 源属性 |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | 角色 | user.assignedroles |

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请单击[此处](../develop/active-directory-enterprise-app-role-management.md)  。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 SumoLogic”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 SumoLogic 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SumoLogic”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name=&quot;configure-sumologic-sso&quot;></a>配置 SumoLogic SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 SumoLogic 公司站点。

1. 转到“管理” -> “安全性” 。

    ![管理](./media/sumologic-tutorial/security.png &quot;管理")

1. 单击“SAML”。 

    ![全局安全设置](./media/sumologic-tutorial/settings.png "全局安全设置")

1. 从“选择配置或创建新配置”  列表中，选择“Azure AD”  ，并单击“配置”  。

    ![显示配置 SAML 2.0 的屏幕截图，你可以在其中选择 Azure AD。](./media/sumologic-tutorial/configure.png "配置 SAML 2.0")

1. 在“配置 SAML 2.0”  对话框上，执行以下步骤：

    ![显示“配置 SAML 2.0”对话框的屏幕截图，你可以在其中输入所述值。](./media/sumologic-tutorial/configuration.png "配置 SAML 2.0")

    a. 在“配置名称”  文本框中，键入“Azure AD”  。

    b. 选择“调试模式”  。

    c. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在“身份验证请求 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    e. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。 

    f. 对于“电子邮件属性”  ，选择“使用 SAML 主题”  。  

    g. 选择“SP 发起的登录配置”  。

    h. 在“登录路径”  文本框中，键入“Azure”  ，并单击“保存”  。

### <a name="create-sumologic-test-user"></a>创建 SumoLogic 测试用户

若要使 Azure AD 用户能够登录到 SumoLogic，必须将其预配到 SumoLogic 中。 对于 SumoLogic，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **SumoLogic** 租户。

1. 转到“管理”\>“用户”  。

    ![显示从“管理”菜单选择了“用户”的屏幕截图。](./media/sumologic-tutorial/user.png "用户")

1. 单击“添加”  。

    ![显示“用户”的“添加”按钮的屏幕截图。](./media/sumologic-tutorial/add-user.png "用户")

1. 在“新建用户”  对话框中，执行以下步骤：

    ![新建用户](./media/sumologic-tutorial/new-account.png "新建用户")

    a. 将要预配的 Azure AD 帐户的相关信息键入到“名字”  、“姓氏”  和“电子邮件”  文本框中。
  
    b. 选择角色。
  
    c. 对于“状态”  ，选择“活动”  。
  
    d. 单击“ **保存**”。

> [!NOTE]
> 可以使用 SumoLogic 提供的任何其他 SumoLogic 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 SumoLogic。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 SumoLogic 磁贴时，应会自动登录到为其设置了 SSO 的 SumoLogic。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 SumoLogic 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。