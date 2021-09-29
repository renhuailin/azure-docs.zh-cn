---
title: 为 Azure Active Directory B2C 配置 Keyless 的教程
titleSuffix: Azure AD B2C
description: 为 Azure Active Directory B2C 配置 Keyless 以进行无密码身份验证的教程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: bc8bc67ca2fd7a023b68f6aa2e21527e26281dd3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588421"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>教程：为 Azure Active Directory B2C 配置 Keyless

在本示例教程中，我们提供了有关如何为 Azure Active Directory (AD) B2C 配置 [Keyless](https://keyless.io/) 的指导。 使用 Azure AD B2C 作为标识提供者，你可以将 Keyless 与任何客户应用程序集成，为用户提供真正的无密码身份验证。

Keyless 的“Keyless Zero-Knowledge Biometric (ZKB™)”解决方案提供无密码多重身份验证，可以防止欺诈、网络钓鱼和凭据重复使用，同时增强客户体验并保护其隐私。

## <a name="pre-requisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户必须关联到你的 Azure 订阅。

- Keyless 云租户。你可以获取一个免费[试用帐户](https://keyless.io/go)。

- 安装在用户设备上的 Keyless Authenticator 应用。

## <a name="scenario-description"></a>方案描述

Keyless 集成包括以下组件：

- Azure AD B2C - 授权服务器，负责验证用户的凭据，也称为标识提供者。

- Web 和移动应用程序 - 你选择用 Keyless 和 Azure AD B2C 保护的移动或 Web 应用程序。

- Keyless 移动应用 - Keyless 移动应用将用于向启用了 Azure AD B2C 的应用程序进行身份验证。

以下体系结构图体现了实施详情。

![图像显示了 Keyless 体系结构图](./media/partner-keyless/keyless-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户访问登录页。 用户选择登录/注册并输入用户名
| 2. | 应用程序将用户属性发送到 Azure AD B2C，以便进行标识验证。
| 3. | Azure AD B2C 收集用户属性并将属性发送到 Keyless，以通过 Keyless 移动应用对用户进行身份验证。
| 4. | Keyless 向已注册用户的移动设备发送一条推送通知，以面部生物识别扫描的形式进行保护隐私的身份验证。
| 5. | 在用户响应推送通知后，将根据验证结果向用户授予客户应用程序的访问权限或拒绝其对客户应用程序的访问。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="add-a-new-identity-provider"></a>添加新的标识提供者

若要添加新的标识提供者，请执行以下步骤：

1. 以 Azure AD B2C 租户的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/#home)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
1. 导航到“仪表板” > “Azure Active Directory B2C” >  “标识提供者”。  
1. 选择“标识提供者”。
1. 选择 **添加** 。

### <a name="configure-an-identity-provider"></a>配置标识提供者

若要配置标识提供者，请执行以下步骤：

1. 选择“标识提供者类型” > “OpenID Connect (预览版)” 
1. 填写用于设置标识提供者的表单：

   |属性 | 值 |
   |:-----| :-----------|
   | 名称   | Keyless |
   | 元数据 URL | 插入托管的 Keyless Authentication 应用的 URI，后跟具体路径，例如“https://keyless.auth/.well-known/openid-configuration” |
   | 客户端机密 | 与 Keyless Authentication 实例关联的机密 - 与以前配置的机密不同。 插入你选择的一个复杂字符串。 稍后将在 Keyless 容器配置中使用此机密。|
   | 客户端 ID | 客户端的 ID。 稍后将在 Keyless 容器配置中使用此 ID。|
   | 范围 | openid |
   | 响应类型 | id_token |
   | 响应模式 | form_post|

1. 选择“确定”。

1. 选择“映射此标识提供者的声明”。

1. 填写用于映射标识提供者的表单：

   |属性 | 值 |
   |:-----| :-----------|
   | UserID    | 从订阅中 |
   | 显示名称 | 从订阅中 |
   | 响应模式 | 从订阅中 |

1. 选择“保存”以完成新 Open ID Connect (OIDC) 标识提供者的设置。

### <a name="create-a-user-flow-policy"></a>创建用户流策略

现在，你应该会看到 Keyless 作为新 OIDC 标识提供者列在 B2C 标识提供者中。

1. 在 Azure AD B2C 租户中的“策略”下，选择“用户流”。

2. 选择“新建”用户流。

3. 依次选择“注册并登录”、版本、“创建” 。

4. 为策略输入一个“名称”。

5. 在“标识提供者”部分，选择新创建的 Keyless 标识提供者。

6. 设置用户流的参数。 插入一个名称并选择已创建的标识提供者。 还可以添加电子邮件地址。 在这种情况下，Azure 不会将登录过程直接重定向到 Keyless，而是会显示一个屏幕，供用户在其中选择要使用的选项。

7. 让“多重身份验证”字段保留原样。

8. 选择“强制实施条件访问策略”。

9. 在“用户属性和令牌声明”下的“收集属性”选项中选择“电子邮件地址”。 除了可由 Azure AD B2C 返回到客户端应用程序的声明之外，还可以添加可供 Azure Active Directory 收集的关于用户的所有属性。

10. 选择“创建”。

11. 成功创建后，选择你的新“用户流”。

12. 在左侧面板中，选择“应用程序声明”。 在“选项”下，勾选“电子邮件”复选框，然后选择“保存”。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“Identity Experience Framework”。

2. 选择前面创建的“SignUpSignIn”。

3. 选择“运行用户流”，然后选择设置：

   a. 应用程序：选择已注册的应用（示例为 JWT）

   b. 回复 URL：选择重定向 URL

   c. 选择“运行用户流”。

4. 完成注册流程并创建帐户

5. 创建用户属性后，将在流中调用 Keyless。 如果该流程未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)