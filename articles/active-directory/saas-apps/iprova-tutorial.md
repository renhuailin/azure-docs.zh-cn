---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Zenya 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zenya 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 946aa1a6a177dd89c851e977ac783806e8a9dcc5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652218"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zenya"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Zenya 集成

本教程介绍如何将 Zenya 与 Azure Active Directory (Azure AD) 集成。 将 Zenya 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zenya。
* 让用户使用其 Azure AD 帐户自动登录到 Zenya。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Zenya 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Zenya 支持 SP 发起的 SSO。
* Zenya 支持[自动用户预配](iprova-provisioning-tutorial.md)。

## <a name="add-zenya-from-the-gallery"></a>从库中添加 Zenya

若要配置 Zenya 与 Azure AD 的集成，需要从库中将 Zenya 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Zenya” 。
1. 从结果面板中选择“Zenya”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zenya"></a>配置并测试 Zenya 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Zenya 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Zenya 中的相关用户之间建立关联。

若要配置并测试 Zenya 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Zenya SSO](#configure-zenya-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Zenya 测试用户](#create-zenya-test-user) - 在 Zenya 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="retrieve-configuration-information-from-zenya"></a>从 Zenya 检索配置信息

在本部分，你将从 Zenya 检索信息以配置 Azure AD 单一登录。

1. 打开 Web 浏览器，并使用以下 URL 模式转到 Zenya 中的“SAML2 信息”页：
    
     `https://<SUBDOMAIN>.zenya.work/saml2info`   
     `https://<SUBDOMAIN>.iprova.nl/saml2info`  
     `https://<SUBDOMAIN>.iprova.be/saml2info`  
     `https://<SUBDOMAIN>.iprova.eu/saml2info` 

    ![查看 Zenya 的“SAML2 信息”页](media/iprova-tutorial/information.png)

1. 在另一个浏览器选项卡中继续执行后续步骤时，保持浏览器选项卡处于打开状态。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Zenya 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”框中填写“Zenya SAML2 信息”页上的标签“登录 URL”后面显示的值。   此页仍在另一浏览器标签页中打开。

    b. 在“标识符”框中填写“Zenya SAML2 信息”页上的标签“EntityID”后面显示的值。   此页仍在另一浏览器标签页中打开。

    c. 在“回复 URL”框中填写“Zenya SAML2 信息”页上的标签“回复 URL”后面显示的值。   此页仍在另一浏览器标签页中打开。

1. Zenya 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Zenya 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性| 命名空间  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

## <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Zenya 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Zenya”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-zenya-sso"></a>配置 Zenya SSO

1. 使用管理员帐户登录到 Zenya。

2. 打开“转到”菜单。

3. 选择“应用程序管理”。

4. 在“系统设置”面板中选择“常规”。

5. 选择“编辑”。

6. 向下滚动到“访问控制”。

    ![Zenya 访问控制设置](media/iprova-tutorial/access-control.png)

7. 查找设置“用户使用其网络帐户自动登录”，并将其更改为“是，通过 SAML 进行身份验证”。 此时会显示其他选项。

8. 选择“设置”。

9. 选择“**下一页**”。

10. Zenya 会询问是要从 URL 下载联合数据，还是从文件上传联合数据。 选择“从 URL”选项。

    ![下载 Azure AD 元数据](media/iprova-tutorial/metadata.png)

11. 粘贴在“配置 Azure AD 单一登录”部分的最后一步中保存的元数据 URL。

12. 选择箭头按钮，从 Azure AD 下载元数据。

13. 下载完成后，将显示确认消息“已下载有效的联合数据文件”。

14. 选择“**下一页**”。

15. 暂时跳过“测试登录”选项，并选择“下一步”。

16. 在“声明使用”下拉框中，选择“windowsaccountname”。

17. 选择“完成”。

18. 现在返回“编辑常规设置”屏幕。 向下滚动到页面底部，选择“确定”以保存配置。

## <a name="create-zenya-test-user"></a>创建 Zenya 测试用户

1. 使用管理员帐户登录到 Zenya。

2. 打开“转到”菜单。

3. 选择“应用程序管理”。

4. 选择“用户和用户组”面板中的“用户”。

5. 选择 **添加** 。

6. 在“用户名”文本框中输入用户的用户名（如 `B.Simon@contoso.com`）。

7. 在“全名”框中输入完整名称，例如 **B.Simon**。

8. 选择“无密码(使用单一登录)”选项。

9. 在“电子邮件地址”框中，输入用户的电子邮件地址（例如 `B.Simon@contoso.com`）。

10. 向下滚动到页面底部，选择“完成”。

> [!NOTE]
> Zenya 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./iprova-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Zenya 登录 URL，你可以从那里启动登录流。 

* 直接转到 Zenya 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Zenya 磁贴时，会重定向到 Zenya 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Zenya 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。