---
title: 教程：Azure Active Directory 与 FreshDesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 FreshDesk 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 53ab7bcc6944d3f7650018238bd326037c5fff41
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746932"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教程：Azure Active Directory 与 FreshDesk 集成

本教程介绍如何将 FreshDesk 与 Azure Active Directory (Azure AD) 集成。 将 FreshDesk 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 FreshDesk。
* 让用户使用其 Azure AD 帐户自动登录到 FreshDesk。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：
 
* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 FreshDesk 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* FreshDesk 支持 **SP** 发起的 SSO

## <a name="add-freshdesk-from-the-gallery"></a>从库中添加 FreshDesk

要配置 FreshDesk 与 Azure AD 的集成，需要从库中将 FreshDesk 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“FreshDesk” 。
1. 从结果面板中选择“FreshDesk”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>配置并测试 FreshDesk 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 FreshDesk 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 FreshDesk 相关用户之间建立关联。

若要配置并测试 FreshDesk 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[配置 FreshDesk SSO](#configure-freshdesk-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 FreshDesk 测试用户](#create-freshdesk-test-user)** - 在 FreshDesk 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

1. 在 Azure 门户中的“FreshDesk”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com`，或者键入 Freshdesk 建议的其他值。
     
    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 若要获取这些值，请与 [FreshDesk 客户端支持团队](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. FreshDesk 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 下面的屏幕截图显示默认属性的列表，而“唯一用户标识符”与 user.userprincipalname 映射，但 FreshDesk 希望此声明与 user.mail 映射，因此需要通过单击“编辑”图标来编辑属性映射，然后更改属性映射。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 FreshDesk”部分中，根据要求复制相应 URL。

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

本部分将通过授予 B.Simon 访问 FreshDesk 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“FreshDesk”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-freshdesk-sso&quot;></a>配置 FreshDesk SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Freshdesk 公司站点。

2. 选择“安全性”图标，在“安全性”部分中，执行以下步骤：

    ![单一登录](./media/freshdesk-tutorial/configure-1.png &quot;单一登录")
  
    a. 对于“单一登录”，请选择“启用”。 

    b. 在“登录方法”中，选择“SAML SSO”。

    c. 在“IdP 提供的实体 ID”文本框中，粘贴从 Azure 门户复制的“实体 ID”值 。

    d. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    e. 在“签名选项”中，从下拉菜单中选择“仅限已签名的断言”。

    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    g. 在“安全证书”文本框中，粘贴你之前获得的“证书(Base64)”值。
  
    h. 单击“ **保存**”。

## <a name="create-freshdesk-test-user"></a>创建 FreshDesk 测试用户

为使 Azure AD 用户能够登录到 FreshDesk，必须将其预配到 FreshDesk。  
就 FreshDesk 而言，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到“Freshdesk”租户。

1. 在左侧菜单中，单击“管理”，然后在“常规设置”选项卡中单击“代理”。
  
    ![代理](./media/freshdesk-tutorial/create-user-1.png "代理")

1. 单击“新建代理”。

    ![新建代理](./media/freshdesk-tutorial/create-user-2.png "新建代理")

1. 在“代理信息”对话框中，输入必填字段，然后单击“创建代理”。

    ![代理信息](./media/freshdesk-tutorial/create-user-3.png "代理信息")

    >[!NOTE]
    >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
    >
    >[!NOTE]
    >可以使用任何其他 Freshdesk 用户帐户创建工具或 Freshdesk 提供的 API 来将 Azure AD 用户帐户预配到 FreshDesk。

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 FreshDesk 登录 URL，你可以从此处启动登录流。 

* 直接转到 FreshDesk 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 FreshDesk 磁贴时，应当会自动登录到已为其设置了 SSO 的 FreshDesk。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 FreshDesk 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。