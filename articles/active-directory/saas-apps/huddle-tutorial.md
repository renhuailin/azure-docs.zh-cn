---
title: 教程：Azure AD SSO 与 Huddle 集成
description: 了解如何在 Azure Active Directory 和 Huddle 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: jeedes
ms.openlocfilehash: 81d3c8382573683c09978dab11f355bf51a19c50
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669892"
---
# <a name="tutorial-azure-ad-sso-integration-with-huddle"></a>教程：Azure AD SSO 与 Huddle 集成

本教程介绍如何将 Huddle 与 Azure Active Directory (Azure AD) 集成。 将 Huddle 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Huddle。
* 让用户使用其 Azure AD 帐户自动登录到 Huddle。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Huddle 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Huddle 支持 SP 和 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-huddle-from-the-gallery"></a>从库中添加 Huddle

若要配置 Huddle 与 Azure AD 的集成，需从库中将 Huddle 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Huddle” 。
1. 从结果面板中选择“Huddle”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-huddle"></a>配置并测试 Huddle 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Huddle 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Huddle 中的相关用户之间建立关联。

若要配置并测试 Huddle 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，以使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Huddle SSO](#configure-huddle-sso)** - 在应用程序端配置 SSO 设置。
    1. **[创建 Huddle 测试用户](#create-huddle-test-user)** - 在 Huddle 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Huddle”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    > [!NOTE]
    > 将在下面输入的域中自动检测 huddle 实例。

    a. 在“标识符”文本框中，键入以下 URL 之一：

    | **Identifier** |
    |------|
    | `https://login.huddle.net` |
    | `https://login.huddle.com` |

    b. 在“回复 URL”文本框中，键入以下 URL 之一： 

    | 回复 URL |
    |----|
    | `https://login.huddle.net/saml/browser-sso` |
    | `https://login.huddle.com/saml/browser-sso` |
    | `https://login.huddle.com/saml/idp-initiated-sso` |

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL： 

    | **登录 URL** |
    |----|
    | `https://<customsubdomain>.huddle.com` |
    | `https://us.huddle.com` |
      
    > [!NOTE]
    > 登录 URL 值不是实际值。 使用实际登录 URL 更新此值。 请联系 [Huddle 客户端支持团队](https://huddle.zendesk.com)获取这些值。

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Huddle”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 B. Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B. Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B. Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Huddle 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Huddle”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，选择“用户”列表中的“B. Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-huddle-sso"></a>配置 Huddle SSO

若要在 Huddle 端配置单一登录，需要将下载的证书(Base64) 以及从 Azure 门户复制的相应 URL 发送给 [Huddle 支持团队](https://huddle.zendesk.com/)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!NOTE]
> 单一登录需要由 Huddle 支持团队启用。 配置完成后，会收到通知。

### <a name="create-huddle-test-user"></a>创建 Huddle 测试用户

为了使 Azure AD 用户能够登录到 Huddle，必须将其预配到 Huddle 中。 对于 Huddle，预配是一项手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到你的 **Huddle** 公司站点。

2. 单击 **“工作区”**。

3. 单击“人员”**\>“邀请人员”**。

    ![People](./media/huddle-tutorial/tasks.png "人员")

4. 在 **“创建新邀请”** 部分中，执行以下步骤：
  
    ![新建邀请](./media/huddle-tutorial/team.png "新建邀请")
  
    a. 在 **“选择要邀请他人加入的团队”** 列表中，选择 **“团队”**。

    b. 在“输入要邀请的人员的电子邮件地址”文本框中，键入希望预配的有效 Azure AD 帐户的“电子邮件地址”。

    c. 单击“邀请”。 

    > [!NOTE]
    > Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

> [!NOTE]
> 可以使用任何其他 Huddle 用户帐户创建工具或 Huddle 提供的 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Huddle 登录 URL，可从此处启动登录流。  

* 直接转到 Huddle 登录 URL，从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Huddle。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的 Huddle 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Huddle。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Huddle 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。