---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Cisco Webex 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Cisco Webex 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: jeedes
ms.openlocfilehash: 2b5a4900c29b294342a6d11a946d487649b64c5e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Cisco Webex 集成

本教程介绍如何将 Cisco Webex 与 Azure Active Directory (Azure AD) 集成。 将 Cisco Webex 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Cisco Webex。
* 让用户使用其 Azure AD 帐户自动登录到 Cisco Webex。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Cisco Webex 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Cisco Webex 支持 SP 发起的 SSO  。
* Cisco Webex 支持 [**自动用户预配**](./cisco-webex-provisioning-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex

若要配置 Cisco Webex 与 Azure AD 的集成，需要从库中将 Cisco Webex 添加到托管 SaaS 应用的列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Cisco Webex”   。
1. 从结果面板中选择“Cisco Webex”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>配置并测试 Cisco Webex 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Cisco Webex 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Cisco Webex 相关用户之间建立链接关系。

若要配置并测试 Cisco Webex 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
2. [配置 Cisco Webex SSO](#configure-cisco-webex-sso)，以在应用程序端配置 SSO 设置。
    1. **[创建 Cisco Webex 测试用户](#create-cisco-webex-test-user)** ，以在 Cisco Webex 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Cisco Webex”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，通过执行以下步骤上传已下载的 **服务提供程序元数据** 文件并配置应用程序：

    >[!Note]
    >你将从“配置 Cisco Webex”  部分获取服务提供商元数据文件，本教程稍后将对此进行说明。 

    a. 单击“上传元数据文件”  。

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    c. 成功完成服务提供程序元数据文件的上传后，**标识符** 和 **回复 URL** 值将自动填充在“基本 SAML 配置”部分： 

    d. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://web.ciscospark.com/idb/Consumer/metaAlias/<ID>/sp`
    
    > [!NOTE]
    > 此值不是真实值。 复制横向“回复 URL”值，并将该值添加到 `https://web.ciscospark.com/` 来表示实际的登录 URL 值。

1. Cisco Webex 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Cisco Webex 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
  
    | 名称 |  源属性|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  源属性值默认将映射到 userpricipalname。 可以根据 Webex 中的设置，将此值更改为 user.mail、user.onpremiseuserprincipalname 或任何其他值。


1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

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

在本部分中，通过授予 B. Simon 访问 Cisco Webex 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Cisco Webex”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cisco-webex-sso"></a>配置 Cisco Webex SSO

1. 使用管理员凭据登录到 Cisco Webex。

1. 选择“组织设置”，然后在“身份验证”部分下单击“修改”。  

    ![屏幕截图显示可在其中选择“修改”的“身份验证设置”。](./media/cisco-spark-tutorial/organization-settings.png)
  
1. 选择“集成第三方标识提供者。(高级)”，然后单击“下一步”。

    ![显示“集成第三方标识提供者”的屏幕截图。](./media/cisco-spark-tutorial/enterprise-settings.png)

1. 单击“下载元数据文件”以下载“服务提供商元数据文件”并将其保存在计算机中，然后单击“下一步”。  

    ![显示“服务提供商元数据文件”的屏幕截图。](./media/cisco-spark-tutorial/sp-metadata.png)

1. 单击“文件浏览器”选项，找到并上传 Azure AD 元数据文件。 然后，选择“元数据中需要由证书颁发机构签名的证书(更安全)”并单击“下一步”。  

    ![屏幕截图显示“导入 Idp 元数据”页。](./media/cisco-spark-tutorial/idp-metadata.png)

1. 选择“测试 SSO 连接”，当新的浏览器标签页打开时，通过登录使用 Azure AD 进行身份验证。 

1. 返回到 **Cisco Cloud Collaboration Management** 浏览器标签页。如果测试成功，则选择“此测试成功。  启用单一登录”，并单击“下一步”。 

1. 单击“保存”  。

> [!NOTE]
> 若要详细了解如何配置 Cisco Webex，请参阅[此页](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog)。

### <a name="create-cisco-webex-test-user"></a>创建 Cisco Webex 测试用户

在本部分，我们将在 Cisco Webex 中创建名为 B.Simon 的用户。此应用程序支持自动用户预配，可让你根据业务规则启用自动预配和取消预配。  Microsoft 建议尽可能地使用自动预配。 了解如何为 [Cisco Webex](./cisco-webex-provisioning-tutorial.md) 启用自动预配。

如果需要手动创建用户，请执行以下步骤：

1. 使用管理员凭据登录到 Cisco Webex。

2. 单击“用户”，然后单击“管理用户”   。
   
    ![屏幕截图显示可在其中“管理用户”的“用户”页。](./media/cisco-spark-tutorial/user-1.png) 

3. 在“管理用户”窗口中，选择“手动添加或修改用户”。 

    ![显示“用户”页的屏幕截图，在此页中可以管理用户和选择“手动添加或修改用户”。](./media/cisco-spark-tutorial/user-2.png)

4. 选择“姓名和电子邮件地址”。  然后，如下所述填写文本框：

    ![屏幕截图显示“管理用户”对话框，可在其中手动添加或修改用户。](./media/cisco-spark-tutorial/user-3.png) 

    a. 在“名字”  文本框中，键入用户的名字，例如 **B**。

    b. 在“姓氏”  文本框中，键入用户的姓氏，例如 **Simon**。

    c. 在“电子邮件地址”文本框中，键入用户的电子邮件地址，例如 b.simon@contoso.com。 

5. 单击加号以添加 B.Simon。 然后单击“下一步”。 

6. 在“为用户添加服务”窗口中，依次单击“添加用户”、“完成”。  

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Cisco Webex 登录 URL，可从中启动登录流。 

* 直接转到 Cisco Webex 登录 URL，并从中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Cisco Webex”磁贴时，会自动重定向到 Cisco Webex 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 Cisco Webex 后，可以强制实施会话控制，实时防止组织的敏感数据遭到外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。