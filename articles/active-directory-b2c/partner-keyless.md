---
title: 有关配置无键与 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 有关为无键配置无密码 authentication Azure Active Directory B2C 的教程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b817cfc347ee79ff7c9cbb4124e3f2b7e4d2b7ee
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644249"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>教程：配置无键与 Azure Active Directory B2C

在此示例教程中，我们提供了有关如何配置 [无键](https://keyless.io/) (AD) B2C Azure Active Directory 的指导。 使用 Azure AD B2C 作为标识提供者，你可以将无键与任何客户应用程序集成，为用户提供真正的无密码身份验证。

无键的解决方案 **无键 Zero-Knowledge 生物识别 (ZKB™)** 提供无密码多重身份验证，可消除欺诈性、网络钓鱼和凭据的重复使用–同时增强客户体验并保护其隐私。

## <a name="pre-requisites"></a>先决条件

若要开始，你将需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户必须链接到你的 Azure 订阅。

- 无键 cloud 租户，获取免费 [试用帐户](https://keyless.io/go)。

- 安装在用户设备上的无键验证器应用。

## <a name="scenario-description"></a>方案描述

无键集成包括以下组件：

- Azure AD B2C –授权服务器，负责验证用户的凭据（也称为标识提供者）。

- Web 和移动应用程序–您选择用无键和 Azure AD B2C 来保护的您的移动或 web 应用程序。

- 无键移动应用–无键移动应用用于向启用 Azure AD B2C 的应用程序进行身份验证。

下面的体系结构关系图显示了实现。

![图像显示无键体系结构图](./media/partner-keyless/keyless-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择登录/注册并输入用户名
| 2. | 应用程序将用户属性发送到 Azure AD B2C 进行身份验证。
| 3. | Azure AD B2C 收集用户属性并将属性发送到无键，以通过无键移动应用对用户进行身份验证。
| 4. | 无键向注册用户的移动设备发送一条推送通知，以面部生物识别扫描的形式提供隐私保留身份验证。
| 5. | 用户响应推送通知后，将根据验证结果向用户授予或拒绝对客户应用程序的访问权限。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="add-a-new-identity-provider"></a>添加新的标识提供者

若要添加新的标识提供程序，请执行以下步骤：

1. 以 Azure AD B2C 租户的全局管理员身份登录到 **[Azure 门户](https://portal.azure.com/#home)** 。

2. 通过在顶部菜单中选择 " **目录 + 订阅** " 筛选器并选择包含租户的目录，确保使用的是包含 Azure AD B2C 租户的目录。

3. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。

4. 导航到 **仪表板**  >  **Azure Active Directory B2C**  >   **标识提供者**

5. 选择 " **标识提供者**"。

6. 选择“添加”。

### <a name="configure-an-identity-provider"></a>配置标识提供者

若要配置标识提供程序，请执行以下步骤：

1. 选择 **标识提供者类型**  >  **OpenID connect (预览)**
2. 填写表单以设置标识提供者：

   |属性 | Value |
   |:-----| :-----------|
   | 名称   | Keyless |
   | 元数据 URL | 插入托管无键 Authentication 应用程序的 URI，后跟特定路径，如 " https://keyless.auth/.well-known/openid-configuration " |
   | 客户端机密 | 与无键 Authentication 实例关联的机密-与以前配置的不同。 插入所选的复杂字符串。 稍后将在无键容器配置中使用此密钥。|
   | 客户端 ID | 客户端的 ID。 稍后将在无键容器配置中使用此 ID。|
   | 范围 | openid |
   | 响应类型 | id_token |
   | 响应模式 | form_post|

3. 选择“确定”。

4. 选择 " **映射此标识提供者的声明**"。

5. 填写表单以映射标识提供者：

   |属性 | “值” |
   |:-----| :-----------|
   | UserID    | 从订阅中 |
   | 显示名称 | 从订阅中 |
   | 响应模式 | 从订阅中 |

6. 选择 " **保存** " 以完成新的开放 ID 连接 (OIDC) 标识提供程序的设置。

### <a name="create-a-user-flow-policy"></a>创建用户流策略

现在应会看到无键作为 B2C 标识提供者中列出的新 OIDC 标识提供者。

1. 在 Azure AD B2C 租户中的 " **策略**" 下，选择 " **用户流**"。

2. 选择 " **新建** 用户流"。

3. 选择 " **注册并登录**"，选择一个 **版本**，然后选择 " **创建**"。

4. 输入策略的 **名称** 。

5. 在 "标识提供者" 部分中，选择新创建的无键标识提供者。

6. 设置用户流的参数。 插入名称并选择已创建的标识提供者。 你还可以添加电子邮件地址。 在这种情况下，Azure 不会将登录过程直接重定向到无键，而是显示一个屏幕，用户可以在其中选择要使用的选项。

7. 按原样保留 " **多重身份验证** " 字段。

8. 选择 "**强制条件性访问策略**"

9. 在 " **用户属性和令牌声明**" 下，在 "收集属性" 选项中选择 " **电子邮件地址** "。 你可以添加 Azure Active Directory 可以与 Azure AD B2C 可以返回到客户端应用程序的声明一起收集的所有属性。

10. 选择“创建”。

11. 成功创建后，选择新的 **用户流**。

12. 在左侧面板中，选择 " **应用程序声明**"。 在 "选项" 下，勾选 **电子邮件** 复选框，然后选择 " **保存**"。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，并在 "策略" 下选择 "标识体验框架"。

2. 选择先前创建的 SignUpSignIn。

3. 选择 "运行用户流"，然后选择设置：

   a. 应用程序：选择注册应用 (示例为 JWT) 

   b. 回复 URL：选择 "重定向 URL"

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 创建用户属性后，将在流中调用无键。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)