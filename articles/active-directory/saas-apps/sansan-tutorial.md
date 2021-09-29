---
title: 教程：Azure Active Directory 与 Sansan 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Sansan 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: jeedes
ms.openlocfilehash: be146cba31d1eb6f22db8788f7e71ed60f9ca52a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627646"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>教程：将 Sansan 与 Azure Active Directory 集成

本教程介绍如何将 Sansan 与 Azure Active Directory (Azure AD) 集成。 将 Sansan 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Sansan。
* 让用户使用其 Azure AD 帐户自动登录到 Sansan。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Sansan 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。
* Sansan 支持 **SP** 发起的 SSO。

## <a name="add-sansan-from-the-gallery"></a>从库中添加 Sansan

若要配置 Sansan 与 Azure AD 的集成，需要从库中将 Sansan 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Sansan”。
1. 从结果面板中选择“Sansan”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sansan"></a>配置并测试 Sansan 的 Azure AD SSO

使用名为 Britta Simon 的测试用户配置和测试 Sansan 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Sansan 相关用户之间建立链接关系。

若要配置并测试 Sansan 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[配置 Sansan SSO](#configure-sansan-sso)** ，以在应用程序端配置 SSO 设置。
   1. **[创建 Sansan 测试用户](#create-sansan-test-user)**，以在 Sansan 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Sansan 应用程序集成页上，找到“管理”部分，选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，执行以下步骤：

   1. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://ap.sansan.com/saml2/<COMPANY_NAME>`

   1. 在“回复 URL”文本框中，使用以下模式之一键入 URL：
    
       | 环境 | 代码 |
      |:--- |:--- |
      | PC |`https://ap.sansan.com/v/saml2/<COMPANY_NAME>/acs` |
      | Smartphone 应用 |`https://internal.api.sansan.com/saml2/<COMPANY_NAME>/acs` |
      | Smartphone Web |`https://ap.sansan.com/s/saml2/<COMPANY_NAME>/acs` |

   1. 在“登录 URL”文本框中，键入 URL：`https://ap.sansan.com/`

    > [!NOTE]
    > 这些不是实际值。 检查“Sansan 管理员设置”上的实际标识符并恢复 URL 值。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Sansan”部分中，根据要求复制相应 URL。

   ![复制配置 URL](common/copy-configuration-urls.png)

   ```Logout URL
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0
    ```

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`BrittaSimon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Sansan 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Sansan”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-sansan-sso"></a>配置 Sansan SSO

若要在 Sansan 端执行单一登录设置，请根据需要执行以下步骤 。

   * [日语](https://jp-help.sansan.com/hc/ja/articles/900001551383 )版本。

   * [英语](https://jp-help.sansan.com/hc/en-us/articles/900001551383 )版本。


### <a name="create-sansan-test-user"></a>创建 Sansan 测试用户

在本部分中，会在 Sansan 中创建一个名为“Britta Simon”的用户。 若要详细了解如何创建用户，请参阅[此处](https://jp-help.sansan.com/hc/articles/206508997-Adding-users)的步骤。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Sansan 登录 URL，可以从那里启动登录流。 

* 直接转到 Sansan 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Sansan”磁贴时，系统会重定向到 Sansan 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Sansan 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
