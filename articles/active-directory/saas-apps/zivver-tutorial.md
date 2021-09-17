---
title: 教程：Azure Active Directory 与 ZIVVER 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ZIVVER 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: jeedes
ms.openlocfilehash: a22d10cb5d8f8ec7998f1e501239f260fba7328f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771860"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>教程：Azure Active Directory 与 ZIVVER 的集成

本教程介绍了如何将 ZIVVER 与 Azure Active Directory (Azure AD) 进行集成。 将 ZIVVER 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 ZIVVER。
* 让用户使用其 Azure AD 帐户自动登录到 ZIVVER。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 ZIVVER 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 ZIVVER 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* ZIVVER 支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-zivver-from-the-gallery"></a>从库中添加 ZIVVER

若要配置 ZIVVER 与 Azure AD 的集成，需要从库中将 ZIVVER 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“ZIVVER” 。
1. 从结果面板中选择“ZIVVER”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zivver"></a>配置并测试 ZIVVER 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 ZIVVER 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 ZIVVER 中的相关用户之间建立关联。

若要配置并测试 ZIVVER 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 ZIVVER SSO](#configure-zivver-sso) - 在应用程序端配置单一登录设置。
    1. [创建 ZIVVER 测试用户](#create-zivver-test-user) - 在 ZIVVER 中创建 B.Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“ZIVVER”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，执行以下步骤：

    在“标识符”文本框中键入 URL：`https://app.zivver.com/SAML/Zivver`

5. ZIVVER 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 ZIVVER 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。

    ![屏幕截图显示“用户属性”，并且已选择“编辑”图标。](common/edit-attribute.png)

6. 除了上述属性，ZIVVER 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性”  对话框的“用户声明”  部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | 名称 | 命名空间 | 源属性 |
    | ---------------|---------| ---------- |
    | ZivverAccountKey | https:\//zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >如果使用由本地 Active Directory 和 Azure AD Connect 工具组成的混合设置，“值”应设置为 `user.objectGUID`

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![屏幕截图显示“用户声明”以及“添加新声明”选项。](common/new-save-attribute.png)

    ![屏幕截图显示“管理用户声明”对话框，可在其中输入所述的值。](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“ **保存**”。

7. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分中，单击“下载”以下载“联合元数据 XML”，单击“复制”图标以根据要求从给定选项中复制“应用联合元数据 URL”并将其保存在计算机上。

    ![证书 URL 下载链接](./media/zivver-tutorial/metadataxmlurl.png)

8. 在“设置 ZIVVER”部分中，根据要求复制相应 URL。

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

在本部分，你将通过授予 B.Simon 访问 ZIVVER 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“ZIVVER”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-zivver-sso"></a>配置 ZIVVER SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 ZIVVER 公司[站点](https://app.zivver.com/login)。

2. 单击浏览器窗口左下角的“组织设置”图标。

3. 转到“单一登录”。

4. 打开从 Azure 门户下载的联合元数据 XML 文件。

5. 在“标识提供者元数据 URL”文本框中，粘贴之前从 Azure 门户中保存的“应用联合元数据 URL”。

6. 选中“开启 SSO”复选框。

7. 单击“保存”  。

### <a name="create-zivver-test-user"></a>创建 ZIVVER 测试用户

在本部分中，将在 ZIVVER 中创建一个名为 Britta Simon 的用户。 请与 [ZIVVER 支持团队](https://support.zivver.com/)协作来在 ZIVVER 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 ZIVVER。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 ZIVVER 磁贴时，你应该会自动登录到已为其设置了 SSO 的 ZIVVER。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 ZIVVER 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。