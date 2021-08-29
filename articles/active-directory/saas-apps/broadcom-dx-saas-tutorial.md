---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Broadcom DX SaaS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Broadcom DX SaaS 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: efa197f7cd538bc82741863717044e7cd4321a37
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744986"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Broadcom DX SaaS 的集成

本教程介绍如何将 Broadcom DX SaaS 与 Azure Active Directory (Azure AD) 相集成。 将 Broadcom DX SaaS 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Broadcom DX SaaS。
* 让用户使用其 Azure AD 帐户自动登录到 Broadcom DX SaaS。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Broadcom DX SaaS 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Broadcom DX SaaS 支持 **IDP** 发起的 SSO。

* Broadcom DX SaaS 支持 **适时** 用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>从库中添加 Broadcom DX SaaS

若要配置 Broadcom DX SaaS 与 Azure AD 的集成，需要从库中将 Broadcom DX SaaS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Broadcom DX SaaS**。
1. 在结果面板中选择“Broadcom DX SaaS”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>配置并测试 Broadcom DX SaaS 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Broadcom DX SaaS 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Broadcom DX SaaS 中的相关用户之间建立链接关系。

若要配置并测试 Broadcom DX SaaS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Broadcom DX SaaS SSO](#configure-broadcom-dx-saas-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Broadcom DX SaaS 测试用户](#create-broadcom-dx-saas-test-user)** - 在 Broadcom DX SaaS 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Broadcom DX SaaS”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    a. 在“标识符”文本框中，使用以下模式键入值：`DXI_<TENANT_NAME>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Broadcom DX SaaS 客户端支持团队](mailto:dxi-na1@saas.broadcom.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Broadcom DX SaaS 应用程序需要特定格式的 SAML 断言，这需要将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Broadcom DX SaaS 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | --------------- | --------- |
    | 组 | user.groups |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Broadcom DX SaaS”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Broadcom DX SaaS 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“Broadcom DX SaaS”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name=&quot;configure-broadcom-dx-saas-sso&quot;></a>配置 Broadcom DX SaaS SSO

1. 以管理员身份登录到 Broadcom DX SaaS 公司站点。

2. 转到“设置”并单击“用户”选项卡。 

    ![用户](./media/broadcom-dx-saas-tutorial/settings.png &quot;用户")

3. 在“用户管理”部分，单击省略号图标并选择“SAML”。 

    ![用户管理](./media/broadcom-dx-saas-tutorial/local.png "用户管理")

4. 在“标识 SAML 帐户”部分执行以下步骤。
   
    ![帐户](./media/broadcom-dx-saas-tutorial/broadcom-1.png "帐户") 

    a. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    b. 在“标识提供者(IDP)登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。 

    c. 在“标识提供者(IDP)注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。 

    d. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“标识提供者证书”文本框中。 

    e. 单击“下一步”。

5. 在“映射属性”部分，填写下一页中所需的 SAML 属性并单击“下一步”。 

    ![属性](./media/broadcom-dx-saas-tutorial/broadcom-2.png "属性") 


6. 在“标识用户组”部分，输入该组的对象 ID 并单击“下一步”。 

    ![添加组](./media/broadcom-dx-saas-tutorial/broadcom-3.png "添加组") 

7. 在“填充 SAML 帐户”部分，验证填充的值并单击“保存”。 

    ![SAML 帐户](./media/broadcom-dx-saas-tutorial/broadcom-4.png "SAML 帐户") 

### <a name="create-broadcom-dx-saas-test-user"></a>创建 Broadcom DX SaaS 测试用户

在本部分，我们将在 Broadcom DX SaaS 中创建名为 Britta Simon 的用户。 Broadcom DX SaaS 支持默认已启用的适时用户预配。 此部分不存在任何操作项。 如果 Broadcom DX SaaS 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”，然后你应会自动登录到为其设置了 SSO 的 Broadcom DX SaaS。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Broadcom DX SaaS”磁贴时，应会自动登录到为其设置了 SSO 的 Broadcom DX SaaS。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Broadcom DX SaaS 后，可以强制实施会话控制，实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。