---
title: 教程：Azure Active Directory 与 Panorama9 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Panorama9 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 252bf6479819eb41c857a2f402dddf13fb7b8abc
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111748696"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>教程：Azure Active Directory 与 Panorama9 集成

本教程介绍如何将 Panorama9 与 Azure Active Directory (Azure AD) 集成。 将 Panorama9 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Panorama9。
* 让用户使用其 Azure AD 帐户自动登录到 Panorama9。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Panorama9 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Panorama9 支持 SP 发起的 SSO。

## <a name="add-panorama9-from-the-gallery"></a>从库中添加 Panorama9

要配置 Panorama9 与 Azure AD 的集成，需要从库中将 Panorama9 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Panorama9” 。
1. 从结果面板中选择“Panorama9”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-panorama9"></a>配置并测试 Panorama9 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Panorama9 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Panorama9 中的相关用户之间建立关联。

若要配置并测试 Panorama9 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Panorama9 SSO](#configure-panorama9-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Panorama9 测试用户](#create-panorama9-test-user) - 在 Panorama9 中创建 B.Simon 的对应帐户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Panorama9 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，键入 URL：`https://dashboard.panorama9.com/saml/access/3262`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://www.panorama9.com/saml20/<TENANT_NAME>`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Panorama9 客户端支持团队](https://support.panorama9.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

6. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

7. 在“设置 Panorama9”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Panorama9 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Panorama9”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-panorama9-sso&quot;></a>配置 Panorama9 SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Panorama9 公司站点。

2. 在顶部工具栏中，单击“管理”，并单击“扩展”。  
   
    ![扩展](./media/panorama9-tutorial/toolbar.png &quot;扩展")

3. 在“扩展”对话框中，单击“单一登录”。  
   
    ![单一登录](./media/panorama9-tutorial/extension.png "单一登录")

4. 在“设置”部分执行以下步骤： 
   
    ![设置](./media/panorama9-tutorial/configuration.png "设置")
   
    a. 在“标识提供者 URL”文本框中，粘贴已从 Azure 门户中复制的“登录 URL”值   。
   
    b. 在“证书指纹”  文本框中，粘贴从 Azure 门户复制的证书“指纹”  值。    
         
5. 单击“ **保存**”。

### <a name="create-panorama9-test-user"></a>创建 Panorama9 测试用户

若要让 Azure AD 用户登录 Panorama9，必须将其预配到 Panorama9 中。  

使用 Panorama9 时，预配属手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Panorama9** 公司站点。

2. 在顶部菜单中，单击“管理”，并单击“用户”。  
   
    ![屏幕截图显示已选择“管理”和“用户”选项卡。](./media/panorama9-tutorial/user.png "用户")

3. 在“用户”部分，单击“+”添加新用户。

    ![用户](./media/panorama9-tutorial/new-user.png "用户")

4. 转到“用户数据”部分，向“电子邮件”  文本框中键入要预配的有效 Azure Active Directory 用户的电子邮件地址。

5. 转到“用户”部分，单击“保存”  。
   
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Panorama9 登录 URL，你可以从那里启动登录流。 

* 直接转到 Panorama9 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Panorama9 磁贴时，会重定向到 Panorama9 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Panorama9 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
