---
title: 教程：将 Azure Active Directory 与 Five9 Plus Adapter (CTI, Contact Center Agents) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Five9 Plus Adapter (CTI, Contact Center Agents) 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: c9d0faa12ffe693729ada3225f96eccde8508c58
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124834760"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>教程：将 Azure Active Directory 与 Five9 Plus Adapter (CTI, Contact Center Agents) 集成

本教程介绍如何将 Five9 Plus Adapter（CTI、联系人中心代理）与 Azure Active Directory (Azure AD) 集成。 将 Five9 plus adapter Plus Adapter（CTI、联系人中心代理）与 Azure AD  集成时，你可以：

* 在 Azure AD 中控制谁有权访问 Five9 Plus Adapter（CTI、联系人中心代理）。
* 让用户使用其 Azure AD 帐户自动登录到 Five9 Plus Adapter（CTI、联系人中心代理）。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Five9 Plus Adapter (CTI, Contact Center Agents) 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Five9 Plus Adapter（CTI、联系人中心代理）单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Five9 Plus Adapter（CTI、联系人中心代理）支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>从库添加 Five9 Plus Adapter（CTI、联系人中心代理）

若要配置 Five9 Plus Adapter (CTI, Contact Center Agents) 与 Azure AD 的集成，请从库将 Five9 Plus Adapter (CTI, Contact Center Agents) 添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Five9 Plus Adapter（CTI、联系人中心代理）” 。
1. 在结果面板中，选择“Five9 Plus Adapter（CTI、联系人中心代理）”，然后添加该应用程序。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>配置和测试 Five9 Plus Adapter（CTI、联系人中心代理）的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Five9 Plus Adapter（CTI、联系人中心代理）的 Azure AD SSO。 若要正常运行 SSO，你需要在 Azure AD 用户与 Five9 Plus Adapter（CTI、联系人中心代理）相关用户之间建立链接关系。

若要配置 Five9 Plus Adapter（CTI、联系人中心代理）的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Five9 Plus Adapter（CTI、联系人中心代理）SSO](#configure-five9-plus-adapter-cti-contact-center-agents-sso)：在应用程序端配置单一登录设置。
    1. [创建 Five9 Plus Adapter（CTI、联系人中心代理）测试用户](#create-five9-plus-adapter-cti-contact-center-agents-test-user)：在 Five9 Plus Adapter（CTI、联系人中心代理）创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Five9 Plus Adapter（CTI、联系人中心代理）应用程序集成页中，选择“管理”部分和“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤：

    a. 在“标识符”文本框中，键入以下 URL 之一： 
    
    |    环境      |       代码      |
    | :-- | :-- |
    | 适用于 Five9 Plus Adapter for Microsoft Dynamics CRM | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | 适用于 Five9 Plus Adapter for Zendesk | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | 适用于 Five9 Plus Adapter for Agent Desktop Toolkit | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. 在“回复 URL”文本框中，键入以下 URL 之一： 

    |      环境     |      代码      |
    | :--                  | :--           |
    | 适用于 Five9 Plus Adapter for Microsoft Dynamics CRM | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | 适用于 Five9 Plus Adapter for Zendesk | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | 适用于 Five9 Plus Adapter for Agent Desktop Toolkit | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Five9 Plus Adapter (CTI, Contact Center Agents)”部分，根据要求复制相应 URL。

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

在本部分中，通过授予 B.Simon 访问 Five9 Plus Adapter（CTI、联系人中心代理）的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Five9 Plus Adapter (CTI, Contact Center Agents)”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>配置 Five9 Plus Adapter（CTI、联系人中心代理）SSO

1. 若要在“Five9 Plus Adapter (CTI, Contact Center Agents)”端配置单一登录，需要将下载的“证书 (Base64)”和复制的相应 URL 发送到 [Five9 Plus Adapter (CTI, Contact Center Agents) 支持团队](https://www.five9.com/about/contact)。 此外，若要进一步配置 SSO，请根据适配器遵循以下步骤：

    a. “Five9 Plus Adapter for Agent Desktop Toolkit”管理员指南：[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. “Five9 Plus Adapter for Microsoft Dynamics CRM”管理员指南：[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. “Five9 Plus Adapter for Zendesk”管理员指南：[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>创建 Five9 Plus Adapter (CTI, Contact Center Agents) 测试用户

本部分在 Five9 Plus Adapter (CTI, Contact Center Agents) 中创建一个名为 Britta Simon 的用户。 通过 [Five9 Plus Adapter (CTI, Contact Center Agents) 支持团队](https://www.five9.com/about/contact)在 Five9 Plus Adapter (CTI, Contact Center Agents) 平台添加用户。 使用单一登录前，必须先创建并激活用户。 

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置 SSO 的 Five9 Plus Adapter（CTI、联系人中心代理）。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Five9 Plus Adapter（CTI、联系人中心代理）”磁贴时，应自动登录到为其设置 SSO 的 Five9 Plus Adapter（CTI、联系人中心代理）。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Five9 Plus Adapter（CTI、联系人中心代理）后，你可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。