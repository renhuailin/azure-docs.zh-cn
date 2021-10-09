---
title: 教程：Azure AD SSO 与 Hub Planner 集成
description: 了解如何在 Azure Active Directory 与 Hub Planner 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: 66cbb4fa8e5f80e56b66b66802fb4c4b7ab8b9d2
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091609"
---
# <a name="tutorial-azure-ad-sso-integration-with-hub-planner"></a>教程：Azure AD SSO 与 Hub Planner 集成

本教程介绍如何将 Hub Planner 与 Azure Active Directory (Azure AD) 集成。 将 Hub Planner 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Hub Planner。
* 让用户使用其 Azure AD 帐户自动登录到 Hub Planner。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Hub Planner 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Hub Planner 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-hub-planner-from-the-gallery"></a>从库中添加 Hub Planner

若要配置 Hub Planner 与 Azure AD 的集成，需从库中将 Hub Planner 添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Hub Planner” 。
1. 从结果面板中选择“Hub Planner”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-hub-planner"></a>配置并测试 Hub Planner 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Hub Planner 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Hub Planner 相关用户之间建立关联。

若要配置并测试 Hub Planner 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Hub Planner SSO](#configure-hub-planner-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Hub Planner 测试用户](#create-hub-planner-test-user)** - 在 Hub Planner 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Hub Planner”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符”框中，使用以下模式键入 URL：`https://app.hubplanner.com/sso/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://app.hubplanner.com/sso/callback`

    c. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.hubplanner.com`。

    > [!NOTE]
    > 这些值是将使用的值。 需要进行的唯一更改是将“登录 URL”中的 \<SUBDOMAIN\> 替换为在注册 Hub Planner 时收到的子域。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Hub Planner”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Hub Planner 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中选择“Hub Planner”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-hub-planner-sso"></a>配置 Hub Planner SSO

若要在“Hub Planner”端配置单一登录，需要登录 Hub Planner 帐户并完成以下任务。 

### <a name="install-the-extension-in-hub-planner"></a>在 Hub Planner 中安装扩展

若要启用 SSO 功能，必须首先启用该扩展。 以帐户所有者的身份或具有等效权限的用户身份完成以下步骤：

1. 转到“设置”。
1. 在侧边菜单中，选择“管理扩展” > “添加/删除扩展” 。
1. 找到单一登录的扩展，然后添加或免费试用它。
1. 出现系统提示时选择同意条款和条件，然后选择“立即添加”。

### <a name="enable-sso"></a>启用 SSO

启用扩展后，必须为你的帐户启用 SSO。 

1. 转到“设置”。
1. 在侧边菜单中，选择“身份验证”。
1. 选择“SSO (单一登录)”。
1. 按下图所示输入其他身份验证信息，然后选择“保存”。

![SSO 设置的屏幕截图](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>创建 Hub Planner 测试用户

如果要添加其他用户，请转到“设置” > 管理资源”，然后从此处添加用户 。 请确保添加他们的电子邮件地址并向他们发出邀请。 发出邀请后，他们将收到一封电子邮件，并且可通过 SSO 进入。 

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Hub Planner 登录 URL，可以从那里启动登录流。 

* 直接转到 Hub Planner 登录 URL，从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Hub Planner”磁贴时，会重定向到 Hub Planner 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Hub Planner 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。