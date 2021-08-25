---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 HRworks Single Sign-On 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 HRworks Single Sign-On 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2021
ms.author: jeedes
ms.openlocfilehash: 28127068210199985a280c26a34ce3aa49520a8b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>教程：Azure Active Directory 单一登录 (SSO) 与 HRworks Single Sign-On 的集成

本教程介绍如何将 HRworks Single Sign-On 与 Azure Active Directory (Azure AD) 集成。 将 HRworks Single Sign-On 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 HRworks Single Sign-On。
* 可让用户使用其 Azure AD 帐户自动登录 HRworks Single Sign-On。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 HRworks Single Sign-On 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* HRworks Single Sign-On 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-hrworks-single-sign-on-from-the-gallery"></a>从库中添加 HRworks Single Sign-On

若要配置 HRworks Single Sign-On 与 Azure AD 的集成，需要从库中将 HRworks Single Sign-On 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“HRworks Single Sign-On”   。
1. 从结果面板中选择“HRworks Single Sign-On”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-hrworks-single-sign-on"></a>配置并测试 HRworks Single Sign-On 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 HRworks Single Sign-On 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 HRworks Single Sign-On 中的相关用户之间建立链接关系。

若要配置并测试 HRworks Single Sign-On 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 HRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 HRworks Single Sign-On 测试用户](#create-hrworks-single-sign-on-test-user)** - 在 HRworks Single Sign-On 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“HRworks Single Sign-On”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://login.hrworks.de/?companyId=<COMPANY_ID>&directssologin=true`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [HRworks Single Sign-On 客户端支持团队](https://www.hrworks.de/dienstleistungen/support/)获取该值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 HRworks Single Sign-On”部分，根据要求复制相应的 URL  。

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

在本部分，我们通过授予 B.Simon 访问 HRworks Single Sign-On 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“HRworks Single Sign-On”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-hrworks-single-sign-on-sso"></a>配置 HRworks Single Sign-On SSO

1. 若要在 HRworks Single Sign-On 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 HRworks Single Sign-On”，此时会将你定向到 HRworks Single Sign-On 应用程序  。 在此处提供管理员凭据以登录到 HRworks Single Sign-On。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-4。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 HRworks Single Sign-On，请打开新的 Web 浏览器窗口，以管理员身份登录到 HRworks Single Sign-On 公司站点，并执行以下步骤：

1. 在菜单栏的左侧单击“管理员” > “基本信息” > “安全性” > “单一登录”，并执行以下步骤：    

    ![配置单一登录](./media/hrworks-single-sign-on-tutorial/configure.png)

    a. 选中“使用单一登录”框。 

    b. 选择“XML 元数据”作为“元数据输入方法”。  

    c. 选择“单个 NameID 标识符”作为“NameID 的值”。  

    d. 在记事本中，打开从 Azure 门户下载的元数据 XML，复制其内容，然后将其粘贴到“元数据”文本框中。 

    e. 单击“ **保存**”。

### <a name="create-hrworks-single-sign-on-test-user"></a>创建 HRworks Single Sign-On 测试用户

若要启用 Azure AD 用户，请登录到 HRworks Single Sign-On。必须在 HRworks Single Sign-On 中预配这些用户。 在 HRworks Single Sign-On 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 HRworks Single Sign-On。

1. 在菜单栏的左侧单击“管理员” > “人员” > “人员” > “新建人员”。    

     ![屏幕截图显示“HRworks”页面，其中已选择“人员”和“新建人员”。](./media/hrworks-single-sign-on-tutorial/persons.png)

1. 在弹出窗口中，单击“下一步”。 

    ![屏幕截图显示可为该人员选择的国家/地区的列表。](./media/hrworks-single-sign-on-tutorial/new-person.png)

1. 在包含国家/地区信息和法律条款的“创建新人员”弹出窗口中填写相关的详细信息，例如“名字”和“姓氏”，然后单击“创建”。    

    ![屏幕截图显示可在其中输入该人员名字和姓氏的文本框。](./media/hrworks-single-sign-on-tutorial/create-person.png)

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 HRworks Single Sign-on 登录 URL，你可以在那里启动登录流。 

* 直接转到 HRworks Single Sign-on 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“HRworks Single Sign-On”磁贴时，会重定向到 HRworks Single Sign-On 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 HRworks Single Sign-on 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
