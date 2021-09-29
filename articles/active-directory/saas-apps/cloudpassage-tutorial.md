---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 CloudPassage 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 CloudPassage 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: e6954dfc324b6a5145da7c59fc763728a1413944
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124765640"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>教程：Azure Active Directory 单一登录 (SSO) 与 CloudPassage 集成

本教程介绍如何将 CloudPassage 与 Azure Active Directory (Azure AD) 集成。 将 CloudPassage 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 CloudPassage。
* 让用户使用其 Azure AD 帐户自动登录到 CloudPassage。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 CloudPassage 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* CloudPassage 支持 **SP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-cloudpassage-from-the-gallery"></a>从库中添加 CloudPassage

要配置 CloudPassage 与 Azure AD 的集成，需要从库中将 CloudPassage 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“CloudPassage”   。
1. 从结果面板中选择“CloudPassage”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>配置并测试 CloudPassage 的 Azure AD 单一登录

使用名为 B.Simon  的测试用户配置和测试 CloudPassage 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 CloudPassage 相关用户之间建立链接关系。

若要配置和测试 CloudPassage 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 CloudPassage 单一登录](#configure-cloudpassage-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 CloudPassage 测试用户](#create-cloudpassage-test-user)** - 在 CloudPassage 中创建 B. Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“CloudPassage”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

     a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://portal.cloudpassage.com/saml/init/accountid`。

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://portal.cloudpassage.com/saml/consume/accountid`。 可以通过在 CloudPassage 门户的“单一登录设置”部分中单击“SSO 设置文档”来获取此属性的值。  

    ![屏幕截图显示 CloudPassage 门户，其中突出显示了“SSO 设置文档”链接。](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 请联系 [CloudPassage 客户端支持团队](https://www.cloudpassage.com/company/contact/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. CloudPassage 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

1. 除上述属性以外，CloudPassage 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | 姓 |user.surname |
    | 电子邮件 |user.mail |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 CloudPassage”部分，根据要求复制相应的 URL  。

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

在本部分中，你将通过授予 B.Simon 访问 CloudPassage 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“CloudPassage”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-cloudpassage-sso"></a>配置 CloudPassage 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 CloudPassage 公司站点。

1. 在顶部菜单中，单击“设置”，并单击“网站管理”。   
   
    ![屏幕截图显示已选择“网站管理”的 CloudPassage 站点。][12]

1. 单击“身份验证设置”选项卡。  
   
    ![屏幕截图显示已选择“身份验证设置”选项卡的 CloudPassage 站点。][13]

1. 在“单一登录设置”  部分中，执行以下步骤： 
   
    ![屏幕截图显示“单一登录设置”部分，可在其中输入此步骤中的信息。][14]

    a. 选中“启用单一登录 (SSO)(SSO 设置文档)”复选框  。
    
    b. 将“Azure AD 标识符”粘贴到“SAML 颁发者 URL”文本框   。
  
    c. 将“登录 URL”粘贴到“SAML 终结点 URL”文本框   。
  
    d. 将“注销 URL”粘贴到“注销登陆页面”文本框   。
  
    e. 在记事本中打开下载的证书，将下载证书的内容复制到剪贴板，然后将其粘贴到“x 509 证书”文本框  。
  
    f. 单击“ **保存**”。

### <a name="create-cloudpassage-test-user"></a>创建 CloudPassage 测试用户

本部分的目的是在 CloudPassage 中创建名为 B. Simon 的用户。

**若要在 CloudPassage 中创建名为 B. Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 **CloudPassage** 公司站点。 

1. 在顶部工具栏中，单击“设置”，并单击“网站管理”。   
   
    ![屏幕截图显示已选择“网站管理”的 CloudPassage。][22] 

1. 单击“用户”选项卡，并单击“添加新用户”。   
   
    ![屏幕截图显示 CloudPassage“网站管理”，其中选择了“用户”选项卡和“添加新用户”选项。][23]

1. 在“添加新用户”  部分中，执行以下步骤： 
   
    ![屏幕截图显示“添加新用户”部分，可在其中指定用户信息。][24]
    
    a. 在“名字”文本框中，键入“Britta”。  
  
    b. 在“姓氏”  文本框中，键入“Simon”。
  
    c. 在“用户名”文本框、“电子邮件”文本框和“重新键入电子邮件”文本框中，键入 Britta 在 Azure AD 中的用户名。   
  
    d. 对于“访问类型”，选择“启用 Halo 门户访问”。  
  
    e. 单击“添加”  。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 CloudPassage 磁贴时，应当会自动登录到为其设置了 SSO 的 CloudPassage。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [通过 Azure AD 试用 CloudPassage](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png