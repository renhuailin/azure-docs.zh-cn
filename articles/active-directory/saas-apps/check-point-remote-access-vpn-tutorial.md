---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Check Point Remote Secure Access VPN 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Check Point Remote Secure Access VPN 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: ef52547bb8ec27bd759a238d742173f6b8fc3994
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-remote-secure-access-vpn"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Check Point Remote Secure Access VPN 的集成

本教程介绍如何将 Check Point Remote Secure Access VPN 与 Azure Active Directory (Azure AD) 相集成。 将 Check Point Remote Secure Access VPN 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Check Point Remote Secure Access VPN。
* 让用户使用其 Azure AD 帐户自动登录到 Check Point Remote Secure Access VPN。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Check Point Remote Secure Access VPN 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Check Point Remote Secure Access VPN 支持 SP 发起的 SSO。

## <a name="adding-check-point-remote-secure-access-vpn-from-the-gallery"></a>从库中添加 Check Point Remote Secure Access VPN

若要配置 Check Point Remote Secure Access VPN 与 Azure AD 的集成，需要从库中将 Check Point Remote Secure Access VPN 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Check Point Remote Secure Access VPN 。
1. 在结果面板中选择“Check Point Remote Secure Access VPN”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-check-point-remote-secure-access-vpn"></a>配置并测试 Check Point Remote Secure Access VPN 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Check Point Remote Secure Access VPN 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Check Point Remote Secure Access VPN 中的相关用户之间建立链接关系。

