---
title: 为商业市场中的可交易 SaaS 产品/服务构建登陆页
description: 了解如何为可交易的 SaaS 产品/服务构建登陆页。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549866"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>为商业市场中的可交易 SaaS 产品/服务构建登陆页

本文指导你完成为 Microsoft 商业市场中销售的可交易 SaaS 应用构建登陆页的过程。

## <a name="overview"></a>概述

可将登陆页视为软件即服务(SaaS) 产品/服务的“大厅”。 买家订阅某个产品/服务后，商业市场会将其定向到登陆页以激活并配置他们对 SaaS 应用程序的订阅。 这相当于一个订单确认步骤，可让买家查看他们购买的内容，并确认其帐户详细信息。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph，你可为买家启用单一登录 (SSO)，并获取有关买家的重要详细信息，这些信息可用来确认并激活他们的订阅，包括他们的姓名、电子邮件地址和组织。

因为激活订阅所需的信息是有限的，并且由 Azure AD 和 Microsoft Graph 提供，所以不需要请求超出基本同意范围以外的信息。 如果所需的用户详细信息需要经过应用程序的额外同意，则应在订阅激活完成后请求此信息。 该操作可顺畅地为买家激活订阅并降低放弃购买的风险。

登陆页通常包括以下内容：

- 显示所购产品/服务和计划的名称，以及计费条款。
- 显示用户的帐户详细信息，包括名字与姓氏、组织和电子邮件。
- 提示买家确认或替换不同的帐户详细信息。
- 激活后，指导用户完成后续步骤。 例如，接收欢迎电子邮件、管理订阅、获取支持或阅读文档。

> [!NOTE]
> 激活后，买家在管理其订阅时也会定向到登陆页。 激活买家的订阅后，必须使用 SSO 来让用户登录。 建议将用户定向到帐户个人资料或配置页。

以下部分将指导你完成构建登陆页的过程：

