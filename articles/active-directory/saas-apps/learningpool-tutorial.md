---
title: 教程：Azure Active Directory 与 Learning Pool LMS 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Learning Pool LMS 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: jeedes
ms.openlocfilehash: 8829da481abbddc61c1cc90da0984742775059ae
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556485"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-pool-lms"></a>教程：Azure Active Directory 与 Learning Pool LMS 集成

本教程介绍如何将 Learning Pool LMS 与 Azure Active Directory (Azure AD) 集成。 将 Learning Pool LMS 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Learning Pool LMS。
* 让用户使用其 Azure AD 帐户自动登录到 Learning Pool LMS。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 使用单一登录有效订阅 Learning Pool LMS。

> [!NOTE]
> 开始某个单一登录项目时，Learning Pool LMS 交付团队的成员将指导你完成此过程。 如果你没有 Learning Pool LMS 交付团队成员的联系方式，请联系 Learning Pool LMS 帐户经理。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Learning Pool LMS 支持 SP 发起的 SSO。

## <a name="adding-learning-pool-lms-from-the-gallery"></a>从库中添加 Learning Pool LMS

若要配置 Learning Pool LMS 与 Azure AD 的集成，需从库中将 Learning Pool LMS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Learning Pool LMS” 。
1. 从结果面板中选择“Learning Pool LMS”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-learning-pool-lms"></a>配置并测试 Learning Pool LMS 的 Azure AD SSO

使用现有 Azure 用户配置和测试 Learning Pool LMS 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Learning Pool LMS 中的相关用户之间建立关联。

若要配置并测试 Learning Pool LMS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
1. [分配 Azure AD 用户](#assign-an-azure-ad-user) - 使该用户能够使用 Azure AD 单一登录。
1. [配置 Learning Pool LMS SSO](#configure-learning-pool-lms-sso) - 在应用程序端配置单一登录设置。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Learning Pool LMS”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，如果有 **服务提供程序元数据文件**，请执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”值会自动填充在“基本 SAML 配置”部分中  。

    在“登录 URL”文本框中，键入 URL：  `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`

    > [!Note]
    > 如果“标识符”值未自动填充，请根据要求手动填充该值。 

5. 必须至少发送一个属性，该属性用于将 Azure 用户与 Learning Pool LMS 上的用户进行匹配。 通常，默认属性就足够了，但在某些情况下，可能需要发送某些自定义属性。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框，并根据需要添加更多属性。

    ![屏幕截图显示“用户属性”，并且已选择“编辑”图标。](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ： 

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![屏幕截图显示“用户声明”以及“添加新声明”选项。](common/new-save-attribute.png)

    ![屏幕截图显示“管理用户声明”对话框，可在其中输入所述的值。](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

7. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“应用联合元数据 URL”旁边的“复制”按钮，然后将该 URL 传递回 Learning Pool 交付团队  。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="assign-an-azure-ad-user"></a>分配 Azure AD 用户

在本部分，你将通过授予现有 Azure AD 用户访问 Learning Pool LMS 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Learning Pool LMS”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框的“用户”列表中选择合适的用户，然后单击屏幕底部的“选择”按钮 。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-learning-pool-lms-sso"></a>配置 Learning Pool LMS SSO

Learning Pool 交付团队将使用“应用联合元数据 URL”将 LMS 配置为接受 SAML2 连接。 你将被要求执行一些测试步骤以验证连接配置是否正确，而 Learning Pool 交付团队将指导你完成此过程。

### <a name="test-sso"></a>测试 SSO

Learning Pool 交付团队将指导你完成测试过程。

## <a name="next-steps"></a>后续步骤

配置 Learning Pool LMS 后，即可强制实施会话控制，从而实时保护组织的敏感数据，使其免遭外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
