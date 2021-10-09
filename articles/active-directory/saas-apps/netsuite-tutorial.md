---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 NetSuite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 NetSuite 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: a38d2ced173e74263cdf159a1f3243e00ce0911b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776537"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>教程：将 Azure AD 单一登录 (SSO) 与 NetSuite 集成

本教程介绍如何将 NetSuite 与 Azure Active Directory (Azure AD) 集成。 将 NetSuite 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 NetSuite。
* 让用户使用其 Azure AD 帐户自动登录到 NetSuite。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 NetSuite 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 

NetSuite 支持：

* IDP 发起的 SSO。
* JIT（实时）用户预配。
* NetSuite 支持[自动用户预配](netsuite-provisioning-tutorial.md)。

> [!NOTE]
> 由于此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-netsuite-from-the-gallery"></a>从库中添加 NetSuite

若要配置 NetSuite 与 Azure AD 的集成，请执行以下操作，从库中将 NetSuite 添加到托管 SaaS 应用列表：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左窗格中，选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“NetSuite”   。
1. 在结果窗格中选择“NetSuite”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-netsuite"></a>配置并测试 NetSuite 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 NetSuite 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 NetSuite 相关用户之间建立链接关系。

若要配置并测试 NetSuite 的 Azure AD SSO，请执行以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    * [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 用户测试 Azure AD 单一登录。  
    * [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使用户 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 NetSuite SSO](#configure-netsuite-sso)，在应用程序端配置单一登录设置。
    * [创建 NetSuite 测试用户](#create-the-netsuite-test-user)，以在 NetSuite 中创建用户 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

若要在 Azure 门户中启用 Azure AD SSO，请执行以下操作：

1. 在 Azure 门户的“NetSuite”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”窗格中选择“SAML”。  
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”旁边的“编辑”（铅笔）图标。   

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分的“回复 URL”文本框中，键入以下 URL： `https://system.netsuite.com/saml2/acs`

1. NetSuite 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，NetSuite 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性 |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > 帐户属性的值不是真实值。 需要根据本教程稍后所述更新此值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 NetSuite”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   

1. 选择屏幕顶部的“新建用户”  。

1. 在“用户”属性窗格中执行以下步骤  ：

   a. 在“名称”框中，输入 **B.Simon**。  
   b. 在“用户名”框中，输入 username@companydomain.extension（例如 B.Simon@contoso.com）  。  
   c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。    
   d. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予用户 B.Simon 访问 NetSuite 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“NetSuite”  。
1. 在概述窗格中找到“管理”部分，然后选择“用户和组”链接。  
1. 选择“添加用户”，然后在“添加分配”窗格中选择“用户和组”    。
1. 在“用户和组”窗格中的“用户”下拉列表内选择“B.Simon”，然后选择屏幕底部的“选择”按钮。    
1. 如果在 SAML 断言中需要任何角色值，请执行以下操作：

   a. 在“选择角色”窗格中的下拉列表内，为用户选择适当的角色。   
   b. 在屏幕底部选择“选择”按钮。 
1. 在“添加分配”窗格中选择“分配”按钮。  

## <a name="configure-netsuite-sso"></a>配置 NetSuite SSO

1. 在浏览器中打开新标签页，并以管理员身分登录 NetSuite 公司站点。

2. 在顶部导航栏中选择“设置”，然后选择“公司” > “启用功能”。   

    ![屏幕截图显示从“公司”选择了“启用功能”。](./media/NetSuite-tutorial/ns-setupsaml.png)

3. 在页面中间的工具栏中，选择“SuiteCloud”。 

    ![屏幕截图显示选择了“SuiteCloud”。](./media/NetSuite-tutorial/ns-suitecloud.png)

4. 在“管理身份验证”  下，选中“SAML 单一登录”  复选框以在 NetSuite 中启用“SAML 单一登录”选项。

    ![屏幕截图显示了“管理身份验证”，你可以在其中选择“SAML 单一登录”。](./media/NetSuite-tutorial/ns-ticksaml.png)

5. 在顶部导航栏中选择“设置”  。

    ![屏幕截图显示从“NETSUITE”导航栏中选择了“安装”。](./media/NetSuite-tutorial/ns-setup.png)

6. 在“设置任务”列表中选择“集成”。  

    ![屏幕截图显示从“安装任务”选择了“集成”。](./media/NetSuite-tutorial/ns-integration.png)

7. 在“管理身份验证”下，选择“SAML 单一登录”。  

    ![屏幕截图显示从“安装任务”中的“集成”项选择了“SAML 单一登录”。](./media/NetSuite-tutorial/ns-saml.png)

8. 在“SAML 设置”窗格中的“NetSuite 配置”下，执行以下操作：  

    ![屏幕截图显示了“SAML 安装”，你可以在其中输入所述值。](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. 选中“主要身份验证方法”复选框。 

    b. 在“SAMLV2 标识提供者元数据”下选择“上传 IDP 元数据文件”，然后选择“浏览”上传从 Azure 门户下载的元数据文件。   

    c. 选择“提交”。 

9. 在 NetSuite 顶部导航栏中选择“设置”，然后选择“公司” > “公司信息”。   

    ![屏幕截图显示从“公司”选择了“公司信息”。](./media/NetSuite-tutorial/ns-com.png)

    ![屏幕截图显示了可以在其中输入所述值的窗格。](./media/NetSuite-tutorial/ns-account-id.png)

    b. 在“公司信息”窗格中的右侧列中，复制“帐户 ID”值。  

    c. 将从 NetSuite 帐户中复制的“帐户 ID”粘贴到 Azure AD 中的“属性值”框内。  

    ![显示添加帐户 ID 值的屏幕截图](./media/netsuite-tutorial/attribute-value.png)

10. 在用户能够单一登录到 NetSuite 之前，必须先在 NetSuite 中为他们分配适当的权限。 若要分配这些权限，请执行以下操作：

    a. 在顶部导航栏中选择“设置”  。

    ![屏幕截图显示从“NETSUITE”导航栏中选择了“安装”。](./media/NetSuite-tutorial/ns-setup.png)

    b. 在左窗格中选择“用户/角色”，然后选择“管理角色”。  

    ![屏幕截图显示了“管理角色”窗格，你可以在其中选择“新角色”。](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. 选择“新建角色”。 

    d. 输入新角色的 **名称**。

    ![屏幕截图显示“安装管理员”，你可以在其中输入角色名称。](./media/NetSuite-tutorial/ns-new-role.png)

    e. 选择“保存”。 

    f. 在顶部导航栏中选择“权限”  。 然后选择“设置”  。

    ![屏幕截图显示了“安装”选项卡，你可以在其中输入所述值。](./media/NetSuite-tutorial/ns-sso.png)

    g. 依次选择“SAML 单一登录”、“添加”   。

    h. 选择“保存”。 

    i. 在顶部导航栏中选择“设置”，然后选择“设置管理器”。  

    ![屏幕截图显示从“NETSUITE”导航栏中选择了“安装”。](./media/NetSuite-tutorial/ns-setup.png)

    j. 在左窗格中选择“用户/角色”，然后选择“管理用户”。  

    ![屏幕截图显示了“管理用户”窗格，你可以在其中选择“套件演示团队”。](./media/NetSuite-tutorial/ns-manage-users.png)

    k. 选择一个测试用户，选择“编辑”，然后选择“访问”选项卡。  

    ![屏幕截图显示了“管理用户”窗格，你可以在其中选择“编辑”。](./media/NetSuite-tutorial/ns-edit-user.png)

    l. 在“角色”窗格中，指定已创建的相应角色。 

    ![屏幕截图显示从“员工”选择了“管理员”。](./media/NetSuite-tutorial/ns-add-role.png)

    m. 选择“保存”。 

### <a name="create-the-netsuite-test-user"></a>创建 NetSuite 测试用户

本部分将在 NetSuite 中创建一个名为 B.Simon 的用户。 NetSuite 支持在默认情况下保持启用的实时用户预配。 此部分不存在任何操作项。 如果 NetSuite 中不存在用户，则会在身份验证后创建一个新用户。

NetSuite 还支持自动用户预配；有关如何配置自动用户预配的更多详细信息，请参阅[此处](./netsuite-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

- 在 Azure 门户中单击“测试此应用程序”后，你应当会自动登录到为其设置了 SSO 的 NetSuite

- 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 NetSuite 磁贴时，你应当会自动登录到为其设置了 SSO 的 NetSuite。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 NetSuite 后，即可强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)
