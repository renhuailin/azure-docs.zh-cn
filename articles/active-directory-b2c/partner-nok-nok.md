---
title: 为 Azure Active Directory B2C 配置 Nok Nok 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何为 Azure Active Directory B2C 配置 Nok Nok 以启用无密码 FIDO2 身份验证
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 26842d40034527e5b7b785cfc94e4f7a37e36e04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587207"
---
# <a name="tutorial-configure-nok-nok-with-azure-active-directory-b2c-to-enable-passwordless-fido2-authentication"></a>教程：为 Azure Active Directory B2C 配置 Nok Nok 以启用无密码 FIDO2 身份验证

在此示例教程中，了解如何将 Nok Nok S3 身份验证套件集成到 Azure Active Directory (AD) B2C 租户中。 [Nok Nok](https://noknok.com/) 为移动和 Web 应用程序启用 FIDO UAF、FIDO U2F、WebAuthn 和 FIDO2 等获得了 FIDO 认证的多重身份验证。 使用 Nok Nok，客户可以在平衡用户体验的同时改善其安全状况。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。

- 获取免费 Nok Nok [试用版租户](https://noknok.com/products/strong-authentication-service/)。

## <a name="scenario-description"></a>方案描述

若要为用户启用无密码 FIDO 身份验证，请将 Nok Nok 作为 Azure AD B2C 租户的标识提供者启用。 Nok Nok 集成包括以下组件：

- Azure AD B2C – 负责验证用户凭据的授权服务器。

- Web 和移动应用程序 - 你选择用 Nok Nok 和 Azure AD B2C 保护的移动或 Web 应用程序。

- Nok Nok 应用 SDK 或 Nok Nok Passport 应用 – 用于对启用了 Azure AD B2C 的应用程序进行身份验证的应用程序。 [Apple App Store](https://apps.apple.com/us/app/nok-nok-passport/id1050437340) 和 [Google Play 商店](https://play.google.com/store/apps/details?id=com.noknok.android.passport2&hl=en&gl=US)中提供了这些应用程序。

以下体系结构图体现了实施详情。 Nok Nok 充当 Azure AD B2C 的标识提供者，使用 Open ID Connect (OIDC) 启用无密码身份验证。

![图像显示 nok nok 和 azure ad b2c 的体系结构关系图](./media/partner-nok-nok/nok-nok-architecture-diagram.png)

| 步骤 | 说明 |
|:------|:-----------|
| 1. | 用户访问登录页。 用户选择登录/注册并输入用户名 |
| 2. | Azure AD B2C 将用户重定向到 Nok Nok OIDC 身份验证提供程序。 |
| 3a. | 对于基于移动的身份验证，Nok Nok 会显示 QR 码，或向最终用户的移动设备发送推送通知请求。 |
| 3b. | 对于基于桌面/电脑的登录，Nok Nok 会将最终用户重定向到 Web 应用程序登录页，以启动无密码身份验证提示。 |
|4a. | 用户使用 Nok Nok 应用 SDK 或 Nok Nok Passport 应用扫描其 Smartphone 中显示的 QR 码。|
| 4b. | 用户在 Web 应用程序的登录页上以输入形式提供用户名，并选择“下一步”。 |
| 5a. | 系统提示用户在 Smartphone 上进行身份验证。 <BR> 用户通过使用用户的首选方法（如生物识别、设备 PIN 或任何漫游验证器）执行无密码身份验证。|
| 5b. | 系统提示用户在 Web 应用程序上进行身份验证。 <BR> 用户通过使用用户的首选方法（如生物识别、设备 PIN 或任何漫游验证器）执行无密码身份验证。 |
| 6. | Nok Nok 服务器验证 FIDO 断言，并在验证时将 OIDC 身份验证响应发送到 Azure AD B2C。|
| 7. | 基于响应授予或拒绝用户访问。 |

## <a name="onboard-with-nok-nok"></a>加入 Nok Nok

填写 [Nok Nok 云表单](https://noknok.com/contact/)以创建你自己的 Nok Nok 租户。 提交表单后，将收到一封电子邮件，说明如何访问租户。 电子邮件还将提供对 Nok Nok 指南的访问。 按照 Nok Nok 集成指南中提供的说明，完成 Nok Nok 云租户的 OIDC 配置。

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
   | 名称   | Nok Nok 身份验证提供程序 |
   | 元数据 URL | 插入托管的 Nok Nok Authentication 应用的 URI，后跟具体路径，例如“https://demo.noknok.com/mytenant/oidc/.well-known/openid-configuration” |
   | 客户端机密 | 使用 Nok Nok 平台提供的客户端密码。|
   | 客户端 ID | 使用 Nok Nok 平台提供的客户端 ID。|
   | 范围 | OpenID 配置文件电子邮件 |
   | 响应类型 | code |
   | 响应模式 | form_post|

1. 选择“确定”。

1. 选择“映射此标识提供者的声明”。

1. 填写用于映射标识提供者的表单：

   |属性 | 值 |
   |:-----| :-----------|
   | UserID    | 从订阅中 |
   | 显示名称 | 从订阅中 |
   | 响应模式 | 从订阅中 |

1. 选择“保存”以完成新 OIDC 标识提供者的设置。

### <a name="create-a-user-flow-policy"></a>创建用户流策略

现在，你应该会看到 Nok Nok 作为新 OIDC 标识提供者列在 B2C 标识提供者中。

1. 在 Azure AD B2C 租户中的“策略”下，选择“用户流”。

2. 选择“新建”用户流。

3. 依次选择“注册并登录”、版本、“创建” 。

4. 为策略输入一个“名称”。

5. 在“标识提供者”部分，选择新创建的 Nok Nok 标识提供者。

6. 设置用户流的参数。 插入一个名称并选择已创建的标识提供者。 还可以添加电子邮件地址。 在这种情况下，Azure 不会将登录过程直接重定向到 Nok Nok，而是会显示一个屏幕，供用户在其中选择要使用的选项。

7. 让“多重身份验证”字段保留原样。

8. 选择“强制实施条件访问策略”。

9. 在“用户属性和令牌声明”下的“收集属性”选项中选择“电子邮件地址”。 除了可由 Azure AD B2C 返回到客户端应用程序的声明之外，还可以添加可供 Azure AD 收集的关于用户的所有属性。

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

5. 创建用户属性后，将在流中调用 Nok Nok。 如果该流程未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
