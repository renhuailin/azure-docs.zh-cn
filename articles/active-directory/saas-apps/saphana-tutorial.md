---
title: 教程：Azure Active Directory 与 SAP HANA 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP HANA 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: dac7582f772beaf62a60661869f829018770538b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751632"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>教程：将 Azure Active Directory 与 SAP HANA 集成

本教程介绍如何将 SAP HANA 与 Azure Active Directory (Azure AD) 集成。 将 SAP HANA 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 SAP HANA。
* 让用户使用其 Azure AD 帐户自动登录到 SAP HANA。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP HANA 的集成，需要以下项：

- Azure AD 订阅
- 已启用单一登录 (SSO) 的 SAP HANA 订阅
- 一个在任何公共 IaaS、本地、Azure VM 或 Azure 中的 SAP 大型实例上运行的 HANA 实例
- XSA 管理 Web 接口以及安装在 HANA 实例上的 HANA Studio

> [!NOTE]
> 我们不建议使用 SAP HANA 的生产环境测试本教程中的步骤。 首先在应用程序的开发环境或过渡环境中测试集成，然后使用生产环境。

若要测试本教程中的步骤，请遵循以下建议：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 SAP HANA 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP HANA 支持 IDP 发起的 SSO。
* SAP HANA 支持实时用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。


## <a name="adding-sap-hana-from-the-gallery"></a>从库中添加 SAP HANA

若要配置 SAP HANA 与 Azure AD 的集成，需要从库中将 SAP HANA 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SAP HANA” 。
1. 从结果面板中选择“SAP HANA”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>配置并测试 SAP HANA 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 SAP HANA 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 SAP HANA 中的相关用户之间建立链接关系。

若要配置并测试 SAP HANA 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 SAP HANA SSO](#configure-sap-hana-sso)** - 在应用程序端配置单一登录设置。
    1. **创建 SAP HANA 测试用户 [ - 在 SAP HANA 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式](#create-sap-hana-test-user)** 。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 SAP HANA 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` 

    > [!NOTE]
    > 答复 URL 值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [SAP HANA 客户支持团队](https://cloudplatform.sap.com/contact.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. SAP HANA 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![显示“用户属性”部分的屏幕截图，其中已选择“编辑”图标。](common/edit-attribute.png)

6. 在“用户属性和声明”对话框中的“用户属性”部分，执行以下步骤：
 
    a. 单击“编辑图标”，打开“管理用户声明”对话框   。

    ![显示“用户属性和声明”对话框的屏幕截图，其中选择了“编辑”图标。](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. 从“转换”列表中，选择“ExtractMailPrefix()”   。

    c. 从“参数 1”列表中，选择“user.mail”   。

    d. 单击“保存”  。

7. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

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

在本部分，你将通过授予 B.Simon 访问 SAP HANA 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“SAP HANA”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-sap-hana-sso"></a>配置 SAP HANA SSO

1. 若要在 SAP HANA 端配置单一登录，请通过转到相应的 HTTPS 终结点登录到“HANA XSA Web 控制台”。 

    > [!NOTE]
    > 在默认配置中，URL 将请求重定向到登录屏幕，这需要经过身份验证的 SAP HANA 数据库用户的凭据。 登录的用户必须有权执行 SAML 管理任务。

2. 在 XSA Web 界面中，转到“SAML 标识提供者”。  选择屏幕底部的 **+** 按钮显示“添加标识提供者信息”窗格。  然后执行以下步骤：

    ![添加标识提供者](./media/saphana-tutorial/sap1.png)

    a. 在“添加标识提供者信息”  窗格中，将从 Azure 门户下载的元数据 XML 内容粘贴到“元数据”  框中。

    ![屏幕截图显示了“添加标识提供者信息”窗格，其中突出显示了“元数据”和“名称”框。](./media/saphana-tutorial/sap2.png)

    b. 如果 XML 文档的内容有效，则分析过程会提取“常规数据”屏幕区域中的“使用者、实体 ID 和颁发者”字段所需的信息。   此外，还会提取“目标”屏幕区域中的 URL 字段（例如，“基 URL”和“单一登录 URL (*)”   字段）所需的信息。

    ![添加标识提供者设置](./media/saphana-tutorial/sap3.png)

    c. 在“常规数据”屏幕区域的“名称”框中，输入新 SAML SSO 标识提供者的名称。  

    > [!NOTE]
    > SAML IDP 的名称是必需的，且必须唯一。 将 SAML 选作 SAP HANA XS 应用程序要使用的身份验证方法时，该名称会显示在可用的 SAML IDP 列表中。 例如，可以在 XS 项目管理工具的“身份验证”屏幕区域中执行此操作。 

3. 选择“保存”  保存 SAML 标识提供者的详细信息并将新 SAML IDP 添加到已知 SAML IDP 列表。

    ![“保存”按钮](./media/saphana-tutorial/sap4.png)

4. 在 HANA Studio 中“配置”选项卡的系统属性内，根据 **saml** 筛选设置。 然后将 **assertion_timeout** 从 **10 秒** 调整为 **120 秒**。

    ![assertion_timeout 设置](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>创建 SAP HANA 测试用户

若要使 Azure AD 用户能够登录到 SAP HANA，必须在 SAP HANA 中预配这些用户。
SAP HANA 支持默认启用的“实时预配”  。

如果需要手动创建用户，请执行以下步骤：

>[!NOTE]
>可更改用户使用的外部身份验证。 他们可以使用 Kerberos 等外部系统进行身份验证。 有关外部标识的详细信息，请联系[域管理员](https://cloudplatform.sap.com/contact.html)。

1. 以管理员身份打开 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)，并为 DB-User 启用 SAML SSO。

    ![创建用户](./media/saphana-tutorial/sap5.png)

2. 选中“SAML”  左侧隐藏的复选框，并选择“配置”链接。 

3. 选择“添加”以添加 SAML IDP。   选择相应的 SAML IDP，再选择“确定”。 

4. 添加“外部标识”（本例中为 BrittaSimon）。 然后选择“确定”。

   > [!Note]
   > 必须填充用户的“外部标识”字段，且该字段必须与 Azure AD 的 SAML 令牌中的 NameID 字段匹配 。 不应选中任何复选框，因为此选项要求 IDP 将 SPProvderID 属性发送到目前不受 Azure AD 支持的 NameID 字段中。 有关更多详细信息，请参阅[此文档](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/db6db355bb571014b56eb25057daec5f.html)。

5. 针对测试，请将所有“XS”  角色分配给该用户。

    ![分配角色](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > 请只授予适合用例的权限。

6. 保存用户。

### <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，应该会自动登录到为其设置了 SSO 的 SAP HANA

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 SAP HANA 磁贴时，应会自动登录到为其设置了 SSO 的 SAP HANA。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 SAP HANA 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。