---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 4me 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 4me 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 98b8b3c1de7879301d82356bbb23e2d2d19ff9cc
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>教程：Azure Active Directory 单一登录 (SSO) 与 4me 集成

本教程介绍如何将 4me 与 Azure Active Directory (Azure AD) 进行集成。 将 4me 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 4me。
* 让用户使用其 Azure AD 帐户自动登录到 4me。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用 4me 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* 4me 支持 SP 发起的 SSO。
* 4me 支持实时用户预配。
* 4me 支持[自动用户预配](4me-provisioning-tutorial.md)。

## <a name="add-4me-from-the-gallery"></a>从库中添加 4me

要配置 4me 与 Azure AD 的集成，需要从库中将 4me 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“4me”。
1. 从结果面板中选择“4me”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-4me"></a>配置和测试 4me 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 4me 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 4me 相关用户之间建立链接关系。

若要配置和测试 4me 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 4me SSO](#configure-4me-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 4me 测试用户](#create-4me-test-user)** - 在 4me 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“4me”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式之一键入 URL：

    | 环境| 代码|
    |---|---|
    | 生产 | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：

    | 环境| 代码|
    |---|---|
    | 生产 | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [4me 客户端支持团队](mailto:support@4me.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 4me 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，4me 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 4me”部分中，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 4me 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“4me”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-4me-sso"></a>配置 4me SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 4me。

1. 在左上角，单击“设置”徽标，然后单击左侧栏中的“单一登录”。

    ![4me 设置](./media/4me-tutorial/settings.png)

1. 在“单一登录”  页上，执行以下步骤：

    ![4me 单一登录](./media/4me-tutorial/single-sign-on.png)

    a. 选择“启用”选项。

    b. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    c. 在 **SAML** 部分的“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    d. 在“证书指纹”文本框中，粘贴从 Azure 门户复制的“指纹”值，其中按双位为一对排序，每对之间用冒号分隔 (AA:BB:CC:DD:EE:FF:GG:HH:II)。

    e. 单击“保存”  。

### <a name="create-4me-test-user"></a>创建 4me 测试用户

本部分将在 4me 中创建一个名为 Britta Simon 的用户。 4me 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 4me 中尚不存在用户，身份验证后会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [4me 支持团队](mailto:support@4me.com)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 4me 登录 URL，并从此处启动登录流。 

* 直接转到 4me 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 4me 磁贴时，会重定向到 4me 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 4me 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。