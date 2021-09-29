---
title: 教程：Azure Active Directory 与 Pingboard 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Pingboard 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: a4f72b89d24f3563f47a1e3ed94d29c6fe7665ff
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770028"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>教程：Azure Active Directory 与 Pingboard 集成

本教程介绍如何将 PingBoard 与 Azure Active Directory (Azure AD) 集成。 将 Pingboard 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Pingboard。
* 让用户可使用其 Azure AD 帐户自动登录到 Pingboard。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Pingboard 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Pingboard 支持 SP 和 IDP 发起的 SSO。

* Pingboard 支持[自动用户预配](./pingboard-provisioning-tutorial.md)。 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-pingboard-from-the-gallery"></a>从库中添加 Pingboard

要配置 Pingboard 与 Azure AD 的集成，需要从库中将 Pingboard 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Pingboard”。
1. 从结果面板中选择“Pingboard”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-pingboard"></a>配置并测试 Pingboard 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Pingboard 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Pingboard 相关用户之间建立关联。

若要配置并测试 Pingboard 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Pingboard SSO](#configure-pingboard-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Pingboard 测试用户](#create-pingboard-test-user) - 在 Pingboard 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Pingboard”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中键入 URL：`http://app.pingboard.com/sp`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<ENTITY_ID>.pingboard.com/auth/saml/consume`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.pingboard.com/sign_in`

    > [!NOTE]
    > 这些不是实际值。 使用实际的回复 URL 和登录 URL 更新这些值。 请联系 [Pingboard 客户端支持团队](https://support.pingboard.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Pingboard”部分，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Pingboard 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Pingboard”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-pingboard-sso"></a>配置 Pingboard SSO

1. 若要在 Pingboard 端配置 SSO，请打开新的浏览器窗口，并登录到 Pingboard 帐户。 必须是 Pingboard 管理员才能设置单一登录。

2. 从顶部菜单中选择“应用”>“集成” 

    ![配置单一登录](./media/pingboard-tutorial/integration.png)

3. 在“集成”  页上，找到“Azure Active Directory”  磁贴，并单击它。

    ![Pingboard 单一登录集成](./media/pingboard-tutorial/directory.png)

4. 在后面的模式中，单击“配置” 

    ![Pingboard 配置按钮](./media/pingboard-tutorial/configure.png)

5. 在以下页上，会出现“Azure SSO 集成已启用”。 在记事本中打开下载的元数据 XML 文件，并将其内容粘贴到 **IDP 元数据**。

    ![Pingboard SSO 配置界面](./media/pingboard-tutorial/metadata.png)

6. 验证文件后，如果所有内容都正确，则会立即启用单一登录。

### <a name="create-pingboard-test-user"></a>创建 Pingboard 测试用户

本部分的目的是在 Pingboard 中创建名为“Britta Simon”的用户。 Pingboard 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](pingboard-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤： 

1. 以管理员身份登录到 Pingboard 公司站点。

2. 单击“目录”页上的“添加员工”按钮。

    ![添加员工](./media/pingboard-tutorial/test-user.png)

3. 在“添加员工”对话框页上，执行以下步骤  ：

    ![邀请人员](./media/pingboard-tutorial/create-name.png)

    a. 在“全名”文本框中，键入用户的全名，如 Britta Simon   。

    b. 在“电子邮件”文本框中，键入用户的电子邮件地址，如 brittasimon@contoso.com。

    c. 在“职务”  文本框中，键入 Britta Simon 的职务。

    d. 在“位置”  下拉列表中，选择 Britta Simon 的位置。

    e. 单击“添加”  。

4. 确认界面随即出现，以便确认添加用户。

    ![确认](./media/pingboard-tutorial/confirm-user.png)

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Pingboard 登录 URL，你可以从那里启动登录流。  

* 直接转到 Pingboard 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Pingboard。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Pingboard”磁贴时，如果是在 SP 模式下配置的，你将会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Pingboard。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Pingboard 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。