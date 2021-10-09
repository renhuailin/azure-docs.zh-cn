---
title: 教程：Azure Active Directory 与 BitaBIZ 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 BitaBIZ 之间配置单一登录。
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
ms.openlocfilehash: 085c17859007b3fe8575ca95760bca9aa727a972
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611022"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>教程：Azure Active Directory 与 BitaBIZ 集成

本教程介绍如何将 BitaBIZ 与 Azure Active Directory (Azure AD) 集成。 将 BitaBIZ 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 BitaBIZ。
* 让用户使用其 Azure AD 帐户自动登录到 BitaBIZ。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 BitaBIZ 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 BitaBIZ 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* BitaBIZ 支持 SP 和 IDP 发起的 SSO。
* BitaBIZ 支持[自动用户预配](bitabiz-provisioning-tutorial.md)。


## <a name="add-bitabiz-from-the-gallery"></a>从库中添加 BitaBIZ

若要配置 BitaBIZ 与 Azure AD 的集成，需要将库中的 BitaBIZ 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“BitaBIZ” 。
1. 从结果面板中选择“BitaBIZ”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-bitabiz"></a>配置并测试 BitaBIZ 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 BitaBIZ 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 BitaBIZ 中的相关用户之间建立关联。

若要配置并测试 BitaBIZ 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 BitaBIZ SSO](#configure-bitabiz-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 BitaBIZ 测试用户](#create-bitabiz-test-user)** - 在 BitaBIZ 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“BitaBIZ”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    在“标识符”文本框中，使用以下模式键入 URL：`https://www.bitabiz.com/<INSTANCE_ID>`

    > [!NOTE]
    > 上述 URL 中的值仅供演示。 本教程稍后会介绍如何使用实际标识符更新该值。

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：  `https://www.bitabiz.com/dashboard`

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 BitaBIZ”部分中，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 BitaBIZ 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“BitaBIZ”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-bitabiz-sso"></a>配置 BitaBIZ SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 BitaBIZ 租户。

2. 单击“设置管理员”。 

    ![显示浏览器窗口的一部分的屏幕截图，其中已选中“设置管理员”。](./media/bitabiz-tutorial/setup-admin.png)

3. 单击“添加值”部分下面的“Microsoft 集成”。  

    ![显示“添加值”的屏幕截图，其中已选中“Microsoft 集成”。](./media/bitabiz-tutorial/integrations.png)

4. 向下滚动到“Microsoft Azure AD (启用单一登录)”部分并执行以下步骤： 

    ![显示可在其中输入在此步骤中描述的信息的 Microsoft Azure AD 部分的屏幕截图。](./media/bitabiz-tutorial/configuration.png)

    a. 复制“实体 ID (Azure AD 中的‘标识符’)”文本框中的值，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分中的“标识符”文本框。    

    b. 在“Azure AD 单一登录服务 URL”  文本框中，粘贴从 Azure 门户复制的“登录 URL”  。

    c. 在“Azure AD SAML 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在记事本中打开下载的“证书(Base64)”文件，将其内容复制到剪贴板，然后将其粘贴到“Azure AD 签名证书(Base64 编码)”文本框中   。

    e. 将业务电子邮件域名（即 mycompany.com）添加到“域名”文本框中，以使用此电子邮件域将 SSO 分配给公司中的用户（不是必需的）。 

    f. 为 BitaBIZ 帐户标记“已启用 SSO”。 

    g. 单击“保存 Azure AD 配置”，以保存并激活 SSO 配置。 


### <a name="create-bitabiz-test-user"></a>创建 BitaBIZ 测试用户

为了使 Azure AD 用户登录到 BitaBIZ，必须将其预配到 BitaBIZ。  
就 BitaBIZ 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 BitaBIZ 公司站点。

2. 单击“设置管理员”。 

    ![显示浏览器窗口的一部分的屏幕截图，其中已选中“设置管理员”。](./media/bitabiz-tutorial/setup-admin.png)

3. 单击“组织”部分下面的“添加用户”。  

    ![显示选择了“添加用户”的“组织”部分的屏幕截图。](./media/bitabiz-tutorial/add-user.png)

4. 单击“添加新员工”。 

    ![显示选择了“添加新员工”的“添加用户”的屏幕截图。](./media/bitabiz-tutorial/new-employee.png)

5. 在“添加新员工”  对话框页上，执行以下步骤：

    ![显示可在其中输入在此步骤中描述的信息的页面的屏幕截图。](./media/bitabiz-tutorial/save-employee.png)

    a. 在“名字”  文本框中，键入用户的名字（如“Britta”）。

    b. 在“姓氏”  文本框中，键入用户的姓氏（如“Simon”）。

    c. 在“电子邮件”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“雇用日期”中选择一个日期。 

    e. 还可以针对用户设置其他非必需的用户属性。 有关详细信息，请参阅[员工设置文档](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)。

    f. 单击“保存员工”。 

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

> [!NOTE]
>BitaBIZ 还支持自动用户预配；有关如何配置自动用户预配的更多详细信息，请参阅[此处](./bitabiz-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 BitaBIZ 登录 URL，可从此处启动登录流。  

* 直接转到 BitaBIZ 登录 URL，从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 BitaBIZ。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的 BitaBIZ 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 BitaBIZ。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 BitaBIZ 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