若要配置并测试 Check Point Remote Secure Access VPN 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Check Point Remote Secure Access VPN SSO](#configure-check-point-remote-secure-access-vpn-sso) - 使用户能够使用此功能。

    1. [创建 Check Point Remote Secure Access VPN 测试用户](#create-check-point-remote-secure-access-vpn-test-user) - 在 Check Point Remote Secure Access VPN 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Check Point Remote Secure Access VPN”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<GATEWAY_IP>/saml-vpn/` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符、回复 URL 和登录 URL 更新这些值。 请联系 [Check Point Remote Secure Access VPN 客户端支持团队](mailto:support@checkpoint.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Check Point Remote Secure Access VPN”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Check Point Remote Secure Access VPN 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Check Point Remote Secure Access VPN”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-check-point-remote-secure-access-vpn-sso"></a>配置 Check Point Remote Secure Access VPN SSO

### <a name="configure-an-external-user-profile-object"></a>配置外部用户配置文件对象

> [!NOTE]
> 仅当你不想要使用本地 Active Directory (LDAP) 时，才需要阅读本部分。

在旧版 SmartDashboard 中配置通用用户配置文件：

1. 在 SmartConsole 中，转到“管理和设置”>“刀片服务器”。

1. 在“移动访问”部分，单击“在 SmartDashboard 中配置” 。 此时会打开旧版 SmartDashboard。

1. 在“网络对象”窗格中，单击“用户” 。

1. 右键单击空白区域并选择“新建”>“外部用户配置文件”>“匹配所有用户”。

1. 配置“外部用户配置文件”属性：

    1. 在“常规属性”页上：
        * 在“外部用户配置文件”名称字段中，保留默认名称 `generic`*
        * 在“到期日期”字段中，设置适用的日期

    1. 在“身份验证”页上：
        * 在“身份验证方案”下拉列表中，选择 `undefined`
    1. 在“位置”、“时间”和“加密”页上  ：
        * 配置其他适用的设置
    1. 单击“确定”。

1. 在顶部工具栏中，单击“更新”（或按 Ctrl + S）。

1. 关闭 SmartDashboard。

1. 在 SmartConsole 中，安装访问控制策略。

### <a name="configure-remote-access-vpn"></a>配置 Remote Access VPN

1. 打开适用安全网关的对象。

1. 在“常规属性”页上，启用“IPSec VPN”软件刀片服务器。

1. 在左侧树中，单击“IPSec VPN”页。

1. 在“此安全网关参与以下 VPN 社区”部分，单击“添加”并选择“远程访问社区”  。

1. 在左侧树中，单击“VPN 客户端”>“远程访问”。

1. 启用“支持访客模式”。

1. 在左侧树中，单击“VPN 客户端”>“办公模式”。

1. 选择“允许办公模式”，然后选择适用的办公模式方法。

1. 在左侧树中，单击“VPN 客户端”>“SAML 门户设置”。

1. 确保“主 URL”包含网关的完全限定域名。
此域名应以组织注册的 DNS 后缀结尾。
例如：`https://gateway1.company.com/saml-vpn`

1. 确保证书受最终用户浏览器的信任。

1. 单击“确定”。


### <a name="configure-an-identity-provider-object"></a>配置标识提供者对象

1. 针对参与 Remote Access VPN 的每个安全网关执行以下步骤。

1. 在“SmartConsole”>“网关和服务器”视图中，单击“新建”>“更多”>“用户/标识”>“标识提供者” 。

    ![新“标识提供者”的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/identity-provider.png)

1. 在“新建标识提供者”窗口中执行以下步骤。

    ![“标识提供者”部分的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/new-identity-provider.png)

    a. 在“网关”字段中，选择需要执行 SAML 身份验证的安全网关。

    b. 在“服务”字段中，从下拉列表中选择“Remote Access VPN” 。

    c. 复制“标识符（实体 ID）”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中  。

    d. 复制“回复URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中  。

    e. 选择“导入元数据文件”，上传从 Azure 门户下载的“联合元数据 XML” 。

    > [!NOTE]
    > 此外，还可以选择“手动插入”，手动将“实体 ID”和“登录 URL”值粘贴到相应的字段中，并上传从 Azure 门户获取的证书文件   。

    f. 单击“确定”。

### <a name="configure-the-identity-provider-as-an-authentication-method"></a>将标识提供者配置为身份验证方法

1. 打开适用安全网关的对象。

1. 在“VPN 客户端”>“身份验证”页上：

    a. 清除复选框“允许旧客户端连接到此网关”。

    b. 添加新对象或编辑现有领域。

    ![“添加新对象”的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/add-new-object.png)

1. 输入名称和显示名称，并添加/编辑身份验证方法：如果要在参与 MEP 的网关上使用“登录选项”，为确保用户体验顺畅，“名称”应以“SAMLVPN_”前缀开头。 

    ![有关“登录选项”的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/login-option.png)

1. 选择选项“标识提供者”，单击绿色的 `+` 按钮，然后选择适用的标识提供者对象。

    ![选择适用标识提供者对象的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/green-button.png)

1. 在“多个登录选项”窗口中：在左侧窗格中单击“用户目录”，然后选择“手动配置” 。
有两个选项：
    1. 如果你不想要使用本地 Active Directory (LDAP)，请仅选择“外部用户配置文件”并单击“确定”。
    2. 如果你想要使用本地 Active Directory (LDAP)，请仅选择“LDAP 用户”，并在“LDAP 查找类型”中选择“电子邮件”。 然后，单击“确定”。

    ![手动配置的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/manual-configuration.png)

1. 在管理数据库中配置所需的设置：

    1.  关闭 SmartConsole。

    2.  将 GuiDBEdit 工具连接到管理服务器（参阅 [sk13009](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails&solutionid=sk13009)）。

    3.  在左上侧窗格中，转到“编辑”>“网络对象”。

    4.  在右上侧窗格中，选择“安全网关对象”。

    5.  在底部窗格中，转到“realms_for_blades”>“vpn”。

    6.  如果你不想要使用本地 Active Directory (LDAP)，请将“do_ldap_fetch”设置为“false”，并将“do_generic_fetch”设置为“true”   。 然后单击“确定”。 如果你想要使用本地 Active Directory (LDAP)，请将“do_ldap_fetch”设置为“true”，并将“do_generic_fetch”设置为“false”   。 然后单击“确定”。

    7.  针对所有适用的安全网关重复步骤 iv 至 vi。

    8.  保存所有更改（单击“文件”菜单 >“全部保存”） 。

1. 关闭 GuiDBEdit 工具。

1. 每个安全网关和每个软件刀片服务器都有独立的设置。 查看使用身份验证的每个安全网关和每个软件刀片服务器中的设置（“VPN”、“移动访问”和“标识感知”）。

    * 确保仅为使用 LDAP 的软件刀片服务器选择“LDAP 用户”选项。

    * 确保仅为不使用 LDAP 的软件刀片服务器选择“外部用户配置文件”选项。

1. 在每个安全网关上安装访问控制策略。

### <a name="vpn-ra-client-installation-and-configuration"></a>VPN RA 客户端安装和配置

1. 安装 VPN 客户端。

1. 设置标识提供者浏览器模式（可选）。默认情况下，Windows 客户端使用其嵌入式浏览器，而 macOS 客户端使用 Safari 在标识提供者的门户上进行身份验证。
要使 Windows 客户端改变此行为以改用 Internet Explorer，请执行以下操作：

   1. 在客户端计算机上，以管理员身份打开一个纯文本编辑器。

   2. 在文本编辑器中打开 `trac.defaults` 文件。

      - 在 32 位 Windows 上：

        `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

      - 在 64 位 Windows 上：

        `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

    3. 将 `idp_browser_mode` 属性的值从 `embedded` 更改为 `IE`。

    4. 保存该文件。

    5. 重启 Check Point Endpoint Security VPN 客户端服务。

   以管理员身份打开 Windows 命令提示符并运行以下命令：

   `# net stop TracSrvWrapper`

   `# net start TracSrvWrapper`

1. 使用后台运行的浏览器开始身份验证：

   1. 在客户端计算机上，以管理员身份打开一个纯文本编辑器。

   2. 在文本编辑器中打开 `trac.defaults` 文件。

      - 在 32 位 Windows 上：

        `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

      - 在 64 位 Windows 上：

        `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

      - 在 macOS 上：
      
        `/Library/Application Support/Checkpoint/Endpoint Security/Endpoint Connect/trac.defaults`

    3. 将 `idp_show_browser_primary_auth_flow` 的值更改为 `false`。

    4. 保存该文件。

    5. 重启 Check Point Endpoint Security VPN 客户端服务。
       - 在 Windows 客户端上，以管理员身份打开 Windows 命令提示符并运行以下命令：

         `# net stop TracSrvWrapper`
        
         `# net start TracSrvWrapper`

       - 在 macOS 客户端上，运行：

         `sudo launchctl stop com.checkpoint.epc.service`

         `sudo launchctl start com.checkpoint.epc.service`

### <a name="create-check-point-remote-secure-access-vpn-test-user"></a>创建 Check Point Remote Secure Access VPN 测试用户

在本部分，你将在 Check Point Remote Secure Access VPN 中创建名为 Britta Simon 的用户。 与 [Check Point Remote Secure Access VPN 支持团队](mailto:support@checkpoint.com)协作，在 Check Point Remote Secure Access VPN 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

1. 打开 VPN 客户端，然后单击“连接到...”。

    ![“连接到”的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/connect.png)

1. 从下拉列表中选择“站点”，然后单击“连接” 。

    ![选择站点的屏幕截图。](./media/check-point-remote-access-vpn-tutorial/site.png)

1. 在 Azure AD 登录弹出窗口中，使用在“创建 Azure AD 测试用户”部分创建的 Azure AD 凭据进行登录。

## <a name="next-steps"></a>后续步骤

配置 Check Point Remote Secure Access VPN 后，可以强制实施会话控制，从而实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
