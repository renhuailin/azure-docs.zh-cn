---
title: Trusona 和 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何在 Azure AD B2C 上将 Trusona 添加为标识提供者以启用无密码身份验证。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2efd5fdc49f5ae6724b9d1dc3b45d54329f17a61
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223943"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>将 Trusona 与 Azure Active Directory B2C 集成

Trusona 是一个独立软件供应商 (ISV) 提供程序，可通过启用无密码身份验证、多重身份验证和数字许可证扫描来帮助保护登录。 在本文中，你将了解如何在 Azure AD B2C 中将 Trusona 添加为标识提供者以启用无密码身份验证。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。
* Trusona 上的一个[试用帐户](https://www.trusona.com/)

## <a name="scenario-description"></a>方案描述

在此方案中，Trusona 充当 Azure AD B2C 的标识提供者来启用无密码身份验证。 以下组件构成了解决方案：

* Azure AD B2C 合并的登录和注册策略
* 作为标识提供者添加到 Azure AD B2C 的 Trusona
* 可下载的 Trusona 应用

![Trusona 体系结构图](media/partner-trusona/trusona-architecture-diagram.png)

| 步骤 | 说明 |
|------|------|
|1     | 用户尝试登录或注册应用程序。 用户通过 Azure AD B2C 登录和注册策略进行身份验证。 在注册过程中，将使用用户以前在 Trusona 应用中验证的电子邮件地址。     |
|2     | Azure B2C 使用隐式流将用户重定向到 Trusona OpenID Connect (OIDC) 标识提供者。     |
|3     | 对于基于台式电脑的登录，Trusona 会显示一个独一无二的、无状态的、具有动画效果的动态 QR 码，以便使用 Trusona 应用进行扫描。 对于基于移动设备的登录，Trusona 使用“深层链接”来打开 Trusona 应用。 这两种方法都用于设备，最终会用于用户发现。     |
|4     | 用户通过 Trusona 应用扫描显示的 QR 码。     |
|5     | 用户的帐户位于 Trusona 云服务中，已准备好进行身份验证。     |
|6     | Trusona 云服务通过一个发送到 Trusona 应用的推送通知向用户发出身份验证质询：<br>a. 系统提示用户响应身份验证质询。 <br> b. 用户选择接受或拒绝质询。 <br> c. 要求用户使用 OS 安全性（例如，生物识别、密码、PIN 或模式）通过安全飞地/受信任执行环境中的私钥来确认和签署质询。 <br> d. Trusona 应用根据身份验证的参数实时生成动态抗重放有效负载。 <br> e. 整个响应由安全飞地/受信任执行环境中的私钥进行签名（第二次），并返回到 Trusona 云服务进行验证。      |
|7     |  Trusona 云服务使用 id_token 将用户重定向回发起应用程序。 Azure AD B2C 使用在设置标识提供者过程中配置的 Trusona 的已发布 OpenID 配置来验证 id_token。    |
|  |  |

## <a name="onboard-with-trusona"></a>通过 Trusona 加入

1. 填写[表单](https://www.trusona.com/)以创建一个 Trusona 帐户并开始使用。

2. 从应用商店下载 Trusona 移动应用。 安装应用并注册你的电子邮件。

3. 通过该软件发送的安全“魔术链接”验证你的电子邮件。  

4. 转到用于自助的 [Trusona 开发者仪表板](https://dashboard.trusona.com)。

5. 选择“我已准备就绪”，自行向 Trusona 应用进行身份验证。

6. 从左侧导航面板中，选择“OIDC 集成”。

7. 选择“创建 OpenID Connect 集成”。

8. 提供你选择的名称，并在“客户端重定向主机”字段中使用之前提供的域信息（例如 Contoso）。  

   > [!NOTE]
   > Azure Active Directory 的初始域名用作客户端重定向主机。

9. 按照 [Trusona 集成指南](https://docs.trusona.com/integrations/aad-b2c-integration/)中的说明进行操作。 出现提示时，使用上一步提到的初始域名（例如 Contoso）。  

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="add-a-new-identity-provider"></a>添加新的标识提供者

> [!NOTE]
> 如果没有租户，请[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。

4. 导航到“仪表板” > “Azure Active Directory B2C” > “标识提供者”。  

3. 选择“标识提供者”。

4. 选择 **添加** 。

### <a name="configure-an-identity-provider"></a>配置标识提供者  

1. 选择“标识提供者类型” > “OpenID Connect (预览版)”。 

2. 填写用于设置标识提供者的表单：  

   | 属性 | 值  |
   | :--- | :--- |
   | 元数据 URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | 客户端 ID | 将从 Trusona 以电子邮件的形式发送给你 |
   | 范围 | OpenID 配置文件电子邮件 |
   | 响应类型 | Id_token |
   | 响应模式  | Form_post |

3. 选择“确定”。  

4. 选择“映射此标识提供者的声明”。  

5. 填写用于映射标识提供者的表单：

   | 属性 | 值  |
   | :--- | :--- |
   | UserID | Sub  |
   | 显示名称 | nickname |
   | 给定名称 | given_name |
   | Surname | Family_name |
   | 响应模式 | 电子邮件 |

6. 选择“确定”以完成新 OIDC 标识提供者的设置。

### <a name="create-a-user-flow-policy"></a>创建用户流策略

现在，你应该会看到 Trusona 作为新 OpenID Connect 标识提供者列出在 B2C 标识提供者中。

1. 在 Azure AD B2C 租户中的“策略”下，选择“用户流”。

1. 选择“新建用户流”。

1. 依次选择“注册并登录”、版本、“创建”。

1. 为策略输入一个“名称”。

1. 在“标识提供者”部分，选择新创建的“Trusona 标识提供者”。

   > [!NOTE]
   > 由于 Trusona 本质上采用的是多重身份验证，因此最好是让多重身份验证保持禁用状态。

1. 选择“创建”  。

1. 在“用户特性和声明”下，选择“显示更多”。  在表单中，选择在前面部分的设置标识提供者过程中指定的至少一个特性。

1. 选择“确定”。  

### <a name="test-the-policy"></a>测试策略

1. 选择你创建的策略。

1. 选择“运行用户流”，然后选择设置：

   1. 应用程序：选择已注册的应用。
 
   1. 回复 URL：选择重定向 URL。
   
1. 选择“运行用户流”。 系统会将你重定向到 Trusona OIDC 网关。 在 Trusona 网关上，使用 Trusona 移动 SDK 通过 Trusona 应用或自定义应用扫描显示的安全 QR 码。

1. 扫描安全 QR 码后，将重定向到你定义的回复 URL。

## <a name="next-steps"></a>后续步骤  

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

- [AAD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