1. 为登陆页[创建 Azure AD 应用注册](#create-an-azure-ad-app-registration)。
1. [使用代码示例作为应用的起点](#use-a-code-sample-as-a-starting-point)。
1. [使用两个 Azure AD 应用来提高生产安全性](#use-two-azure-ad-apps-to-improve-security-in-production)。
1. [解析由商业市场添加到 URL 的市场购买标识令牌](#resolve-the-marketplace-purchase-identification-token)。
1. [从 ID 令牌中编码的声明中读取信息](#read-information-from-claims-encoded-in-the-id-token)，该令牌是登录后从 Azure AD 接收的，随请求一起发送。
1. 根据需要[使用 Microsoft Graph API](#use-the-microsoft-graph-api) 收集其他信息。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

商业市场与 Azure AD 完全集成。 买家进入通过 [Azure AD 帐户或 Microsoft 帐户 (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology) 进行身份验证的市场。 购买后，买家将从商业市场转到你的登陆页 URL，以激活和管理 SaaS 应用程序的订阅。 必须让买家使用 Azure AD SSO 登录到你的应用程序。 （登陆页 URL 在产品/服务的[技术配置](plan-saas-offer.md#technical-information)页中指定。）

使用标识的第一步是确保登陆页注册为 Azure AD 应用程序。 注册应用程序后，可以使用 Azure AD 对用户进行身份验证并请求访问用户资源。 此操作可被视为应用程序的定义，可让服务知道如何根据应用的设置向应用颁发令牌。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 门户注册新应用程序

若要开始，请按照有关[注册新应用程序](../active-directory/develop/quickstart-register-app.md)的说明操作。 若要让其他公司的用户访问该应用，必须在系统询问谁可以使用该应用程序时选择一个多租户选项。

如果你打算查询 Microsoft Graph API，请[配置新应用程序以访问 Web API](../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 当你为此应用程序选择 API 权限时，默认值 **User.Read** 足以收集有关买家的基本信息，从而顺畅地自动完成加入过程。 请勿请求任何标有“需要管理员同意”的 API 权限，否则会阻止所有非管理员用户访问登陆页。

如果在执行加入或预配的过程中需要提升的权限，请考虑使用 Azure AD 的[增量同意](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md)功能，以便发送自市场的所有买家都能够与登陆页进行初始交互。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用代码示例作为起点

我们提供了多个示例应用，可实现一个启用了 Azure AD 登录的简单网站。 在 Azure AD 中注册应用程序之后，“快速入门”边栏选项卡将提供常用应用程序类型和开发堆栈的列表，如图 1 所示。 选择与环境匹配的项，并按照说明进行下载和设置。

***图 1：Azure 门户中的“快速入门”边栏选项卡***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="演示 Azure 门户中的“快速入门”边栏选项卡。":::

下载代码并设置开发环境后，在应用中更改配置设置，以反映你在前面的过程中记录的应用程序 ID、租户 ID 和客户端机密。 请注意，具体步骤根据所用的示例而异。

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>使用两个 Azure AD 应用来提高生产安全性

本文提供了用来为商业市场 SaaS 产品/服务实现登陆页的体系结构的简化版本。 在生产环境中运行该页时，我们建议提高安全性，具体做法是仅通过另一个受保护的应用程序来与 SaaS 履行 API 通信。 这需要创建两个新应用程序：

- 第一个应用程序是前面所述的多租户登陆页应用程序，只不过其中不包括用来与 SaaS 履行 API 通信的功能。 此功能将卸载到另一应用程序，如下所述。
- 第二个应用程序是负责与 SaaS 履行 API 通信的应用程序。 此应用程序应是仅供你的组织使用的单租户，可以建立一个访问控制列表，以便仅限从此应用对 API 进行访问。

这样，该解决方案便可以在遵守[关注点分离](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns)原则的方案中运行。 例如，登陆页使用注册的第一个 Azure AD 应用将用户登录。 在用户登录后，登陆页使用第二个 Azure AD 来请求访问令牌，以调用 SaaS 履行 API 并调用解析操作。

## <a name="resolve-the-marketplace-purchase-identification-token"></a>解析市场购买标识令牌

将买家定向到登陆页后，会在 URL 参数中添加一个令牌。 此令牌不同于 Azure AD 颁发的令牌以及用于服务间身份验证的访问令牌，它用作输入，供 [SaaS 履行 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 解析调用以获取订阅的详细信息。 与对 SaaS 履行 API 发出的所有调用一样，服务到服务的请求将通过一个访问令牌进行身份验证，该访问令牌基于应用的用于服务间身份验证的 Azure AD 应用程序 ID 用户。

> [!NOTE]
> 大多数情况下，最好从第二个单租户应用程序发出此调用。 请参阅本文前面所述的[使用两个 Azure AD 应用提高生产安全性](#use-two-azure-ad-apps-to-improve-security-in-production)。

### <a name="request-an-access-token"></a>请求访问令牌

若要使用 SaaS 履行 API 对应用程序进行身份验证，需要一个可以通过调用 Azure AD OAuth 终结点生成的访问令牌。 请参阅[如何获取发布者的授权令牌](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)。

### <a name="call-the-resolve-endpoint"></a>调用解析终结点

SaaS 履行 API 实现[解析](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)终结点，可以调用该终结点来确认市场令牌的有效性并返回有关订阅的信息。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>从 ID 令牌中编码的声明读取信息

作为 [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) 流的一部分，在将买家定向到登陆页时，Azure AD 会在请求中添加一个 [ID 令牌](../active-directory/develop/id-tokens.md)。 该令牌包含激活过程中可能用到的多条基本信息，包括下表中显示的信息。

| 值 | 说明 |
| ------------ | ------------- |
| aud | 此令牌的目标受众。 在本例中，它应与应用程序 ID 匹配并经过验证。 |
| preferred_username | 进行访问的用户的主用户名。 可以是电子邮件地址、电话号码或其他标识符。 |
| 电子邮件 | 用户的电子邮件地址。 请注意，此字段可为空。 |
| name | 用户可读值，用于标识令牌使用者。 在本例中，它是买家的姓名。 |
| oid | Microsoft 标识系统中的标识符，用于在不同的应用程序中唯一标识用户。 Microsoft Graph 将返回此值作为给定用户帐户的 ID 属性。 |
| tid | 表示买家所属 Azure AD 租户的标识符。 对于 MSA 标识，此值始终为 ``9188040d-6c67-4c5b-b112-36a304b66dad``。 有关详细信息，请参阅下一部分“使用 Microsoft Graph API”中的说明。 |
| sub | 唯一标识此特定应用程序中的用户的标识符。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

ID 令牌包含用于标识买家的基本信息，但你的激活过程可能需要其他详细信息（例如买家的公司）才能完成加入过程。 使用 [Microsoft Graph API](/graph/use-the-api) 请求这些信息，以避免强制用户再次输入这些详细信息。 默认情况下，标准的 **User.Read** 权限包含以下信息。

| 值 | 说明 |
| ------------ | ------------- |
| displayName | 用户通讯簿中显示的名称。 |
| givenName | 用户的名字。 |
| jobTitle | 用户的职务。 |
| mail | 用户的 SMTP 地址。 |
| mobilePhone | 用户的主要手机号码。 |
| preferredLanguage | 用户首选语言的 ISO 639-1 代码。 |
| surname | 用户的姓氏。 |
|||

可以选择在请求中包含其他属性，例如用户公司的名称，或用户的位置（国家/地区）。 有关更多详细信息，请参阅[用户资源类型的属性](/graph/api/resources/user#properties)。

在 Azure AD 中注册的大多数应用都授予从其公司的 Azure AD 租户读取用户信息的委托权限。 向 Microsoft Graph 发出针对该信息的任何请求都必须附带一个访问令牌用于身份验证。 生成访问令牌的具体步骤取决于所用的技术堆栈，但示例代码将包含示例。 有关详细信息，请参阅[代表用户获取访问权限](/graph/auth-v2-user)。

> [!NOTE]
> 来自 MSA 租户（租户 ID 为 ``9188040d-6c67-4c5b-b112-36a304b66dad``）的帐户返回的信息不会超过已使用 ID 令牌收集的信息。 因此你可以跳过对这些帐户进行的 Graph API 调用。

## <a name="next-steps"></a>后续步骤

- [如何在商业市场中创建 SaaS 产品/服务](create-new-saas-offer.md)