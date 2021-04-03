---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 New Relic 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 New Relic 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504043"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>教程：Azure Active Directory 单一登录 (SSO) 与 New Relic 集成

本教程介绍如何将 New Relic 与 Azure Active Directory (Azure AD) 集成。 将 New Relic 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 New Relic。
* 让用户使用其 Azure AD 帐户自动登录到 New Relic。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个启用了单一登录 (SSO) 的 New Relic 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* New Relic 支持由标识提供商或标识提供者发起的 SSO。
* 配置 New Relic 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-new-relic-from-the-gallery"></a>从库中添加 New Relic

若要配置 New Relic 与 Azure AD 的集成，需要从库中将 New Relic (By Organization) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 **Azure Active Directory** 服务。
1. 选择“企业应用程序” > “新应用程序” 。
1. 在“浏览 Azure AD 库”页面上，在搜索框中键入“New Relic (By Organization)” 。
1. 从结果中选择“New Relic (By Organization)”，然后选择“创建” 。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>配置并测试 New Relic 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 New Relic 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 New Relic 中的相关用户之间建立链接关系。

若要配置并测试 New Relic 的 Azure AD SSO：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
   1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
   1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 New Relic SSO](#configure-new-relic-sso) - 在 New Relic 端配置单一登录设置。
   1. [创建 New Relic 测试用户](#create-a-new-relic-test-user) - 在 New Relic 中创建 B.Simon 的对应用户，并将其链接到 Azure AD 用户。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在“New Relic by Organization”应用程序集成页上，找到“管理”部分 。 然后选择“单一登录”。

1. 在“选择单一登录方法”页上选择“SAML”   。

1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置   。

   ![“设置 SAML 单一登录”的屏幕截图，其中突出显示了笔形图标。](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，填入“标识符”和“回复 URL”的值  。

   * 使用 New Relic“我的组织”应用程序检索这些值。 若要使用此应用程序，请执行以下操作：
      1. [登录](https://login.newrelic.com/) New Relic。
      1. 在顶部菜单中，选择“应用”。
      1. 在“你的应用”部分中，选择“我的组织” > “身份验证域”  。
      1. 选择要 Azure AD SSO 连接到的身份验证域（如果有多个身份验证域）。 大多数公司只有一个称为“默认”的身份验证域。 如果只有一个身份验证域，则无需选择任何内容。
      1. 在“身份验证”部分中，“断言使用者 URL”包含用于“回复 URL”的值  。
      1. 在“身份验证”部分中，“实体 ID”包含用于“标识符”的值  。

1. 在“用户属性和声明”部分中，确保将“唯一用户标识符”映射到包含 New Relic 中使用的电子邮件地址的字段 。

   * 如果默认字段“user.userprincipalname”的值与 New Relic 电子邮件地址相同，则该字段适用。
   * 如果“user.userprincipalname”不是 New Relic 电子邮件地址，则字段“user.mail”可能更适用 。

1. 在“SAML 签名证书”部分中，复制“应用联合元数据 URL”并保存其值，以供以后使用 。

1. 在“设置 New Relic by Organization”部分中，复制“登录 URL”并保存其值，以供以后使用 。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

以下介绍如何在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户中，选择“Azure Active Directory”。
1. 选择“用户” > “新建用户” 。
1. 在“新建用户”页面上：
   1. 在“用户名”字段中输入 `username@companydomain.extension`。 例如，`b.simon@contoso.com`。 这应该与将在 New Relic 端使用的电子邮件地址匹配。
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 选择“显示密码”并保存显示的值。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

以下介绍如何通过授予 B.Simon 访问 New Relic By Organization 应用程序的权限，使其能够使用 Azure AD 单一登录。

1. 在 Azure 门户中，选择“Azure Active Directory”。
1. 选择“企业应用程序” > “New Relic by Organization” 。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。

   ![“管理”部分的屏幕截图，其中突出显示了“用户和组”。](common/users-groups-blade.png)

1. 选择“添加用户”。 在“添加分配”中，选择“用户和组”（或“用户”，具体取决于计划级别）  。

   ![“添加用户”选项的屏幕截图。](common/add-assign-user.png)

1. 在“用户和组”（或“用户”）中，从“用户”列表中选择“B.Simon”，然后选择屏幕底部的“选择”    。
1. 在“添加分配”中，选择“分配” 。

## <a name="configure-new-relic-sso"></a>配置 New Relic SSO

请按照以下步骤在 New Relic 上配置 SSO。

1. [登录](https://login.newrelic.com/) New Relic。

1. 在顶部菜单中，选择“应用”。

1. 在“你的应用”部分中，选择“我的组织” > “身份验证域”  。

1. 选择要 Azure AD SSO 连接到的身份验证域（如果有多个身份验证域）。 大多数公司只有一个称为“默认”的身份验证域。 如果只有一个身份验证域，则无需选择任何内容。

1. 在“身份验证”部分中，选择“配置” 。

   1. 对于“SAML 元数据源”，输入先前从 Azure AD“应用联合元数据 URL”字段保存的值 。

   1. 对于“SSO 目标 URL”，输入先前从 Azure AD“登录 URL”字段中保存的值 。

   1. 验证 Azure AD 和 New Relic 端的设置均没有问题后，选择“保存”。 如果这两端的配置不正确，则用户将无法登录到 New Relic。

### <a name="create-a-new-relic-test-user"></a>创建 New Relic 测试用户

本部分将在 New Relic 中创建名为 B.Simon 的用户。

1. [登录](https://login.newrelic.com/) New Relic。

1. 在顶部菜单中，选择“应用”。

1. 在“你的应用”部分中，选择“用户管理” 。

1. 选择“添加用户”。

   1. 对于“名称”，请输入“B.Simon” 。
   
   1. 对于“电子邮件”，输入将由 Azure AD SSO 发送的值。
   
   1. 为用户选择“类型”和“组” 。 对于测试用户，在“类型”中选择“基本用户”，在“组”中选择“用户”比较合理 。
   
   1. 若要保存用户，请选择“添加用户”。

## <a name="test-sso"></a>测试 SSO 

以下介绍如何使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“New Relic by Organization”时，应会自动登录到 New Relic。 有关访问面板的详细信息，请参阅[在“我的应用”门户中登录和启动应用](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](./tutorial-list.md)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [尝试在 Azure AD 中使用 New Relic](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](/cloud-app-security/proxy-intro-aad)
