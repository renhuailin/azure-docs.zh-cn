---
title: 教程：Azure Active Directory 与 Adobe Experience Manager 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Adobe Experience Manager 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 0592404cbc7a2960466fb84d7739c0787dbaa043
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803263"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>教程：Azure Active Directory 与 Adobe Experience Manager 的集成

本教程介绍如何将 Adobe Experience Manager 与 Azure Active Directory (Azure AD) 集成。 将 Adobe Experience Manager 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Adobe Experience Manager。
* 让用户使用 Azure AD 帐户自动登录到 Adobe Experience Manager。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Adobe Experience Manager 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Adobe Experience Manager 支持 **SP 和 IDP** 发起的 SSO

* Adobe Experience Manager 支持 **实时** 用户预配

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>从库中添加 Adobe Experience Manager

若要配置 Adobe Experience Manager 与 Azure AD 的集成，需要将库中的 Adobe Experience Manager 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Adobe Experience Manager”。
1. 从结果面板中选择“Adobe Experience Manager”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>配置和测试 Adobe Experience Manager 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Adobe Experience Manager 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Adobe Experience Manager 中的相关用户之间建立链接关系。

若要配置和测试 Adobe Experience Manager 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. [配置 Adobe Experience Manager SSO](#configure-adobe-experience-manager-sso) - 在应用程序端配置单一登录设置。
    1. **[创建 Adobe Experience Manager 测试用户](#create-adobe-experience-manager-test-user)** - 在 Adobe Experience Manager 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Adobe Experience Manager”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，同样键入已在 AEM 服务器上定义的唯一值  。

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > 答复 URL 值不是真实值。 请将回复 URL 值更新为实际回复 URL。 若要获取此值，请联系 [Adobe Experience Manager 客户端支持团队](https://helpx.adobe.com/support/experience-manager.html)来获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 Adobe Experience Manager 服务器 URL  。

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Adobe Experience Manager”  部分中，根据要求复制相应的 URL。

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

本部分的步骤通过向 B.Simon 授予对 Adobe Experience Manager 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Adobe Experience Manager”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-adobe-experience-manager-sso"></a>配置 Adobe Experience Manager SSO

1. 在另一个浏览器窗口中打开“Adobe Experience Manager”管理门户  。

2. 选择“设置” > “安全” > “用户”。

    ![显示 Adobe Experience Manager 中“用户”磁贴的屏幕截图。](./media/adobe-experience-manager-tutorial/user-1.png)

3. 选择“管理员”或其他任何相关用户  。

    ![突出显示“管理员”用户的屏幕截图。](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. 选择“帐户设置” > “管理信任存储”。

    ![显示“帐户设置”下的“管理信任存储”的屏幕截图。](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. 在“从 CER 文件添加证书”中单击“选择证书文件”   。 浏览并选择已从 Azure 门户下载的证书文件。

    ![突出显示“选择证书文件”按钮的屏幕截图。](./media/adobe-experience-manager-tutorial/user-2.png)

6. 证书随即已添加到信任存储。 请记下证书的别名。

    ![显示证书随即已添加到信任存储的屏幕截图。](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. 在“用户”页上，选择“身份验证服务”   。

    ![突出显示屏幕上“身份验证服务”的屏幕截图。](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. 选择“帐户设置” > “创建/管理密钥存储”。 通过提供密码创建密钥存储。

    ![突出显示“管理密钥存储”的屏幕截图。](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. 返回管理屏幕。 然后选择“设置” > “操作” > “Web 控制台”。

    ![突出显示“设置”部分中“操作”下的“Web 控制台”的屏幕截图。](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    此时会打开配置页。

    ![配置单一登录“保存”按钮](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. 找到“Adobe Granite SAML 2.0 身份验证处理程序”  。 然后选择“添加”图标  。

    ![突出显示“Adobe Granite SAML 2.0 身份验证处理程序”的屏幕截图。](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. 在此页上执行以下操作。

    ![配置单一登录“保存”按钮](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. 在“路径”框中，输入 **/**。

    b. 在“IDP URL”框中，输入从 Azure 门户复制的“登录 URL”值   。

    c. 在“IDP 证书别名”框中，输入在信任存储中添加的“证书别名”值   。

    d. 在“安全提供的实体 ID”文本框中，输入已在 Azure 门户中配置的唯一“Azure AD 标识符”值   。

    e. 在“断言使用者服务 URL”框中，输入已在 Azure 门户中配置的“回复 URL”值   。

    f. 在“密钥存储的密码”框中，输入已在密钥存储中设置的“密码”   。

    g. 在“用户属性 ID”框中，输入“名称 ID”或与具体情况相关的其他用户 ID   。

    h. 选择“自动创建 CRX 用户”。 

    i. 在“注销 URL”框中，输入从 Azure 门户获取的“注销 URL”值   。

    j. 选择“保存”。 

### <a name="create-adobe-experience-manager-test-user"></a>创建 Adobe Experience Manager 测试用户

本部分的步骤在 Adobe Experience Manager 中创建名为 Britta Simon 的用户。 如果已选择“自动创建 CRX 用户”选项，则在成功完成身份验证后，会自动创建这些用户  。

若要手动创建用户，请与 [Adobe Experience Manager 支持团队](https://helpx.adobe.com/support/experience-manager.html)配合，在 Adobe Experience Manager 平台中添加用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Adobe Experience Manager 登录 URL，可以从那里启动登录流。  

* 直接转到 Adobe Experience Manager 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Adobe Experience Manager 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Adobe Experience Manager 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Adobe Experience Manager。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 Adobe Experience Manager 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。