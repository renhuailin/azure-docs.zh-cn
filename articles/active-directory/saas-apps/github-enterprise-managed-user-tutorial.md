---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 GitHub Enterprise Managed User 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 GitHub Enterprise Managed User 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 6038db88610e1fde8d95c2c31a9bcfa2c5ad5ac2
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904240"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>教程：Azure Active Directory 单一登录 (SSO) 与 GitHub Enterprise Managed User 的集成

本教程介绍如何将 GitHub Enterprise Managed User (EMU) 与 Azure Active Directory (Azure AD) 相集成。 将 GitHub Enterprise Managed User 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 GitHub Enterprise Managed User。
* 让用户使用其 Azure AD 帐户自动登录到 GitHub Enterprise Managed User。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 GitHub Enterprise Managed User 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* GitHub Enterprise Managed User 支持 **SP 和 IDP** 发起的 SSO。
* GitHub Enterprise Managed User 需要[自动用户预配](./github-enterprise-managed-user-provisioning-tutorial.md)。

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>从库中添加 GitHub Enterprise Managed User

若要配置 GitHub Enterprise Managed User 与 Azure AD 的集成，需要从库中将 GitHub Enterprise Managed User 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **GitHub Enterprise Managed User**。
1. 在结果面板中选择“GitHub Enterprise Managed User”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>配置并测试 GitHub Enterprise Managed User 的 Azure AD SSO

若要配置并测试 GitHub Enterprise Managed User 的 Azure AD SSO，请执行以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso) - 在 AAD 租户中启用 SAML 单一登录。
1. [配置 GitHub Enterprise Managed User SSO](#configure-github-enterprise-managed-user-sso) - 在 GitHub Enterprise 中配置单一登录设置。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“GitHub Enterprise Managed User”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在开始之前，请确保已准备好你的企业 URL。 下面提到的“实体”字段是启用了 EMU 的企业 URL 的企业名称。 例如， https://github.com/enterprises/contoso -  contoso 就是实体。 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://github.com/enterprises/<ENTITY>`
    
    > [!NOTE]
    > 请注意，标识符格式不同于应用程序的建议格式 - 请遵循上述格式。 此外，请确保标识符不包含尾部斜杠。
    
    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://github.com/enterprises/<ENTITY>/saml/consume`
    

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://github.com/enterprises/<ENTITY>/sso`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificate-base64-download.png)

1. 在“设置 GitHub Enterprise Managed User”部分，复制并保存以下 URL，供稍后在配置 GitHub 时使用。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你要将自己的帐户分配到 GitHub Enterprise Managed User 以完成 SSO 设置。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“GitHub Enterprise Managed User”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框的“用户”列表中选择你的帐户，然后单击屏幕底部的“选择”按钮 。
1. 在“选择角色”对话框中选择“企业所有者”角色，然后单击屏幕底部的“选择”按钮  。 在下一篇教程中预配你的帐户时，该帐户将分配为 GitHub 实例的企业所有者。 
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-github-enterprise-managed-user-sso"></a>配置 GitHub Enterprise Managed User SSO

若要在 GitHub Enterprise Managed User 一端配置单一登录，需要指定以下各项：

1. 上述 AAD Enterprise Managed User 应用程序中的 URL：登录 URL；Azure AD 标识符；注销 URL
1. GitHub Enterprise 的第一个管理员用户的帐户名和密码。 这些凭据是通过 GitHub 解决方案工程联系人所发送的密码重置电子邮件提供的。 

### <a name="enable-github-enterprise-managed-user-saml-sso"></a>启用 GitHub Enterprise Managed User SAML SSO

在本部分，你将采用 AAD 提供的上述信息，并将其输入到企业设置中以启用 SSO 支持。

1. 转到 https://github.com
1. 单击右上角的“登录”
1. 输入第一个管理员用户帐户的凭据。 登录句柄应采用以下格式：`<your enterprise short code>_admin`
1. 导航到 `https://github.com/enterprises/` `<your enterprise name>`。 此信息应由解决方案工程联系人提供。
1. 在左侧导航菜单中，依次选择“设置”、“安全性” 。
1. 单击“启用 SAML 身份验证”复选框
1. 填写“登录 URL”。 此 URL 是前面从 AAD 复制的“登录 URL”。
1. 填写“颁发者”。 此 URL 是前面从 AAD 复制的“Azure AD 标识符”。
1. 填写“公共证书”。 请打开前面下载的 base64 证书，并将该文件的文本内容粘贴到此对话框中。
1. 单击“测试 SAML 配置”。 此时会打开一个对话框，可在其中使用 Azure AD 凭据登录，以验证 SAML SSO 是否已正确配置。 使用 AAD 凭据登录。 成功验证后，你将收到消息“已通过: 已成功对你的 SAML SSO 标识进行身份验证”。
1. 单击“保存”以保存这些设置。
1. 请在安全的位置保存（下载、打印或复制）恢复代码。
1. 单击“启用 SAML 身份验证”。
1. 此时，只有使用 SSO 的帐户才能登录到你的企业。 请按照以下文档中的预配说明来预配 SSO 支持的帐户。

## <a name="next-steps"></a>后续步骤

GitHub Enterprise Managed User 还要求通过自动用户预配创建所有帐户，在[此处](./github-enterprise-managed-user-provisioning-tutorial.md)可以找到有关如何配置自动用户预配的更多详细信息。
