---
title: 教程：Azure AD SSO 与 Zero Networks 集成
description: 了解如何在 Azure Active Directory 和 Zero Networks 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 533e1dee16a303931987bc83f15f929d89d38b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669331"
---
# <a name="tutorial-azure-ad-sso-integration-with-zero-networks"></a>教程：Azure AD SSO 与 Zero Networks 集成

本教程介绍如何将 Zero Networks 与 Azure Active Directory (Azure AD) 集成。 将 Zero Networks 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zero Networks。
* 让用户可使用其 Azure AD 帐户自动登录到 Zero Networks。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Zero Networks 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

在本教程中，将为 Zero Networks 管理门户和访问门户配置 Azure AD SSO。

* Zero Networks 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-zero-networks-from-the-gallery"></a>从库中添加 Zero Networks

若要配置 Zero Networks 与 Azure AD 的集成，需要从库中将 Zero Networks 添加到托管 SaaS 应用列表。

1. 使用 Microsoft 工作帐户登录 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Zero Networks” 。
1. 从结果面板中选择“Zero Networks”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Civic Platform”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，执行以下步骤。

    a. 在“登录 URL”文本框中，键入 URL：`https://portal.zeronetworks.com/#/login`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Zero Networks”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

## <a name="configure-zero-networks-sso&quot;></a>配置 Zero Networks SSO

1. 以管理员身份登录到 Zero Networks 管理门户。

1. 导航到“设置” > “标识提供者” 。

1. 单击“Microsoft Azure”，然后执行以下步骤。
    
    ![屏幕截图显示 SSO 配置的设置。](./media/zero-networks-tutorial/settings.png &quot;帐户")

    1. 复制“标识符（实体 ID）”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中  。

    1. 复制“回复 URL (断言使用者服务 URL)”值，并将此值粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    1. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    1. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    1. 在记事本中打开从 Azure 门户下载的“证书 (Base64)”，将内容粘贴到“证书 (Base64)”文本框中 。

    1. 单击“保存”。

## <a name="configure-user-assignment-requirement"></a>配置用户分配要求

1. 在 Azure 门户中的“Zero Networks”应用程序集成页上，找到“管理”部分并选择“属性”  。
1. 将“需要进行用户分配?”更改为“否” 。

![“需要进行用户分配”的屏幕截图。](./media/zero-networks-tutorial/user-assignment.png)

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Zero Networks 登录 URL，从中可启动登录流。 

* 直接转到 Zero Networks 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Zero Networks 磁贴时，将会重定向到 Zero Networks 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。
