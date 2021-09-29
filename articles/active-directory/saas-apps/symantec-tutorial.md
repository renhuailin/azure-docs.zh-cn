---
title: 教程：Azure Active Directory 与 Symantec Web Security Service (WSS) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Symantec Web Security Service (WSS) 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 7eb68e8e460c12336e6f10eb65701a542fcc1b3a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751889"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>教程：Azure Active Directory 与 Symantec Web Security Service (WSS) 集成

本教程介绍如何将 Symantec Web Security Service (WSS) 帐户与 Azure Active Directory (Azure AD) 帐户集成，以便 WSS 可使用 SAML 身份验证对 Azure AD 中预配的最终用户进行身份验证，并强制执行用户级或组级策略规则。

将 Symantec Web Security Service (WSS) 与 Azure AD 集成可提供以下优势：

- 从 Azure AD 门户管理 WSS 帐户使用的所有最终用户和组。

- 允许最终用户使用其 Azure AD 凭据在 WSS 中验证自己的身份。

- 对于在 WSS 帐户中定义的用户和组级策略规则启用强制执行。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 Symantec Web Security Service (WSS) 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Symantec Web Security Service (WSS) 支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>从库添加 Symantec Web Security Service (WSS)

若要配置 Symantec Web Security Service (WSS) 与 Azure AD 的集成，需将库中的 Symantec Web Security Service (WSS) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Symantec Web Security Service (WSS) 
1. 在结果面板中选择“Symantec Web Security Service (WSS)”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>为 Symantec Web Security Service (WSS) 配置并测试 Azure AD SSO

使用名为 B.Simon 的测试用户在 Symantec Web Security Service (WSS) 中配置并测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Symantec Web Security Service (WSS) 中的相关用户之间建立关联。

若要在 Symantec Web Security Service (WSS) 中配置并测试 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Symantec Web Security Service (WSS) SSO](#configure-symantec-web-security-service-wss-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Symantec Web Security Service (WSS) 测试用户](#create-symantec-web-security-service-wss-test-user)** - 在 Symantec Web Security Service (WSS) 中创建 B.Simon 的对应用户，并将该用户关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Symantec Web Security Service (WSS)”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”对话框中，执行以下步骤：

    a. 在“标识符”文本框中键入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. 在“回复 URL”文本框中键入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > 如果“标识符”和“回复 URL”的值由于某种原因不起作用，请联系 [Symantec Web Security Service (WSS) 客户端支持团队](https://www.symantec.com/contact-us)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

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

本部分将通过授予 B.Simon 访问 Symantec Web Security Service (WSS) 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Symantec Web Security Service (WSS)”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-symantec-web-security-service-wss-sso"></a>配置 Symantec Web Security Service (WSS) SSO

若要在 Symantec Web Security Service (WSS) 端配置单一登录，请参阅 WSS 联机文档。 需要将下载的“联合元数据 XML”导入到 WSS 门户。 如果配置 WSS 门户时需要帮助，请联系 [Symantec Web Security Service (WSS) 支持团队](https://www.symantec.com/contact-us)。

### <a name="create-symantec-web-security-service-wss-test-user"></a>创建 Symantec Web Security Service (WSS) 测试用户

在本部分中，将在 Symantec Web Security Service (WSS) 中创建一个名为 Britta Simon 的用户。 可在 WSS 门户中手动创建相应的最终用户名，或可等待 Azure AD 中预配的用户/组在几分钟（约 15 分钟）后同步到 WSS 门户。 使用单一登录前，必须先创建并激活用户。 将用于浏览网站的最终用户计算机的公共 IP 地址也需要在 Symantec Web Security Service (WSS) 门户中进行预配。

> [!NOTE]
> 请单击[此处](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)，获取计算机的公共 IP 地址。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Symantec Web Security Service (WSS)。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Symantec Web Security Service (WSS)”磁贴时，你应会自动登录到为其设置了 SSO 的 Symantec Web Security Service (WSS)。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Imperva Data Security 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。