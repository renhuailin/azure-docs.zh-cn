---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 NetDocuments 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 NetDocuments 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 7e6fc17510186ebae3ec736f2385baf39c22eda0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802106"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>教程：Azure Active Directory 单一登录 (SSO) 与 NetDocuments 集成

在本教程中，你将了解如何将 NetDocuments 与 Azure Active Directory (Azure AD) 集成。 将 NetDocuments 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 NetDocuments。
* 让用户使用其 Azure AD 帐户自动登录到 NetDocuments。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 NetDocuments 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* NetDocuments 支持 SP 发起的 SSO 

## <a name="adding-netdocuments-from-the-gallery"></a>从库中添加 NetDocuments

若要配置 NetDocuments 与 Azure AD 的集成，需要从库中将 NetDocuments 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“NetDocuments”   。
1. 从结果面板中选择“NetDocuments”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>配置并测试 NetDocuments 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 NetDocuments 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 NetDocuments 相关用户之间建立链接关系。

若要配置和测试 NetDocuments 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 NetDocuments SSO](#configure-netdocuments-sso)  - 在应用程序端配置单一登录设置。
    1. [创建 NetDocuments 测试用户](#create-netdocuments-test-user) - 在 NetDocuments 中有一个与 Azure AD 中的 B.Simon 相对应的关联用户  。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 NetDocuments 应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，键入以下 URL 模式之一：

    |登录 URL|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. 在“标识符(实体 ID)”文本框中，键入以下 URL 之一：

    |标识符|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. 在“回复 URL”文本框中，键入以下 URL 模式之一：

    |回复 URL|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > 这些不是实际值。 使用实际的登录 URL 和回复 URL 更新这些值。 存储库 ID 是一个值，它以 **CA-** 开头，后跟与 NetDocuments 存储库关联的由 8 个字符组成的代码。 有关详细信息，可以检查 [NetDocuments 联合身份支持文档](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login)。 或者，如果在使用上述信息进行配置时遇到困难，可以联系 [NetDocuments 客户端支持团队](https://support.netdocuments.com/hc/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. NetDocuments 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 NetDocuments 应用程序要求通过 ObjectID 或任何其他适用于 nameidentifier 组织的声明对 nameidentifier 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射   。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页面的“SAML 签名证书”部分，查找“应用联合元数据 URL”，然后复制 URL  。

    ![证书下载链接](common/copy-metadataurl.png)

1. 在“设置 NetDocuments”部分中，根据要求复制相应 URL  。

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

在本部分中，将通过授予 B.Simon 访问 NetDocuments 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“NetDocuments”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name=&quot;configure-netdocuments-sso&quot;></a>配置 NetDocuments 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 NetDocuments 公司站点。

2. 在右上角，选择你的姓名>“管理员”  。

3. 选择“安全中心”  。
   
    ![存储库](./media/netdocuments-tutorial/security-center.png &quot;安全中心")

4. 选择“高级身份验证”  。
    
    ![配置高级身份验证选项](./media/netdocuments-tutorial/advance-authentication.png "配置高级身份验证选项")

5.  在“联合 ID”  选项卡上，执行以下步骤：   
   
    [ ![联合身份](./media/netdocuments-tutorial/federated-id.png "联合身份")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. 对于“联合身份模式服务器类型”，请选择“Windows Azure Active Directory” 。
    
    b.  选择“选取文件”，以上传从 Azure 门户下载的元数据文件  。
    
    c.  选择“保存”  。

### <a name="create-netdocuments-test-user"></a>创建 NetDocuments 测试用户

为了使 Azure AD 用户登录 NetDocuments，必须将其预配到 NetDocuments 中。 使用 NetDocuments 时，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 **NetDocuments** 公司站点。

2. 在右上角，选择你的姓名>“管理员”  。
   
    ![管理员](./media/netdocuments-tutorial/user-admin.png "管理员")

3. 选择“用户和组”  。
   
    ![用户和组](./media/netdocuments-tutorial/users-groups.png "存储库")

4. 在“电子邮件地址”文本框中，键入要预配的有效 Azure Active Directory 帐户的电子邮件地址，并单击“添加用户”。  
   
    ![电子邮件地址](./media/netdocuments-tutorial/user-mail.png "电子邮件地址")
   
    > [!NOTE]
    > Azure Active Directory 帐户持有者会获得一封电子邮件，该邮件包含在激活帐户前确认帐户的链接。 可以使用任何其他 NetDocuments 用户帐户创建工具或 NetDocuments 提供的 API 来预配 Azure Active Directory 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 NetDocuments 登录 URL，可在其中启动登录流。 

* 直接转到 NetDocuments 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 NetDocuments 磁贴时，应会自动登录到你为其设置了 SSO 的 NetDocuments。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 NetDocuments 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。