---
title: 教程：Azure Active Directory 与 JOBHUB 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 JOBHUB 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.openlocfilehash: 5b0ed5b76aa9b097fcd4559a88822fb37e0d4788
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739460"
---
# <a name="tutorial-integrate-jobhub-with-azure-active-directory"></a>教程：将 JOBHUB 与 Azure Active Directory 集成

本教程介绍如何将 JOBHUB 与 Azure Active Directory (Azure AD) 集成。 将 JOBHUB 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 JOBHUB。
* 让用户使用其 Azure AD 帐户自动登录到 JOBHUB。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 JOBHUB 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 JOBHUB 支持 SP  发起的 SSO。

## <a name="adding-jobhub-from-the-gallery"></a>从库中添加 JOBHUB

若要配置 JOBHUB 与 Azure AD 的集成，需要从库中将 JOBHUB 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“JOBHUB”   。
1. 从结果面板中选择“JOBHUB”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户配置和测试 JOBHUB 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 JOBHUB 相关用户之间建立链接关系。

若要配置和测试 JOBHUB 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. [配置 JOBHUB SSO](#configure-jobhub-sso)  - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 JOBHUB 测试用户](#create-jobhub-test-user)  - 在 JOBHUB 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“JOBHUB”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：在“登录 URL”  文本框中，使用以下模式键入 URL：`https://pasona.jobhub.jp/saml/init`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [JOBHUB 客户端支持团队](mailto:platform@pasonagroup.co.jp)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 JOBHUB”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-jobhub-sso"></a>配置 JOBHUB SSO

若要在 JOBHUB  端配置单一登录，需要将“指纹”值以及从 Azure 门户复制的相应 URL 发送给 [JOBHUB 支持团队](mailto:platform@pasonagroup.co.jp)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

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

在本部分中，通过授予 Britta Simon 访问 JOBHUB 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“JOBHUB”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-jobhub-test-user"></a>创建 JOBHUB 测试用户

在本部分中，将在 JOBHUB 中创建一个名为“Britta Simon”的用户。 请与 [JOBHUB 支持团队](mailto:platform@pasonagroup.co.jp)协作，将用户添加到 JOBHUB 平台。 使用单一登录前，必须先创建并激活用户。

### <a name="test-sso"></a>测试 SSO

在访问面板中选择“JOBHUB”磁贴时，应会自动登录到设置了 SSO 的 JOBHUB。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)