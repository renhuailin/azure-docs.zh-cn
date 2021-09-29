---
title: 使用 Azure Active Directory B2C 的 itsme OpenID Connect
titleSuffix: Azure AD B2C
description: 了解如何使用客户端密码用户流策略将 Azure AD B2C 身份验证与 itsme OIDC 集成。 itsme 是一个数字 ID 应用。 它无需读卡器、密码、双因素身份验证和多个 PIN 代码，即可让用户实现安全登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 03b2f3a4b0a9876b1880194233c81138589d1331
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583290"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 配置 itsme OpenID Connect (OIDC)

itsme 数字 ID 应用无需读卡器、密码、双因素身份验证或多个 PIN 代码，即可让用户实现安全登录。 Itsme 应用可使用已验证的标识提供强大的客户身份验证功能。 本文介绍了如何使用客户端密码用户流策略将 Azure AD B2C 身份验证与 itsme OpenID Connect (OIDC) 集成。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。
* itsme 提供的客户端 ID（也称为合作伙伴代码）。
* itsme 提供的服务代码。
* itsme 帐户的客户端密码。

## <a name="scenario-description"></a>方案描述

![itsme 体系结构示意图](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| 步骤 | 说明 |
|------|------|
|1     | 在网站或应用程序上，在 Azure AD B2C 用户流中进行调整，添加“**使用 itsme 登录**”按钮。 当用户单击此按钮时，将启动交互流。  |
|2     | Azure AD B2C 通过向 itsme 客户端密码 API 发送授权请求来启动 OpenID connect 流。 提供的已知/OpenID 配置终结点包含有关终结点的信息。  |
|3     | itsme 环境将用户重定向到 itsme 标识页，让用户填写其电话号码。  |
|4     | itsme 环境接收来自用户的电话号码并验证其正确性。  |
|5     | 如果电话号码属于活动的 itsme 用户，则会为 itsme 应用创建一个操作。  |
|6     | 用户打开 itsme 应用，检查请求，并确认该操作。  |
|7     |  应用通知 itsme 环境操作已得到确认。 |
|8     |  itsme 环境将 OAuth 授权代码返回给 Azure AD B2C。 |
|9     |  Azure AD B2C 使用授权代码执行令牌请求。 |
| 10 | itsme 环境检查令牌请求，如果仍然有效，则返回 OAuth 访问令牌以及包含所请求用户信息的 ID 令牌。 |
| 11 | 最后，将用户作为经过身份验证的用户重定向到重定向 url。  |
|   |   |

## <a name="onboard-with-itsme"></a>加入 itsme

1. 若要创建 itsme 帐户，请访问 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace)上的 itsme。

1. 请通过向 onboarding@itsme.be 发送电子邮件来激活 itsme 帐户。 用户将收到 B2C 设置所需的 **合作伙伴代码** 和 **服务代码**。

1. itsme 合作伙伴帐户激活后，用户将收到一封电子邮件，其中包含指向 **客户端密码** 的一次性链接。

1. 按照 [itsme](https://business.itsme.be/en) 中的说明完成配置。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>在 Azure AD B2C 中设置新的标识提供者

> [!NOTE]
> 如果没有租户，请[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。

1. 确保你正在使用包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“**Azure 服务**”下，选择“**Azure AD B2C**”（或选择“**更多服务**”，并使用“**所有服务**”搜索框来搜索 *Azure AD B2C*）。
1. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。
1. 使用以下信息填写窗体：

   |属性 | 值 |
   |------------ |------- |
   | 名称 | itsme |
   | 元数据 URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>其中，`<environment>` 是 `e2e`（测试环境）或 `prd`（生产）  |
   | ClientID     | **客户端 ID**（也称为 **合作伙伴代码**）  |
   | 客户端机密 | **客户端密码** |
   | 范围  | openid 服务：YOURSERVICECODE 配置文件 电子邮件 [电话] [地址]  |
   |响应类型 | code |
   |响应模式 | query |
   |域提示 | *可以将此项留空* |
   |UserID | sub |
   |显示名称 | name |
   |给定名称 | given_name |
   |Surname | family_name |
   |电子邮件 | 电子邮件|

1. 选择“保存”。

### <a name="configure-a-user-flow"></a>配置用户流

1. 在 Azure AD B2C 租户中的“**策略**”下，选择“**用户流**”。

1. 选择“新建用户流”。

1. 选择“**注册并登录**”，选择一个版本，然后选择“**创建**”。

1. 输入“名称”。

1. 在“**标识提供者**”部分中，选择“**itsme**”。

1. 选择“创建”  。

1. 选择用户流名称，打开新创建的用户流。

1. 选择“**属性**”，并调整以下值：

   * 将 **访问与 ID 令牌生存期（分钟）** 更改为 **5**。
   * 将 **刷新令牌滑动窗口生存期** 更改为 **永不过期**。

### <a name="register-an-application"></a>注册应用程序

1. 在 B2C 租户中的“**管理**”下，选择“**应用注册**” > “**新建注册**”。

1. 提供应用程序的 **名称**，然后输入 **重定向 URI**。 出于测试目的，请输入 `https://jwt.ms`。

1. 请确保多重身份验证 **已禁用**。

1. 选择“注册”。

   a. 处于测试目的，选择“**身份验证**”，然后在“**隐式授予**”下，选中“**访问令牌**”和“**ID 令牌**”复选框。  

   b. 选择“保存”。

## <a name="test-the-user-flow"></a>测试用户流

1. 在 B2C 租户中，在“**策略**”下选择“**用户流**”。

1. 选择前面创建的用户流。

1. 选择“运行用户流”。

   a. **应用程序**：*选择已注册的应用*

   b. **回复 URL**：*选择重定向 URL*

1. 此时将显示 itsme **标识自己** 页。  

1. 输入移动电话号码，然后选择“**发送**”。

1. 确认 itsme 应用中的操作。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章：

* [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

* [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)