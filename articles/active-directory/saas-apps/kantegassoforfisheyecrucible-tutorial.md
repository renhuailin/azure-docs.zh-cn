---
title: 教程：Azure AD SSO 与 Kantega SSO for FishEye/Crucible 集成
description: 了解如何在 Azure Active Directory 和 Kantega SSO for FishEye/Crucible 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/05/2021
ms.author: jeedes
ms.openlocfilehash: 09a46bed2aff2e25bf702415a5a5cdc1167cc9fa
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857169"
---
# <a name="tutorial-azure-ad-sso-integration-with-kantega-sso-for-fisheyecrucible"></a>教程：Azure AD SSO 与 Kantega SSO for FishEye/Crucible 集成

本教程介绍如何将 Kantega SSO for FishEye/Crucible 与 Azure Active Directory (Azure AD) 集成。 将 Kantega SSO for FishEye/Crucible 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Kantega SSO for FishEye/Crucible。
* 让用户使用其 Azure AD 帐户自动登录到 Kantega SSO for FishEye/Crucible。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Kantega SSO for FishEye/Crucible 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Kantega SSO for FishEye/Crucible 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Kantega SSO for FishEye/Crucible 支持启用了 SP 和 IDP 的 SSO。

## <a name="add-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>从库中添加 Kantega SSO for FishEye/Crucible

若要配置 Kantega SSO for FishEye/Crucible 的集成（集成到 Azure AD 中），需从库中将 Kantega SSO for FishEye/Crucible 添加到托管 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Kantega SSO for FishEye/Crucible” 。
1. 从结果面板中选择“Kantega SSO for FishEye/Crucible”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-fisheyecrucible"></a>配置并测试 Kantega SSO for FishEye/Crucible 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Kantega SSO for FishEye/Crucible 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Kantega SSO for FishEye/Crucible 中的相关用户之间建立关联。

