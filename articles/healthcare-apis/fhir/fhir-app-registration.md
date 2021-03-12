---
title: 为适用于 FHIR 的 Azure API 注册 Azure Active Directory 应用
description: 本教程介绍了需要为 azure 的 Azure API 注册哪些应用程序 FHIR 和 FHIR 服务器。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: b01f65b6be31007cebc7a5b03a1c57f6b901fe53
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017893"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>为适用于 FHIR 的 Azure API 注册 Azure Active Directory 应用

设置适用于 FHIR 的 Azure API 或 Azure (OSS) 的 FHIR 服务器时，有几个配置选项可供选择。 对于开源，你需要创建自己的资源应用程序注册。 对于 FHIR 的 Azure API，会自动创建此资源应用程序。

## <a name="application-registrations"></a>应用程序注册

为了使应用程序能够与 Azure AD 进行交互，需要对其进行注册。 在 FHIR 服务器的上下文中，有两种类型的应用程序注册要讨论：

1. 资源应用程序注册。
1. 客户端应用程序注册。

**资源应用程序** 是 AZURE AD 的 API 或资源的表示形式，它是使用 Azure AD 保护的，尤其是 Azure API for FHIR。 预配服务时，将自动创建用于 FHIR 的 Azure API 的资源应用程序，但如果使用的是开源服务器，则需要在 Azure AD 中 [注册资源应用程序](register-resource-azure-ad-client-app.md) 。 此资源应用程序将具有标识符 URI。 建议将此 URI 与 FHIR 服务器的 URI 相同。 此 URI 应用作 `Audience` FHIR 服务器的。 在请求令牌时，客户端应用程序可以请求访问此 FHIR 服务器。

*客户端应用程序* 是将请求令牌的客户端的注册。 通常在 OAuth 2.0 中，我们在至少三种不同类型的应用程序之间进行区分：

1. **机密客户端**（在 Azure AD 中也称为 web 应用）。 机密客户端是使用 [授权代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) 的应用程序，代表登录用户获取有效凭据。 它们被称为机密客户端，因为它们能够保存机密，并将在交换令牌的身份验证代码时提供此机密 Azure AD。 由于机密客户端能够使用客户端密码对其自身进行身份验证，因此它们的信任比公共客户端更多，并且可以具有更长的生存期令牌并被授予刷新令牌。 阅读有关如何 [注册机密客户端](register-confidential-azure-ad-client-app.md)的详细信息。 请注意，必须注册客户端将在其上接收授权代码的回复 url。
1. **公共客户端**。 这些是不能保留机密的客户端。 通常，这会是一个移动设备应用程序或一个页面 JavaScript 应用程序，用户可以在其中发现客户端的机密。 公共客户端也使用授权代码流，但在获取令牌时，它们不允许提供机密，它们可能具有较短的生存期令牌和刷新令牌。 阅读有关如何 [注册公用客户端](register-public-azure-ad-client-app.md)的详细信息。
1. 服务客户端。 这些客户端使用 [客户端凭据流](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)，代表用户)  (代表自己获取令牌。 它们通常表示以非交互式方式访问 FHIR 服务器的应用程序。 例如引入过程。 使用服务客户端时，无需启动使用终结点调用获取令牌的过程 `/authorize` 。 服务客户端可以直接转到 `/token` 终结点，并提供客户端 ID 和客户端机密来获取令牌。 阅读有关如何[注册服务客户端](register-service-azure-ad-client-app.md)的详细信息

## <a name="next-steps"></a>后续步骤

在此概述中，你已完成了在使用 FHIR API 时可能需要的应用程序注册类型。

根据你的设置，请参阅如何注册你的应用程序

* [注册资源应用程序](register-resource-azure-ad-client-app.md)
* [注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)
* [注册公共客户端应用程序](register-public-azure-ad-client-app.md)
* [注册服务应用程序](register-service-azure-ad-client-app.md)

注册应用程序后，即可部署用于 FHIR 的 Azure API。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)