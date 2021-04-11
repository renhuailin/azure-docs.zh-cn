---
title: 针对商业市场中的免费或试用版 SaaS 套餐构建登录页
description: 了解如何为免费或试用版 SaaS 套餐构建登录页。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: e7cee47e90e6484a4258ba82e47af03725c41d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559284"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>针对商业市场中的免费或试用版 SaaS 套餐构建登录页

本文将指导你完成为 Microsoft 商业市场中将销售的免费或试用版 SaaS 应用构建登录页的过程。

## <a name="overview"></a>概述

你可以将登录页视为服务型软件 (SaaS) 套餐的“大厅”。 客户选择获取应用后，商业市场会将他们定向到登录页以激活和配置其 SaaS 应用程序订阅。 创建服务型软件 (SaaS) 套餐时，可以在合作伙伴中心中选择是否[通过 Microsoft 进行销售](plan-saas-offer.md#listing-options)。 如果只想在 Microsoft 商业市场中列出套餐，而不想通过 Microsoft 销售，则可以指定潜在客户与套餐进行交互的方式。 如果启用“立即获取(免费)”或“免费试用”列表选项，则必须指定一个登录页 URL，用户可以通过该 URL 访问免费订阅或试用版 。

登录页的目的只是接收用户，以便他们可以激活免费试用版或免费订阅。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph 可为用户启用单一登录 (SSO)，并获得有关用户的重要详细信息，这些信息可用于激活他们的免费试用版或免费订阅，包括他们的名称、电子邮件地址和组织。

因为激活订阅所需的信息是有限的，并且由 Azure AD 和 Microsoft Graph 提供，所以不需要请求超出基本同意范围以外的信息。 如果所需的用户详细信息需要经过应用程序的额外同意，则应在订阅激活完成后请求此信息。 该操作可顺畅地为用户激活订阅并降低弃购风险。

登录页通常包含以下信息和列表选项：

- 显示免费试用版或免费订阅的名称和详细信息。 例如，指定使用限制或试用期。
- 显示用户的帐户详细信息，包括名字与姓氏、组织和电子邮件。
- 提示用户确认或替换不同的帐户详细信息。
- 激活后，指导用户执行后续步骤。 例如，接收欢迎电子邮件、管理订阅、获取支持或阅读文档。

本文的以下部分将指导你完成构建登录页的过程：

1. 为登录页[创建 Azure AD 应用注册](#create-an-azure-ad-app-registration)。
2. [使用代码示例作为应用的起点](#use-a-code-sample-as-a-starting-point)。
3. [从 ID 令牌中编码的声明中读取信息](#read-information-from-claims-encoded-in-the-id-token)，该令牌是登录后从 Azure AD 接收的，随请求一起发送。
4. 根据需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api) 收集其他信息。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

商业市场与 Azure AD 完全集成。 用户进入通过 [Azure AD 帐户或 Microsoft 帐户 (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology) 进行身份验证的市场。 通过仅限列表的套餐获取免费或免费试用版订阅后，用户将从商业市场转到登录页 URL，以激活和管理其 SaaS 应用程序订阅。 必须让用户通过 Azure AD SSO 登录到你的应用程序。 （登录页 URL 在套餐的[技术配置](plan-saas-offer.md#technical-information)页中指定。

使用标识的第一步是确保登陆页注册为 Azure AD 应用程序。 注册应用程序后，可以使用 Azure AD 对用户进行身份验证并请求访问用户资源。 此操作可被视为应用程序的定义，可让服务知道如何根据应用的设置向应用颁发令牌。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 门户注册新应用程序

若要开始使用，请按照[注册新应用程序](../active-directory/develop/quickstart-register-app.md)的说明进行操作。 若要允许其他公司的用户访问该应用，必须在系统询问你可使用该应用程序的人员时选择“任何组织目录中的帐户(任何 Azure AD 目录 - 多租户)和 Microsoft 个人帐户(如 Skype 或 Xbox)”。

如果要查询 Microsoft Graph API，请[配置新应用程序以访问 Web API](../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 当你为此应用程序选择 API 权限时，默认值 User.Read 足以收集有关用户的基本信息，从而顺畅地自动执行加入过程。 请勿请求任何标记有“需要管理员同意”的 API 权限，否则会阻止所有非管理员用户访问登录页。

如果在执行加入或预配的过程中确实需要提升权限，请考虑使用 Azure AD 的[增量同意](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md)功能，以便发送自市场的所有用户都能够与登录页进行初始交互。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用代码示例作为起点

Microsoft 提供了多个示例应用，可实现一个启用了 Azure AD 登录的简单网站。 在 Azure AD 中注册应用程序之后，“快速入门”边栏选项卡将提供常用应用程序类型和开发堆栈的列表（图 1）。 选择与环境匹配的项，并按照说明进行下载和设置。

***图 1：Azure 门户中的“快速入门”边栏选项卡***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="演示 Azure 门户中的“快速入门”边栏选项卡。":::

下载代码并设置开发环境后，根据前面过程中记录的应用程序 ID、租户 ID 和客户端机密，在应用中更改配置设置。 请注意，具体步骤根据所用的示例而异。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>从 ID 令牌中编码的声明读取信息

作为 [OpenID connect](../active-directory/develop/v2-protocols-oidc.md) 流的一部分，在将用户发送到登录页时，Azure AD 会在请求中添加一个 [ID 令牌](../active-directory/develop/id-tokens.md)。 该令牌包含激活过程中可能用到的多条基本信息，包括下表中显示的信息。

| 值 | 说明 |
| ------------ | ------------- |
| aud | 此令牌的目标受众。 在本例中，它应与应用程序 ID 匹配并经过验证。 |
| preferred_username | 进行访问的用户的主用户名。 可以是电子邮件地址、电话号码或其他标识符。 |
| 电子邮件 | 用户的电子邮件地址。 请注意，此字段可为空。 |
| name | 用户可读值，用于标识令牌使用者。 在本例中，它是用户的姓名。 |
| oid | Microsoft 标识系统中的标识符，用于在不同的应用程序中唯一标识用户。 Microsoft Graph 将返回此值作为给定用户帐户的 ID 属性。 |
| tid | 表示用户所属 Azure AD 租户的标识符。 对于 MSA 标识，此值将始终为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 有关详细信息，请参阅下一部分“使用 Microsoft Graph API”中的说明。 |
| sub | 唯一标识此特定应用程序中的用户的标识符。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

ID 令牌包含用于标识用户的基本信息，但你的激活过程可能需要其他详细信息（例如用户的公司）才能完成加入过程。 使用 [Microsoft Graph API](/graph/use-the-api) 请求这些信息，以避免强制用户再次输入这些详细信息。 默认情况下，标准 User.Read 权限包含以下信息：

| “值” | 说明 |
| ------------ | ------------- |
| displayName | 用户通讯簿中显示的名称。 |
| givenName | 用户的名字。 |
| jobTitle | 用户的职务。 |
| mail | 用户的 SMTP 地址。 |
| mobilePhone | 用户的主要手机号码。 |
| preferredLanguage | 用户首选语言的 ISO 639-1 代码。 |
| surname | 用户的姓氏。 |
|||

可以选择在请求中包含其他属性，例如用户公司的名称或用户的位置（国家/地区）。 有关更多详细信息，请参阅[用户资源类型的属性](/graph/api/resources/user#properties)。

大多数在 Azure AD 中注册的应用都授予从其公司的 Azure AD 租户读取用户信息的委托权限。 向 Microsoft Graph 发出针对该信息的任何请求都必须附带一个访问令牌用于身份验证。 生成访问令牌的具体步骤将取决于所使用的技术堆栈，但示例代码将包含示例。 有关详细信息，请参阅[代表用户获取访问权限](/graph/auth-v2-user)。

> [!NOTE]
> 来自 MSA 租户（租户 ID 为 `9188040d-6c67-4c5b-b112-36a304b66dad`）的帐户返回的信息不会超过已使用 ID 令牌收集的信息。 因此你可以跳过对这些帐户进行的 Graph API 调用。

## <a name="next-steps"></a>后续步骤
- [如何在商业市场中创建 SaaS 产品/服务](create-new-saas-offer.md)