---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Check Point Harmony Connect 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Check Point Harmony Connect 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: 18a919827ec213f5165d8b7d913f0c489d63ee5c
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891835"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-harmony-connect"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Check Point Harmony Connect 集成

本教程介绍如何将 Check Point Harmony Connect 与 Azure Active Directory (Azure AD) 集成。 将 Check Point Harmony Connect 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁可访问 Check Point Harmony Connect。
* 让用户使用 Azure AD 帐户自动登录 Check Point Harmony Connect。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Check Point Harmony Connect 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Check Point Harmony Connect 支持 SP 发起的 SSO。
> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。


## <a name="adding-check-point-harmony-connect-from-the-gallery"></a>从库中添加 Check Point Harmony Connect

若要配置 Check Point Harmony Connect 与 Azure AD 的集成，需将 Check Point Harmony Connect 从库中添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Check Point Harmony Connect” 。
1. 从结果面板中选择“Check Point Harmony Connect”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-check-point-harmony-connect"></a>配置并测试 Check Point Harmony Connect 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Check Point Harmony Connect 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Check Point Harmony Connect 相关用户之间建立关联。

若要配置并测试 Check Point Harmony Connect 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Check Point Harmony Connect SSO](#configure-check-point-harmony-connect-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Check Point Harmony Connect 测试用户](#create-check-point-harmony-connect-test-user)** - 在 Check Point Harmony Connect 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Check Point Harmony Connect 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    在“登录 URL”  文本框中，键入 URL：`https://cloudinfra-gw.portal.checkpoint.com/api/saml/sso`

1. Check Point Harmony Connect 应用程序需要特定格式的 SAML 断言，因此需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Check Point Harmony Connect 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | ---------------- | --------- |
    | groups | user.groups |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Check Point Harmony Connect”部分，根据需要复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 对 Check Point Harmony Connect 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Check Point Harmony Connect”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-check-point-harmony-connect-sso"></a>配置 Check Point Harmony Connect SSO

1. 以管理员身份登录 Check Point Harmony Connect 网站。

1. 单击“设置”，然后转到“标识提供者”，单击“立即连接”  。

    ![标识提供者的屏幕截图。](./media/check-point-harmony-connect-tutorial/identity-provider.png)

1. 选择 Microsoft Azure AD 作为标识提供者，然后单击“下一步” 。

    ![选择标识提供者的屏幕截图。](./media/check-point-harmony-connect-tutorial/select-identity-provider.png)

1. 在“验证域”页上，输入组织域，并将生成的该 DNS 记录作为 TXT 记录输入到 DNS 服务器，然后单击“下一步” 。

    ![域值的屏幕截图。](./media/check-point-harmony-connect-tutorial/domain.png)

1. 在“允许连接”页中，执行以下步骤：

    ![“允许连接”页的屏幕截图。](./media/check-point-harmony-connect-tutorial/allow-connectivity.png)

    a. 复制“实体 ID”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中  。

    b. 复制“回复URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中  。

    c. 单击“下一步”。

1. 在“配置元数据”页上，上传从 Azure 门户下载的联合元数据 XML 。

1. 在“确认标识提供者”页上，单击“添加”以完成配置 。

### <a name="create-check-point-harmony-connect-test-user"></a>创建 Check Point Harmony Connect 测试用户

1. 以管理员身份登录 Check Point Harmony Connect 网站。

1. 转到“策略” -> “访问控制”，创建一个新规则，然后单击“(+)”来添加新用户    。 

    ![创建新用户的屏幕截图。](./media/check-point-harmony-connect-tutorial/add-new-user.png)

1. 在“添加用户”窗口中，在相应的文本框中输入名称和用户名，然后单击“添加” 。

    ![创建用户的屏幕截图。](./media/check-point-harmony-connect-tutorial/add-user.png)

## <a name="test-sso"></a>测试 SSO 

若要测试 Check Point Harmony Connect，请转到其身份验证服务，并使用在“创建 Azure AD 测试用户”部分中创建的测试帐户进行身份验证。

## <a name="next-steps"></a>后续步骤

配置 Check Point Harmony Connect 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。