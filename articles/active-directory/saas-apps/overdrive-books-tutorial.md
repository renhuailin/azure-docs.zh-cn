---
title: 教程：Azure Active Directory 与 Overdrive 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Overdrive 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2021
ms.author: jeedes
ms.openlocfilehash: 4f521f21ad61eb0163460b552199f2ef5d7b6152
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821818"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>教程：Azure Active Directory 与 Overdrive 的集成

本教程介绍了如何将 Overdrive 与 Azure Active Directory (Azure AD) 集成。 将 Overdrive 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Overdrive。
* 让用户使用其 Azure AD 帐户自动登录到 Overdrive。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：
 
* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Overdrive 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Overdrive 支持 SP 发起的 SSO。

* Overdrive 支持“实时”用户预配。

## <a name="add-overdrive-from-the-gallery"></a>从库中添加 Overdrive

若要配置 Overdrive 与 Azure AD 的集成，请执行以下操作，从库中将 Overdrive 添加到托管 SaaS 应用列表：
 
1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左窗格中，选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Overdrive” 。
1. 在结果窗格中选择“Overdrive”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-overdrive"></a>配置并测试 Overdrive 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Overdrive 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Overdrive 相关用户之间建立关联。

若要配置并测试 Overdrive 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Overdrive SSO](#configure-overdrive-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Overdrive 测试用户](#create-overdrive-test-user)** - 在 Overdrive 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Overdrive”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    在“登录 URL”文本框中，使用以下模式键入 URL：`http://<subdomain>.libraryreserve.com`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Overdrive 客户端支持团队](https://help.overdrive.com/)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”来根据要求下载从给定选项提供的应用联合元数据 URL，并将其保存在计算机上   。

    ![证书下载链接](common/copy-metadataurl.png)

6. 在“设置 Overdrive”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Overdrive 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Overdrive”。

2. 在应用程序列表中，选择“Overdrive”。

3. 在左侧菜单中，选择“用户和组”  。

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-overdrive-sso"></a>配置 Overdrive SSO

若要在 Overdrive 端配置单一登录，需要将应用联合元数据 URL 发送给 [Overdrive 支持团队](https://help.overdrive.com/) 。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-overdrive-test-user"></a>创建 Overdrive 测试用户

在本部分，我们将在 Overdrive 中创建一个名为 Britta Simon 的用户。 Overdrive 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Overdrive 中不存在用户，则会在身份验证后创建一个新用户。

>[!NOTE]
>可以使用任何其他 OverDrive 用户帐户创建工具或 OverDrive 提供的 API 来预配 Azure AD 用户帐户。
>

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Overdrive 登录 URL，你可以从此处启动登录流。 

* 直接转到 Overdrive 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Overdrive 磁贴时，会重定向到 Overdrive 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Overdrive 后，就可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。