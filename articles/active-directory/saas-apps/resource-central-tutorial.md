---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Resource Central - 会议室预订系统的 SAML SSO 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Resource Central - 会议室预订系统的 SAML SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 0db8c8d14dbd3e09ebb798c41ea2d7b526acabc9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central--saml-sso-for-meeting-room-booking-system"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Resource Central - 会议室预订系统的 SAML SSO 的集成

本教程介绍如何将 Resource Central - 会议室预订系统的 SAML SSO 与 Azure Active Directory (Azure AD) 集成。 将 Resource Central - 会议室预订系统的 SAML SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Resource Central - 会议室预订系统的 SAML SSO。
* 支持用户使用其 Azure AD 帐户自动登录到 Resource Central - 会议室预订系统的 SAML SSO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* Resource Central - 会议室预订系统的 SAML SSO 已启用单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Resource Central - 会议室预订系统的 SAML SSO 支持 SP 发起的 SSO

* Resource Central - 会议室预订系统的 SAML SSO 支持实时用户预配

## <a name="add-resource-central--saml-sso-for-meeting-room-booking-system-from-the-gallery"></a>从库中添加 Resource Central - 会议室预订系统的 SAML SSO

若要配置 Resource Central - 会议室预订系统的 SAML SSO 与 Azure AD 的集成，需要从库中将 Resource Central - 会议室预订系统的 SAML SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，输入“Resource Central - 会议室预订系统的 SAML SSO” 。
1. 从结果面板中选择“Resource Central - 会议室预订系统的 SAML SSO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-resource-central--saml-sso-for-meeting-room-booking-system"></a>配置并测试 Resource Central - 会议室预订系统的 SAML SSO 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Resource Central - 会议室预订系统的 SAML SSO 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Resource Central - 会议室预订系统的 SAML SSO 相关用户之间建立关联。

若要配置并测试 Resource Central - 会议室预订系统的 SAML SSO 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
    1. **[创建 Resource Central 会议室预订系统的 SAML SSO 测试用户](#create-resource-central-saml-sso-for-meeting-room-booking-system-test-user)** - 在 Resource Central 会议室预订系统的 SAML SSO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[配置 Resource Central 会议室预订系统的 SAML SSO SSO](#configure-resource-central-saml-sso-for-meeting-room-booking-system-sso)** - 在应用程序端配置单一登录设置。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Resource Central - 会议室预订系统的 SAML SSO”应用程序集成页，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”中，输入以下字段的值：

   1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<DOMAIN_NAME>/ResourceCentral` 

   1. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<DOMAIN_NAME>/ResourceCentral`

   1. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > 这些值不是文本值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Resource Central - 会议室预订系统的 SAML SSO 客户端支持团队](mailto:st@aod.vn)获取这些值。  还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Resource Central - 会议室预订系统的 SAML SSO”中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 `username@companydomain.extension`。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Resource Central - 会议室预订系统的 SAML SSO 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Resource Central - 会议室预订系统的 SAML SSO”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”窗格中选择“用户和组”  。
1. 在“用户和组”窗格中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮   。
1. 如果你希望将某角色分配给用户，可以在“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”窗格中，单击“分配”按钮 。

### <a name="create-resource-central-saml-sso-for-meeting-room-booking-system-test-user"></a>创建 Resource Central 会议室预订系统的 SAML SSO 测试用户

本部分将在 Resource Central - 会议室预订系统的 SAML SSO 中创建一个名为 B.Simon 的用户 。

1. 在 Resource Central - 会议室预订系统的 SAML SSO 中，选择“安全性” > “人员” > “新建”  。
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="显示“Resource Central”中的“人员”窗格的屏幕截图，其中突出显示了“新建”按钮。":::

1. 在“人员详细信息”中，对于“显示名称”，请输入用户“B.Simon”  。 对于“SMTP 地址”，请输入用户的 Azure AD 用户名。 例如，`B.Simon@contoso.com`。

    :::image type="content" source="./media/resource-central/person.png" alt-text="显示 Resource Central 中“人员详细信息”窗格的屏幕截图。":::

## <a name="configure-resource-central-saml-sso-for-meeting-room-booking-system-sso"></a>配置 Resource Central 会议室预订系统的 SAML SSO SSO

本部分将在 Resource Central 系统管理员中配置单一登录。

1. 在“Resource Central - 会议室预订系统的 SAML SSO 系统管理员”中，选择“外部身份验证”。
1.  对于“Enable Configuration”，选择“是” 。

    ![显示在 Resource Central - 会议室预订系统的 SAML SSO 的“外部身份验证”窗格中选择的“启用配置”选项的屏幕截图。](./media/resource-central/enable.png)

1. 在“身份验证协议”中，选择“SAML2” 。 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="显示在 Resource Central 中为身份验证协议选择 SAML2 的屏幕截图。":::

1. 在“SAML2 配置”下，输入以下字段的值：

    1. 对于“标识符(实体 ID)”、“登录 URL”、“注销 URL”和“Azure AD 标识符”，请输入相关 URL   ：

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Resource Central 中“SAML2”配置窗格的屏幕截图。":::

        从“设置 Resource Central - 会议室预订系统的 SAML SSO”窗格复制 URL：

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Resource Central 中的“设置 Resource Central”窗格的屏幕截图。":::

   1. 对于“返回 URL”，输入 `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler`。
  
1. 对于“证书”，请上传证书，然后输入密码。

   ![Resource Central - 会议室预订系统的 SAML SSO 中证书部分的屏幕截图。](./media/resource-central/cert.png)
   
1. 选择“保存”。

1. 返回到 **Azure 门户**。 在“SAML 签名证书”中，上传证书并输入密码。

   ![Azure 门户中“上传证书”窗格的屏幕截图。](./media/resource-central/cert2.png).

1. 选择 **添加** 。

## <a name="test-sso"></a>测试 SSO 

在本部分中，测试 Azure AD 单一登录配置。 若要测试单一登录，有三个选项：

* 在 Azure 门户中，选择“测试此应用程序”。 该链接重定向到 Resource Central - 会议室预订系统的 SAML SSO 登录 URL，你可以在其中启动登录。

* 直接转到 Resource Central - 会议室预订系统的 SAML SSO 登录 URL，并启动登录。

   :::image type="content" source="./media/resource-central/test.png" alt-text="单一登录测试网页的屏幕截图。":::

* 使用 Microsoft 的“我的应用”门户。 在“我的应用”门户中，选择“Resource Central - 会议室预订系统的 SAML SSO”磁贴以重定向到 Resource Central - 会议室预订系统的 SAML SSO 登录 URL。 有关详细信息，请参阅[从“我的应用”门户登录和启动应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

设置 Resource Central - 会议室预订系统的 SAML SSO 后，即可强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。