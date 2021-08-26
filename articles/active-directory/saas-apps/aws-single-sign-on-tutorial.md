---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 AWS Single Sign-on 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 AWS Single Sign-on 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 91a66240941ce18b4d898327abeac695b09d65e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732344"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>教程：Azure Active Directory 单一登录 (SSO) 与 AWS Single Sign-on 的集成

本教程介绍如何将 AWS Single Sign-on 与 Azure Active Directory (Azure AD) 相集成。 将 AWS Single Sign-on 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AWS Single Sign-on。
* 让用户使用其 Azure AD 帐户自动登录到 AWS Single Sign-on。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 AWS Single Sign-on 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* AWS Single Sign-on支持 **SP 和 IDP** 发起的 SSO。

* AWS Single Sign-on 支持 [**自动用户预配**](./aws-single-sign-on-provisioning-tutorial.md)。

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>从库中添加 AWS Single Sign-on

若要配置 AWS Single Sign-on 与 Azure AD 的集成，需要从库中将 AWS Single Sign-on 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **AWS Single Sign-on**。
1. 在结果面板中选择“AWS Single Sign-on”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>配置并测试 AWS Single Sign-on 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 AWS Single Sign-on 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 AWS Single Sign-on 中的相关用户之间建立链接关系。

若要配置并测试 AWS Single Sign-on 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 AWS Single Sign-on SSO](#configure-aws-single-sign-on-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 AWS Single Sign-on 测试用户](#create-aws-single-sign-on-test-user)** - 在 AWS Single Sign-on 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“AWS Single Sign-on”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果你有 **服务提供商元数据文件**，请在“基本 SAML 配置”部分执行以下步骤：

    a. 单击“上传元数据文件”  。

    b. 单击 **文件夹徽标**，从 **配置 AWS 单一登录 SSO** 部分（点 8）中选择已下载的元数据文件，并单击 **添加**。

    ![image2](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”  和“回复 URL”  值会自动填充在“基本 SAML 配置”部分：

    ![image3](common/idp-intiated.png)

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请根据需求手动填充这些值。

1. 如果你没有 **服务提供商元数据文件**，请在“基本 SAML 配置”部分执行以下步骤；如果你想要在“IDP”发起的模式下配置应用程序，请输入以下字段的值： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [AWS Single Sign-on 客户端支持团队](mailto:aws-sso-partners@amazon.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. AWS Single Sign-on 应用程序需要特定格式的 SAML 断言，这需要将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > 如果在 AWS SSO 中启用了 ABAC，则可将其他属性作为会话标记直接传入 AWS 帐户。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上。   

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 AWS Single Sign-on”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 AWS Single Sign-on 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“AWS Single Sign-on”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-aws-single-sign-on-sso"></a>配置 AWS Single Sign-on SSO

1. 若要在 AWS Single Sign-on 中自动执行配置，需要通过单击 **安装扩展** 来安装 **我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击 **设置 AWS Single Sign-on**，此时会将您定向到 AWS Single Sign-on 应用程序。 在此处提供管理员凭据以登录到 AWS Single Sign-on。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-10。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 AWS Single Sign-on，请在另一个 Web 浏览器窗口中，以管理员身份登录到 AWS Single Sign-on 公司站点。

1. 请参阅 **服务 -> 安全性、标识和符合性 -> AWS Single Sign-On**。
2. 在左侧导航窗格中选择“设置”。
3. 在 **设置** 页上找到 **标识源**，然后单击 **更改**。

    ![标识源更改服务的屏幕截图](./media/aws-single-sign-on-tutorial/settings.png)

4. 在“更改标识源”上选择 **外部标识提供者**。

    
    ![选择外部标识提供者部分的屏幕截图](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. 在 **配置外部标识提供者** 部分中执行以下步骤：

    ![下载和上传元数据部分的屏幕截图](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. 在 **服务提供商元数据** 部分，找到 **AWS SSO SAML 元数据**，然后选择 **下载元数据文件** 以下载元数据文件并将其保存在计算机上，然后使用此元数据文件上传至 Azure 门户上。

    b. 复制 **AWS SSO Sign-in URL** 值，并将此值粘贴到 Azure 门户上 **“基本 SAML 配置”部分** 的 **登录 URL** 文本框中。

    c. 在“标识提供者元数据”部分，选择“浏览”以上传从 Azure 门户下载的元数据文件。 

    d. 选择“下一步: 复查”。

8. 在文本框中，键入 **ACCEPT** 更改标识源。

    ![确认配置的屏幕截图](./media/aws-single-sign-on-tutorial/accept.png)

9. 单击 **更改标识源**。

### <a name="create-aws-single-sign-on-test-user"></a>创建 AWS Single Sign-on 测试用户

1. 打开 **AWS SSO 控制台**。

2. 在左侧导航窗格中选择“用户”。

3. 在“用户”页上选择“添加用户”。

4. 在“添加用户”页上执行以下步骤：

    a. 在“用户名”字段中输入 B.Simon。

    b. 在“电子邮件地址”字段中输入 `username@companydomain.extension`。 例如，`B.Simon@contoso.com` 。

    c. 在“确认电子邮件地址”字段中，重新输入上一步骤中所输入的电子邮件地址。

    d. 在“名字”字段中输入 `Jane`。

    e. 在“姓氏”字段中输入 `Doe`。

    f. 在“显示名称”字段中输入 `Jane Doe`。

    g. 选择“下一步: 组”。

    > [!NOTE]
    > 确保在 AWS SSO 中输入的用户名与该用户的 Azure AD 登录名相匹配。 这有助于避免出现任何身份验证问题。

5. 选择“添加用户”。
6. 接下来将用户分配到 AWS 帐户。 为此，请在 AWS SSO 控制台的左侧导航窗格中，选择“AWS 帐户”。
7. 在“AWS 帐户”页上选择“AWS 组织”选项卡，选中要分配给该用户的 AWS 帐户旁边的复选框。 然后选择“分配用户”。
8. 在“分配用户”页上，找到并选中用户 B.Simon 旁边的复选框。 然后选择“下一步: 权限集”。
9. 在“选择权限集”部分下，选中要分配给用户 B.Simon 的权限集旁边的框。 如果当前没有权限集，请选择“创建新权限集”。

    > [!NOTE]
    > 权限集定义用户和组对 AWS 帐户拥有的访问级别。 若要详细了解权限集，请查看 AWS SSO 的“权限集”页。
10. 选择“完成”。

> [!NOTE]
> AWS Single Sign-on 还支持自动用户预配，在[此处](./aws-single-sign-on-provisioning-tutorial.md)可以找到有关如何配置自动用户预配的更多详细信息。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 AWS Single Sign-on 登录 URL，可在其中启动登录流。  

* 直接转到 AWS Single Sign-on 登录 URL，并从中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，然后你应会自动登录到为其设置了 SSO 的 AWS Single Sign-on。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“AWS Single Sign-on”磁贴时，如果该应用程序是在 SP 模式下配置的，则你会重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 AWS Single Sign-on。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 AWS Single Sign-on 后，可以强制实施会话控制，实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
