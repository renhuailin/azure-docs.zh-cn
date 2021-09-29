---
title: 教程：Azure Active Directory SAML 2.0 与 PolicyStat 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 PolicyStat 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 578606e26ef66e9b03e57b539d04b21a9d38cc07
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124790009"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>教程：Azure Active Directory 与 PolicyStat 的集成

本教程介绍如何将 PolicyStat 与 Azure Active Directory (Azure AD) 集成。
将 PolicyStat 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 PolicyStat。
* 可让用户使用其 Azure AD 帐户自动登录到 PolicyStat（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 PolicyStat 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 PolicyStat 单一登录的订阅

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* PolicyStat 支持 **SP** 发起的 SSO

* PolicyStat 支持 **实时** 用户预配

## <a name="adding-policystat-from-the-gallery"></a>从库中添加 PolicyStat

若要配置 PolicyStat 与 Azure AD 的集成，需要从库中将 PolicyStat 添加到托管 SaaS 应用列表。

若要从库中添加 PolicyStat，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **PolicyStat**，在结果面板中选择“PolicyStat”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的“PolicyStat”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 PolicyStat 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 PolicyStat 相关用户之间建立链接关系。

若要配置和测试 PolicyStat 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 PolicyStat 单一登录](#configure-policystat-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
4. **[创建 PolicyStat 测试用户](#create-policystat-test-user)** - 在 PolicyStat 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 PolicyStat 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/) 中的“PolicyStat”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在 **选择单一登录方法** 对话框中，选择 **SAML/WS-Fed** 模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![PolicyStat 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.policystat.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [PolicyStat 客户端支持团队](https://rldatix.com/services-support/support)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

5. PolicyStat 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。

    ![显示“用户属性”对话框的屏幕截图，其中选择了“编辑”图标。](common/edit-attribute.png)

6. 除上述属性以外，PolicyStat 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | 名称 | 源属性 |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。
    
    ![显示“用户声明”部分的屏幕截图，其中突出显示了“添加新声明”和“保存”操作。](common/new-save-attribute.png)

    ![显示“管理用户声明”对话框的屏幕截图，其中突出显示了“名称”、“转换”和“参数”文本框，并选择了“保存”按钮。](./media/policystat-tutorial/attribute01.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“转换”作为“源”。

    e. 在“转换”列表中，键入该行显示的属性值。
    
    f. 在“参数 1”列表中，键入该行显示的属性值。

    g. 单击“ **保存**”。

7. 在“设置 PolicyStat”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-policystat-single-sign-on&quot;></a>配置 PolicyStat 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 PolicyStat 公司站点。

2. 单击“管理员”选项卡，并单击左侧导航窗格中的“单一登录配置”。
   
    ![“管理员”菜单](./media/policystat-tutorial/ic808633.png &quot;“管理员”菜单")

3. 单击“IDP 元数据”，并在“IDP 元数据”部分执行以下步骤：
   
    ![显示选择了“IDP 元数据”操作的屏幕截图。](./media/policystat-tutorial/ic808636.png "单一登录配置")
   
    a. 打开下载的元数据文件，复制其内容，然后将其粘贴到“标识提供者元数据”文本框中。

    b. 单击 **“保存更改”** 。

4. 单击“配置属性”，并在“配置属性”部分执行以下步骤：
   
    a. 在“用户名属性”文本框中，键入“uid”。

    b. 在“名字属性”文本框中，键入 Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** 中的名字属性声明名称。

    c. 在“姓氏属性”文本框中，键入 Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** 中的姓氏属性声明名称。

    d. 在“电子邮件属性”文本框中，键入 Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** 中的电子邮件属性声明名称。

    e. 单击 **“保存更改”** 。

5. 在“设置”部分，选择“启用单一登录集成”。
   
    ![单一登录配置](./media/policystat-tutorial/ic808634.png "单一登录配置")


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过为你自己的帐户授予访问 PolicyStat 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“PolicyStat”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“PolicyStat”.

    ![“应用程序”列表中的“PolicyStat”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框的“用户”列表中选择你的帐户，然后单击屏幕底部的“选择”按钮 。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-policystat-test-user"></a>创建 PolicyStat 测试用户

在本部分，我们将在 PolicyStat 中创建名为 Britta Simon 的用户。 PolicyStat 支持默认已启用的恰时用户预配。 此部分不存在任何操作项。 如果 PolicyStat 中尚不存在用户，身份验证后会创建一个新用户。

>[!NOTE]
>可使用任何其他 PolicyStat 用户帐户创建工具或 PolicyStat 提供的 API 来预配 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“PolicyStat”磁贴时，应会自动登录到设置了 SSO 的 PolicyStat。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)