若要配置 Kantega SSO for FishEye/Crucible 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Kantega SSO for FishEye/Crucible SSO](#configure-kantega-sso-for-fisheyecrucible-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Kantega SSO for FishEye/Crucible 测试用户](#create-kantega-sso-for-fisheyecrucible-test-user) - 在 Kantega SSO for FishEye/Crucible 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Kantega SSO for FishEye/Crucible”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 在配置 FishEye/Crucible 插件的过程中，将接收这些值，这将在教程的后面部分进行说明。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Kantega SSO for FishEye/Crucible”部分中，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Kantega SSO for FishEye/Crucible 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Kantega SSO for FishEye/Crucible”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-kantega-sso-for-fisheyecrucible-sso"></a>配置 Kantega SSO for FishEye/Crucible SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 FishEye/Crucible 本地服务器。

1. 将鼠标悬停在小齿轮上，并单击“外接程序”  。

    ![屏幕截图显示选择了“加载项”的齿轮图标。](./media/kantegassoforfisheyecrucible-tutorial/admin.png)

1. 在“系统设置”部分，单击“查找新外接程序”  。 

    ![屏幕截图显示选择了“查找新加载项”的“系统设置”部分。](./media/kantegassoforfisheyecrucible-tutorial/settings.png)

1. 搜索“Kantega SSO for Crucible”  ，然后单击“安装”  按钮安装新的 SAML 插件。

    ![屏幕截图显示在搜索框中显示了“Kantega SSO for Crucible”并选择了“安装”按钮的“Attlasian Marketplace for FishEye”页。](./media/kantegassoforfisheyecrucible-tutorial/install.png)

1. 插件安装随即开始。 

    ![屏幕截图显示插件的“正在安装”对话框。](./media/kantegassoforfisheyecrucible-tutorial/plugin.png)

1. 安装完成后。 单击“关闭”  。

    ![屏幕截图显示“已安装并准备就绪”对话框，其中已选择“关闭”按钮。](./media/kantegassoforfisheyecrucible-tutorial/installation.png)

1.  单击“管理”。 

    ![屏幕截图显示“Kantega SSO for Crucible SAML & Kerberos”应用页，其中已选择“管理”按钮。](./media/kantegassoforfisheyecrucible-tutorial/integration.png)

1. 单击“配置”  配置新的插件。 

    ![屏幕截图显示“用户安装的加载项”页，其中已选择“配置”按钮。](./media/kantegassoforfisheyecrucible-tutorial/user.png)

1. 在“SAML”部分中  。 从“添加标识提供者”下拉菜单选择“Azure Active Directory (Azure AD)”   。

    ![屏幕截图显示带有“添加标识提供者”下拉列表的“加载项”-“Kantega 单一登录”页面，其中已选择“Azure Active Directory (Azure AD)”。 ](./media/kantegassoforfisheyecrucible-tutorial/azure.png)

1. 选择订阅级别为“基本”  。

    ![屏幕截图显示选择了“基本”的“准备 Azure AD”部分。](./media/kantegassoforfisheyecrucible-tutorial/subscription.png)

1. 在“应用属性”部分，执行以下步骤  ：

    ![屏幕截图显示选择了“App ID URI”文本框和“复制”按钮的“应用属性”部分。](./media/kantegassoforfisheyecrucible-tutorial/properties.png)

    a. 复制“应用 ID URI”值并将其用作 Azure 门户中“基本 SAML 配置”部分中的“标识符、回复 URL 和登录 URL”。

    b. 单击“下一步”。 

1. 在“元数据导入”部分，执行以下步骤：

    ![屏幕截图显示选择了“电脑上的元数据文件”的“元数据导入”部分。](./media/kantegassoforfisheyecrucible-tutorial/metadata.png)

    a. 选择“我的计算机上的元数据文件”，上传从 Azure 门户下载的元数据文件。

    b. 单击“下一步”。 

1. 在“名称和 SSO 位置”部分，执行以下步骤：

    ![屏幕截图显示突出显示了“标识提供者名称”文本框并选择了“下一步”按钮的“名称和 SSO 位置”。](./media/kantegassoforfisheyecrucible-tutorial/location.png)

    a. 在“标识提供者名称”文本框中添加标识提供者名称（例如 Azure AD）。

    b. 单击“下一步”。 

1. 验证签名证书，然后单击“下一步”  。   

    ![屏幕截图显示“签名验证”部分信息，其中已选择“下一步”按钮。](./media/kantegassoforfisheyecrucible-tutorial/certificate.png)

1. 在“FishEye 用户帐户”  部分，执行以下步骤：

    ![屏幕截图显示选择了“根据需要在 FishEye 的内部目录中创建用户”选项和“下一步”按钮的“FishEye 用户帐户”部分。](./media/kantegassoforfisheyecrucible-tutorial/accounts.png)

    a. 选择“根据需要在 FishEye 的内部目录中创建用户”  ，并输入用户的组的合适名称（可以为多个 组，用逗号隔开）。

    b. 单击“下一步”。

1. 单击“完成”  。

    ![屏幕截图显示已选择“完成”按钮的“摘要”部分。](./media/kantegassoforfisheyecrucible-tutorial/summary.png)

1. 在“Azure AD 的已知域”部分，执行以下步骤  ：  

    ![屏幕截图显示选择了“保存”按钮的“Azure AD 已知域”部分。](./media/kantegassoforfisheyecrucible-tutorial/domain.png)

    a. 从页的左侧面板中选择“已知域”。

    b. 在“已知域”文本框中输入域名。

    c. 单击“保存”  。

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>创建 Kantega SSO for FishEye/Crucible 测试用户

要使 Azure AD 用户能够登录 FishEye/Crucible，必须将这些用户预配到 FishEye/Crucible 中。 在 Kantega SSO for FishEye/Crucible 中，需手动完成预配任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Crucible 本地服务器。

1. 将鼠标悬停在小齿轮上，然后单击“用户”  。

    ![屏幕截图显示已选择齿轮图标，并从下拉列表中选择了“用户”。](./media/kantegassoforfisheyecrucible-tutorial/projects.png)

1. 在“用户”  选项卡部分，单击“添加用户”  。

    ![显示已选择“添加用户”按钮的“用户”部分的屏幕截图。](./media/kantegassoforfisheyecrucible-tutorial/add-user.png)

1. 在“添加新用户”  对话框页上，执行以下步骤：

    ![添加员工](./media/kantegassoforfisheyecrucible-tutorial/new-user.png)

    a. 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    b. 在“显示名称”  文本框中，键入用户的显示名称（如 Britta Simon）。

    c. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“密码”文本框中，键入用户的密码。 

    e. 在“确认密码”文本框中，再次输入用户密码  。

    f. 单击“添加”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Kantega SSO for FishEye/Crucible 登录 URL，可以从那里启动登录流。  

* 直接转到 Kantega SSO for FishEye/Crucible 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Kantega SSO for FishEye/Crucible。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的 Kantega SSO for FishEye/Crucible 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Kantega SSO for FishEye/Crucible。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Kantega SSO for FishEye/Crucible 后，即可强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。