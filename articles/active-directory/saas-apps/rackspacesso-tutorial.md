---
title: 教程：Azure Active Directory 与 Rackspace SSO 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Rackspace SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 50b0a6bdb58559f5b0ca5c22991f6f6611269932
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758933"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>教程：Azure Active Directory 与 Rackspace SSO 的集成

本教程介绍如何将 Rackspace SSO 与 Azure Active Directory (Azure AD) 相集成。 将 Rackspace SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Rackspace SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Rackspace SSO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Rackspace SSO 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Rackspace SSO 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Rackspace SSO 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-rackspace-sso-from-the-gallery"></a>从库中添加 Rackspace SSO

要配置 Rackspace SSO 与 Azure AD 的集成，需要从库中将 Rackspace SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Rackspace SSO 。
1. 在结果面板中选择“Rackspace SSO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-rackspace-sso"></a>配置并测试 Rackspace SSO 的 Azure AD SSO

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Rackspace SSO 的 Azure AD 单一登录。
将单一登录用于 Rackspace 时，首次登录到 Rackspace 门户时，将自动创建 Rackspace 用户。 

若要配置并测试 Rackspace SSO 的 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 Rackspace SSO 单一登录](#configure-rackspace-sso-single-sign-on)** - 在应用程序端配置单一登录设置。
    1. **[在 Rackspace 控制面板中设置属性映射](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - 为 Azure AD 用户分配 Rackspace 角色。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Rackspace SSO”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”中，上传你可以从 [URL](https://login.rackspace.com/federate/sp.xml) 下载的 **服务提供程序元数据文件**，然后执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![屏幕截图显示了“基本 SAML 配置”，其中包含“上传元数据文件”链接。](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![屏幕截图显示了可在其中选择并上传文件的对话框。](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，将自动填充所需的 URL。

    d. 在“登录 URL”文本框中，键入 URL：`https://login.rackspace.com/federate/`

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

此文件将上传到 Rackspace 来填充所需的联合身份验证配置设置。

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

在本部分，你将通过授予 B.Simon 访问 Rackspace SSO 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Rackspace SSO”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-rackspace-sso-single-sign-on"></a>配置 Rackspace SSO 单一登录

若要在 **Rackspace SSO** 端配置单一登录，请执行以下操作：

1. 请参阅文档[向控制面板中添加标识提供者](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. 它将引导你完成以下步骤：
    1. 创建新的标识提供者
    1. 指定在登录时用户将用来标识你的公司的电子邮件域。
    1. 上传之前从 Azure 控制面板下载的 **联合元数据 XML**。

这将正确配置 Azure 和 Rackspace 进行连接所需的基本 SSO 设置。

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>在 Rackspace 控制面板中设置属性映射

Rackspace 使用 **属性映射策略** 为你的单一登录用户分配 Rackspace 角色和组。 **属性映射策略** 将 Azure AD SAML 声明转换为 Rackspace 所需的用户配置字段。 可以在 Rackspace [属性映射基础知识文档](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)中找到更多文档。 注意事项：

* 如果希望使用 Azure AD 组分配可变级别的 Rackspace 访问权限，则需要在 Azure **Rackspace SSO** 单一登录设置中启用组声明。 然后，可以使用 **属性映射策略** 将那些组匹配到所需的 Rackspace 角色和组：

    ![组声明设置](common/sso-groups-claim.png)

* 默认情况下，Azure AD 会在 SAML 声明中发送 Azure AD 组的 UID 而非组名称。 但是，如果要将本地 Active Directory 同步到 Azure AD，则可以选择发送组的实际名称：

    ![组声明名称设置](common/sso-groups-claims-names.png)

下面的示例 **属性映射策略** 演示了以下内容：
1. 将 Rackspace 用户的名称设置为 `user.name` SAML 声明。 可以使用任何声明，但最常见的做法是将其设置为包含用户电子邮件地址的字段。
1. 通过按组名称或组 UID 匹配 Azure AD 组来为用户设置 Rackspace 角色 `admin` 和 `billing:admin`。 `roles` 字段中使用了替代项 `"{0}"`，它将替换为 `remote` 规则表达式的结果。
1. 使用  默认替代项`"{D}"` 让 Rackspace 通过在 SAML 交换中查找标准且已知的 SAML 声明来检索额外的 SAML 字段。

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> 在编辑策略文件时，请确保使用可以验证 YAML 语法的文本编辑器。

有关更多示例，请参阅 Rackspace [属性映射基础知识文档](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Rackspace SSO 登录 URL，可从中启动登录流。 

* 直接转到 Rackspace SSO 登录 URL，并从中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Rackspace SSO”磁贴时，会重定向到 Rackspace SSO 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

还可以使用 **Rackspace SSO** 单一登录设置中的“验证”按钮：

   ![SSO“验证”按钮](common/sso-validate-sign-on.png)

## <a name="next-steps"></a>后续步骤

配置 Rackspace SSO 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。